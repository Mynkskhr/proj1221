Q. Last IAM change you led that reduced risk (what + why)
======================================================

In my previous roles at OpenText and Diehl Metering, I worked on AWS environments operating in a multi-account setup using landing zones, Service Control Policies (SCPs), and IAM role-based access.

During an IAM audit, I identified several high-risk patterns:

- Long-lived IAM users with static access keys
- Access keys not rotated or encrypted
- Over-permissive policies (e.g. ``*:*``)
- Application teams sharing credentials
- Lack of enforcement of least-privilege access
- Inconsistent IAM practices across accounts

These issues increased the risk of credential compromise, privilege escalation, and lack of traceability.

Actions Taken
-------------
- Migrated access model from IAM users to IAM roles using STS-based temporary credentials
- Enforced role-based access for applications and engineers
- Designed IAM roles aligned to job functions and service boundaries

- Identified and remediated access key risks:
  - Flagged access keys older than 90 days
  - Detected unencrypted or unused keys
  - Implemented automated remediation using:
    - AWS Lambda
    - EventBridge triggers
    - Step Functions for controlled workflows
  - Actions included deactivation or deletion of non-compliant keys

- Prevented creation of over-permissive policies:
  - Implemented guardrails aligned with security posture
  - Used automated checks to detect ``*:*`` patterns
  - Enforced policy review before deployment

- Addressed credential sharing:
  - Shifted application teams to role-based access
  - Removed dependency on shared credentials
  - Reinforced shared responsibility model

- Standardised IAM via Terraform:
  - Introduced reusable IAM modules
  - Defined a standard operating process (SOP) for IAM changes
  - Ensured consistent policy structure and naming conventions across environments

Controls Implemented
-------------------
- Least-privilege IAM policies per service and role
- SCP guardrails to restrict high-risk actions at account level
- Automated detection and remediation of non-compliant access keys
- Role-based access with temporary credentials (STS)
- Audit visibility through CloudTrail and logging

Outcome
-------
- Eliminated long-lived credentials across critical workloads
- Reduced risk of credential leakage and misuse
- Prevented creation of overly permissive IAM policies
- Improved consistency of IAM practices across multiple AWS accounts
- Strengthened overall security posture and audit readiness
