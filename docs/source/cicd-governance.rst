Q. CI/CD protections you enforce (specific controls) (GitLab CI/CD)
===================================================================

In my experience, CI/CD is not just for automation. it is the main control point for production changes.

Across my work at Xebia, OpenText and Diehl Metering, I have used GitLab CI/CD to enforce governance, security, and safe deployments.

Source Control Protection
--------------------------
- Protected ``main`` / ``production`` branches
- No direct commits allowed
- Mandatory merge requests for all changes
- Required code reviews before merge

This ensures all changes are visible and reviewed before reaching production.

Pipeline Controls
------------------
Every change goes through a controlled pipeline.

Typical stages:
- validation (lint, syntax checks)
- security scanning
- terraform plan / build step
- approval gate
- deployment

No change skips the pipeline.

Deployment Controls
-------------------
- Production deployments are restricted
- Only authorized users can trigger production jobs
- Manual approval required before deploy
- Separate environments (dev, staging, prod)

This prevents accidental or unauthorized releases.

Secrets Management
-------------------
- No secrets stored in code or Git
- Secrets managed via:
  - GitLab CI variables (protected & masked)
  - AWS Secrets Manager / Parameter Store
- Access controlled via IAM roles

This avoids exposure of sensitive data in pipelines.

Security Enforcement
--------------------
Security checks are part of the pipeline:

- SAST / dependency scanning
- Container/image scanning (Trivy or similar)
- Terraform scanning (Checkov)

If a critical issue is found, pipeline fails.

Policy and Approval Gates
--------------------------
- Approval required before production deploy
- Restricted approvers (senior engineers / platform team)
- Optional policy checks (e.g. blocking risky configurations)

This adds a governance layer on top of automation.

Rollback Strategy
-----------------
- Versioned deployments
- Ability to redeploy last stable version
- In some cases, automated rollback on failure

This ensures production stability during releases.

Audit and Visibility
--------------------
- All pipeline runs are logged
- Merge requests provide full change history
- Easy to trace:
  - who changed what
  - when it was deployed

Summary
-------
My approach to CI/CD in GitLab is:

- No direct changes to production
- Everything goes through pipeline + review
- Strong access control and approvals
- Security checks built into pipeline
- Clear rollback and audit trail

This ensures safe, controlled, and repeatable production deployments.
