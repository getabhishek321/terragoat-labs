# **S3 Non-Negotiable Security Baseline (2025)**

### *If any ONE of these is missing → the bucket is insecure.*

---

##  **1. Block ALL Public Access (MANDATORY)**

### MUST exist:

```hcl
resource "aws_s3_bucket_public_access_block" "bucket" {
  bucket = aws_s3_bucket.<name>.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}
```

### ❌ Instantly vulnerable if:

* This resource is missing
* Any value is `false`
* Bucket policy allows public access anyway

### Why non-negotiable:

> **Public S3 exposure is the #1 cause of AWS data breaches.**

No justification exists today for public buckets holding data.

---

## **2. Encryption at Rest (SSE-KMS preferred)**

### MUST exist:

```hcl
server_side_encryption_configuration {
  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "aws:kms"
    }
  }
}
```

### ❌ Instantly vulnerable if:

* No encryption block exists
* Using plaintext storage
* KMS key policy is overly permissive

### Why non-negotiable:

* Required by CIS, SOC2, ISO27001, HIPAA, PCI-DSS
* Prevents catastrophic impact if access is gained
* Zero performance penalty in AWS

> **Unencrypted S3 is indefensible in 2025.**

---

##  **3. Versioning Enabled**

### MUST exist:

```hcl
versioning {
  enabled = true
}
```

### ❌ Instantly vulnerable if:

* Versioning is missing or disabled

### Why non-negotiable:

* Protects against ransomware
* Protects against insider overwrites
* Enables forensic reconstruction
* Required for incident response maturity

> Without versioning, you cannot guarantee data integrity.

---

## **4. ACL MUST Be Private**

### MUST exist:

```hcl
acl = "private"
```

### ❌ Instantly vulnerable if:

* `public-read`
* `public-read-write`
* ACL missing and policy is loose
* ACL contradicts access block

### Why non-negotiable:

ACLs still get misused and override intentions.

> **Private is the only acceptable ACL for data buckets.**

---

##  **5. No Dangerous Bucket Policies**

### MUST NOT exist:

```json
"Principal": "*"
```

OR

```json
"Action": "s3:*"
```

### ❌ Instantly vulnerable if:

* Policy allows public `GetObject`
* Cross-account access is overly broad
* Policy bypasses public access block

### Why non-negotiable:

Bucket policies override everything else.

> One bad policy = total exposure.

---

#  **Strongly Expected (But Technically Optional)**

These are **borderline non-negotiable** in real orgs:

##  **6. Access Logging**

Not always mandatory, but **expected** for:

* sensitive data
* regulated data
* production workloads

Missing logs → **weak IR posture**

---

##  **7. `force_destroy` = false (Production)**

###  Vulnerable if:

* `force_destroy = true` on prod buckets

Why:

* Allows irreversible data loss
* Breaks compliance retention requirements

---

# ⚡ **The 5-Second S3 Security Test (Mental Shortcut)**

Ask yourself:

1️⃣ Is public access blocked?
2️⃣ Is encryption enabled?
3️⃣ Is versioning enabled?
4️⃣ Is ACL private?
5️⃣ Is bucket policy safe?

### If ALL YES → **Bucket passes baseline**

### If ANY NO → **Bucket is vulnerable**

---

#  **Why This Matters for YOU (Career Angle)**

If you internalize this baseline:

* You can instantly review any S3 Terraform file
* You can explain *why* something is insecure (interview gold)
* You can design secure storage by default
* You think like a **cloud security engineer**, not a tool operator

This baseline alone is enough to:

* Pass most AWS security interviews
* Catch 90% of real-world S3 incidents
* Confidently defend security decisions

---

#  Final Truth

> **If a bucket is private, encrypted, versioned, and not publicly accessible — it is already ahead of most production environments.**

Everything else builds on top of this.

