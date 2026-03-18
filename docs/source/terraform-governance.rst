Q. How you run Terraform in prod (modules/state/approvals/drift)
================================================================

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

How I Run Terraform in Production
=================================

In my experience across AWS environments at OpenText and Diehl Metering, Terraform is not just used as a tool, but as the standard way to manage infrastructure in production.

I follow a simple principle: no direct changes in AWS — everything goes through Terraform.

Modules and Structure
---------------------
I use modular Terraform design to keep things reusable and consistent.

- Separate modules for networking, IAM, compute, etc.
- Environments split into ``dev``, ``staging``, and ``prod``
- Common naming and tagging across resources

This helps in scaling across multiple accounts and keeping configurations predictable.

State Management
----------------
For production, I always use remote state.

- S3 bucket for storing Terraform state
- DynamoDB for state locking
- Access restricted via IAM

This avoids conflicts and ensures only controlled changes happen.

Approvals and Workflow
----------------------
All Terraform changes go through Git and CI/CD.

Day-to-day flow:
- Raise a merge request
- Pipeline runs ``terraform plan``
- Plan is reviewed
- Production apply is manually approved

No one applies changes directly from their laptop to production.

This gives control, auditability, and reduces risk.

Drift Detection (Day-to-Day)
----------------------------
In real environments, drift happens — someone changes something in AWS console, or during incidents.

To handle this:

- I run regular ``terraform plan`` checks via pipeline
- If drift is detected:
  - I check what changed (CloudTrail helps)
  - Decide if the change is valid or not

Then:
- If valid → update Terraform code
- If not → revert using Terraform

This keeps infrastructure aligned with code.

Bringing Manual Resources into Terraform
----------------------------------------
Sometimes resources are created manually (especially in urgent situations).

If they need to stay, I bring them under Terraform control:

- First write the Terraform resource block
- Then import the resource into state

Example:

.. code-block:: bash

   terraform import aws_s3_bucket.logs my-bucket-name

After that:
- Run ``terraform plan``
- Fix differences until code matches actual resource

Important: I don’t blindly import — I first check if the resource is correct and secure.

Policy Checks (OPA / Rego)
--------------------------
To prevent bad changes, I use policy checks in pipeline.

For example, I block:

- Public access to resources
- Over-permissive IAM policies
- Missing required tags

Simple example:

.. code-block:: rego

   deny[msg] {
     input.resource_changes[_].type == "aws_security_group"
     input.resource_changes[_].change.after.ingress[_].cidr_blocks[_] == "0.0.0.0/0"
     msg := "Open access is not allowed"
   }

If policy fails, pipeline fails — change doesn’t go to production.

Security Scanning (Checkov)
---------------------------
I also scan Terraform code before apply.

Example GitLab stage:

.. code-block:: yaml

   checkov_scan:
     stage: security
     image: bridgecrew/checkov
     script:
       - checkov -d .

This helps catch:
- Public S3 buckets
- Missing encryption
- Weak configurations

Summary
-------
In production, my focus is:

- No manual changes
- Everything through Terraform + CI/CD
- Strong state management
- Controlled approvals
- Drift detection and correction
- Policy and security checks before apply

This approach has helped me manage AWS environments reliably at scale across multiple accounts while keeping security and control in place.
