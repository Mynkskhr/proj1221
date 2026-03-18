Q. CI/CD protections you enforce (specific controls) (GitLab CI/CD)
===================================================================

In my experience, CI/CD is the primary control layer for production.

Using GitLab Ultimate, I enforce CI/CD as a governed system with built-in security, approval, and deployment controls. No change reaches production unless it passes all enforced gates.

Source Control Controls
-----------------------
- Protected ``main`` / ``production`` branches  
- No direct commits allowed  
- Mandatory merge requests for all changes  
- Required approvals before merge (minimum reviewers enforced)  
- Code Owners defined for sensitive areas (e.g. Terraform, IAM)  

This ensures that all changes are reviewed and ownership is clearly enforced.

Pipeline Enforcement Controls
-----------------------------
- Pipelines are mandatory — no manual bypass  
- All changes trigger pipelines automatically  
- Pipeline failure blocks merge or deployment  

Standard enforced stages:

- validation (lint, syntax checks)  
- security scanning  
- terraform plan / build  
- policy checks  
- approval gate  
- deployment  

I treat pipelines as guardrails — if any stage fails, the change is stopped.

Example:
A Terraform change once attempted to modify multiple resources unintentionally due to incorrect variables. The ``terraform plan`` stage exposed the issue, and the pipeline blocked it before apply.

Security Controls (GitLab Ultimate Features)
--------------------------------------------
Security is enforced directly in CI/CD using GitLab Ultimate capabilities:

- SAST (Static Application Security Testing)  
- Dependency Scanning (SCA)  
- Container Scanning (e.g. Trivy integration)  
- Secret Detection  
- Terraform scanning (Checkov or similar)  

Critical or high findings block the pipeline.

Example:
A deployment was blocked because a secret was accidentally committed in code — detected via GitLab secret detection before merge.

Approval and Deployment Controls
--------------------------------
- Protected environments for production  
- Manual approval required before production deploy  
- Restricted deploy permissions (only authorised roles)  
- Separation of duties:
  - Developers propose changes  
  - Platform/security team approves production deployment  

This prevents unauthorized or accidental changes reaching production.

Secrets and Access Controls
---------------------------
- No secrets stored in code or repository  
- GitLab protected and masked variables used for pipelines  
- Integration with AWS Secrets Manager / Parameter Store  
- Access controlled via IAM roles and environment scope  

Secrets are never exposed in logs or pipeline output.

Policy and Guardrails
---------------------
I enforce policy-as-code as part of CI/CD controls:

- Block overly permissive IAM policies (``*:*``)  
- Block public exposure (e.g. ``0.0.0.0/0``)  
- Enforce required tags and environment standards  

Policy failures automatically block deployment.

Example:

.. code-block:: rego

   deny[msg] {
     input.resource_changes[_].type == "aws_security_group"
     input.resource_changes[_].change.after.ingress[_].cidr_blocks[_] == "0.0.0.0/0"
     msg := "Open access is not allowed"
   }

Rollback and Recovery Controls
------------------------------
- Version-controlled deployments via Git  
- Ability to redeploy last stable version quickly  
- Terraform rollback using previous commit/state  

Example:
During a failed deployment, we reverted to the previous stable version via pipeline, restoring service without manual intervention.

Audit and Traceability
----------------------
- Full audit trail via merge requests and pipeline logs  
- Visibility into:
  - who made the change  
  - what was deployed  
  - when it was deployed  

This supports both operational debugging and compliance requirements.

Summary
-------
I enforce CI/CD as a controlled system with strict protections:

- No direct production changes  
- Mandatory pipelines with enforced stages  
- Built-in security scanning (GitLab Ultimate)  
- Strong approval and deployment controls  
- Policy-based guardrails  
- Full auditability and rollback capability  

This ensures production deployments are secure, controlled, and repeatable across AWS environments.
