# Lab: <lab-name>

## Date
YYYY-MM-DD

## Objective
- Short objective(s) of the lab

## Scope & Environment
- Repo used: terragoat (link)
- AWS account / sandbox: <profile>
- Terraform version: X.Y.Z

## Pre-reqs
- Tools installed: terraform, awscli, git, checkov, tfsec
- IAM/profile used: ...

## Steps performed
1. Step 1: ...
2. Step 2: ...

## Commands executed (save to commands.log)
\`\`\`bash
# paste commands you ran
\`\`\`

## Artifacts / Evidence
- IP: xxx
- Credentials: shown in outputs

### Screenshots
![screenshot1](screenshots/20251205-120001-terraform-plan.png)

## Findings (vulnerabilities / configs)
- S3 bucket public
- Security group wide-open port 22

## Mitigation / Remediation
- Fix 1: change ACL / add bucket policy
- Fix 2: restrict SG to management IP

## Lessons learned
- Short notes

## Next steps
- Write detection rule
- Run Checkov scan 
