# ✅ **S3 Terraform Security Checklist (Fast Audit)**

### *A quick way to determine if an S3 bucket configuration is secure or vulnerable.*

---

# **1. Encryption (MUST)**

### **Check if SSE is enabled:**

```hcl
server_side_encryption_configuration { ... }
```

### MUST include:

* `sse_algorithm = "aws:kms"` (preferred)
* OR `AES256` (acceptable but weaker)

### ❌ Vulnerability if:

* No SSE block at all
* Using legacy insecure AES patterns
* Using KMS but with overly permissive key policies

**Why:** Prevents at-rest data exposure & meets compliance.

---

#  **2. Versioning (Strongly Recommended)**

### Look for:

```hcl
versioning {
  enabled = true
}
```

### ❌ Vulnerability if:

* Versioning is missing
* Versioning is disabled

**Why:** Protects against accidental deletion, ransomware, malicious overwrites.

---

#  **3. Access Logging (For Monitoring & IR)**

### Look for:

```hcl
logging {
  target_bucket = aws_s3_bucket.logs.id
  target_prefix = "something/"
}
```

### ❌ Vulnerability if:

* No logging block
* Logging bucket doesn’t exist
* Logging bucket isn’t versioned or encrypted

**Why:** Enables forensic investigation & anomaly detection.

---

#  **4. Block Public Access (Critical)**

### Look for:

```hcl
resource "aws_s3_bucket_public_access_block" "x" {
  bucket = aws_s3_bucket.<bucket>.id
  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}
```

### ❌ Vulnerability if:

* The resource is missing entirely
* Any of these values are `false`

### Bonus check:

If ACL is set to anything other than `private`, **investigate deeply**.

**Why:** Prevents accidental public exposure & catastrophic data leaks.

---

#  **5. Bucket ACLs (Never Public)**

### Look for:

```hcl
acl = "private"
```

### ❌ Vulnerability if:

* `public-read`
* `public-read-write`
* `log-delivery-write` (only valid for log buckets)
* Missing ACL but bucket policy allows public access

**Why:** Public ACLs are one of the top AWS breach causes.

---

#  **6. Secure Bucket Policies**

### Look for:

* No `Principal = "*"` unless very controlled
* No bucket policies allowing `s3:GetObject` to public
* No wildcard `"Action": "s3:*"` unless needed

### ❌ Vulnerability if:

* Policy grants `s3:*` to everyone
* Policy allows uploads from untrusted IAM roles
* Policy bypasses bucket public access block

**Why:** Policy misconfigs override ACLs and cause breaches.

---

#  **7. Object Upload Safety**

### Check `aws_s3_bucket_object`:

* Are sensitive files being uploaded?
* Does the bucket have encryption?
* Does the bucket have logging?

**Immediate red flag:**
Uploading `customer.xlsx`, `keys.json`, `.pem`, `.log` to insecure buckets.

---

#  **8. `force_destroy = true` (Dangerous)**

### Look for:

```hcl
force_destroy = true
```

### ❌ Vulnerability if:

* Used in production
* Used on buckets with sensitive data or logs

**Why:** Deletes bucket even when objects exist → complete data loss.

---

#  **9. Missing Lifecycle Policies (Optional but recommended)**

Check for:

```hcl
lifecycle_rule { ... }
```

Recommended to:

* Transition to infrequent access
* Delete old versions
* Optimize cost
* Prevent bucket bloat

Not always a security issue, but useful.

---

#  **10. KMS Key Hardening (Advanced)**

If bucket uses SSE-KMS, check:

### **Key policy should NOT allow:**

```json
"Principal": "*"
```

### Should restrict:

* `kms:Decrypt` to specific roles
* Allow `kms:GenerateDataKey` only for necessary services

**Why:** You can encrypt securely and still leak if KMS key is open.

---

#  **11. Sensitive Data Classification**

Look for object names or directories like:

* `customer-master.xlsx`
* `financials/`
* `invoices/`
* `logs/`
* `datasets/`

Ensure:

* Versioning is ON
* SSE-KMS is ON
* Public access is BLOCKED

This is where real DFIR intuition comes in.

---

#  **12. Cross-Account Access Review**

Check bucket policy for:

```json
"Principal": {
  "AWS": "<another account>"
}
```

Ensure:

* The other account is trusted
* sts:AssumeRole is used correctly
* No overly broad cross-account permissions

This is a major source of real-world compromise.

---

# ⚡ **13. S3 Event Notifications (Optional)**

If present:

* Ensure events don't notify unknown/unauthenticated endpoints
* Check for Lambda triggers that could expose data

---

#  **14. Public Exposure Quick Test (IaC Mental Shortcut)**

Ask yourself:

> *Can anyone on the internet do `curl https://bucket.s3.amazonaws.com/file`?*

If yes → Critical vulnerability.

---

#  **15. The Three Checks for Instant S3 Health (The “Green Signal”)**

If a bucket has:

✔ SSE-KMS
✔ Versioning
✔ Public Access Block

Then **90% of S3 risks are already mitigated**.

---

#  Summary — Your S3 Fast-Audit Checklist

If a bucket is missing any of these three:

❌ Encryption
❌ Logging
❌ Versioning

Or if either of these two exist:

❌ Public access
❌ Dangerous bucket policy

→ **It is automatically considered vulnerable.**

This is how security architects instantly classify S3 findings.

---
