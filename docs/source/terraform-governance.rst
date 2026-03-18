Q. How you run Terraform in prod (modules/state/approvals/drift)
================================================================

In my experience at OpenText and Diehl Metering, Terraform is the single source of truth for infrastructure.

My core principle is simple: no direct changes in AWS — everything goes through Terraform and CI/CD.

Modules and Structure
---------------------
I use a modular structure to make infrastructure reusable and consistent across accounts.

- Separate modules for networking, IAM, compute, and storage  
- Environment separation: ``dev``, ``staging``, ``prod``  
- Standard naming, tagging, and account-level structure  

This allows the same patterns to scale across multi-account environments while keeping configurations predictable.

State Management
----------------
For production, I always use remote state:

- S3 bucket for state storage (with versioning enabled)  
- DynamoDB for state locking  
- IAM-restricted access to state  

This ensures:
- no concurrent changes  
- safe recovery of previous state if needed  
- controlled access to production infrastructure  

Approvals and Workflow
----------------------
All Terraform changes are controlled through GitLab CI/CD.

Typical flow:

- Engineer raises a merge request  
- Pipeline runs validation, security checks, and ``terraform plan``  
- Plan is reviewed before approval  
- Production apply is manually approved  

No one applies Terraform directly from their local machine to production.

Example:
During a change in pre-production, a bad PR caused unintended infrastructure changes. We reverted to the previous commit and re-applied Terraform, restoring the environment quickly.

This shows Terraform is not just for provisioning, but also for controlled rollback.

Drift Detection (Day-to-Day)
----------------------------
Drift happens in real environments, especially during incidents or manual fixes.

My approach:

- Run scheduled ``terraform plan`` checks via pipeline  
- If drift is detected:
  - Check CloudTrail to identify what changed  
  - Validate if change was intentional  

Then:

- If valid → update Terraform code  
- If not → revert infrastructure using Terraform  

Example:
A security group was modified manually during troubleshooting. Drift detection caught it, and we reverted it back through Terraform to maintain consistency.

Bringing Manual Resources into Terraform
----------------------------------------
Sometimes resources are created manually in urgent situations.

If they need to be retained:

- Write Terraform resource definition  
- Import into state  

Example:

.. code-block:: bash

   terraform import aws_s3_bucket.logs my-bucket-name

Then:

- Run ``terraform plan``  
- Adjust code until it matches actual configuration  

Important:
I only import after validating the resource is secure and aligned with standards — I don’t bring unmanaged or risky resources into Terraform blindly.

Policy Checks (OPA / Rego)
--------------------------
To prevent unsafe changes, I enforce policy checks in CI/CD.

Examples of controls:

- Block public access (e.g. ``0.0.0.0/0``)  
- Prevent overly permissive IAM policies  
- Enforce mandatory tags  

Example:

.. code-block:: rego

   deny[msg] {
     input.resource_changes[_].type == "aws_security_group"
     input.resource_changes[_].change.after.ingress[_].cidr_blocks[_] == "0.0.0.0/0"
     msg := "Open access is not allowed"
   }

If policy fails, the pipeline fails — change does not proceed.

Security Scanning (Checkov)
---------------------------
Terraform code is scanned before apply.

Example GitLab stage:

.. code-block:: yaml

   checkov_scan:
     stage: security
     image: bridgecrew/checkov
     script:
       - checkov -d .

This helps detect:

- Public S3 buckets  
- Missing encryption  
- Weak IAM or network configurations  

Summary
-------
In production, I focus on:

- No manual infrastructure changes  
- Terraform as single source of truth  
- Remote state with strict access control  
- CI/CD-driven approvals and deployments  
- Continuous drift detection and correction  
- Policy and security checks before apply  

This approach has helped me run AWS infrastructure reliably across multiple accounts, with strong control, auditability, and quick recovery when things go wrong.
