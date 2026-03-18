Terraform Governance – Production Infrastructure Management
===========================================================

Structure
---------
- Modular architecture:
  - Reusable modules for networking, compute, IAM, storage
- Environment separation:
  - ``dev``, ``staging``, ``prod``

State Management
----------------
- Remote backend:
  - S3 bucket for state storage
  - DynamoDB table for state locking
- Restricted access via IAM policies

Workflow
--------
1. Changes proposed via Git pull requests
2. ``terraform plan`` generated automatically
3. Plan reviewed before approval
4. Manual approval required for production apply

Controls
--------
- No direct ``terraform apply`` in production
- All changes go through CI/CD pipeline
- Version-controlled infrastructure definitions

Drift Detection
---------------
- Scheduled ``terraform plan`` runs
- Alerts on detected drift
- Remediation via controlled apply process

Outcome
-------
- Predictable and auditable infrastructure changes
- Reduced risk of configuration drift
- Strong separation between environments
