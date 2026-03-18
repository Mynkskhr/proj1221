Q. Last IAM change you led that reduced risk (what + why)
======================================================

In my roles at OpenText and Diehl Metering, I worked on multi-account AWS environments using landing zones, SCP guardrails, and IAM role-based access.

During an IAM audit, I identified key risks:

- Long-lived IAM users with static access keys  
- Access keys not rotated or encrypted  
- Over-permissive policies (``*:*``)  
- Credential sharing across application teams  
- Inconsistent IAM practices across accounts  

For example, we found production access keys older than 90 days still active and not rotated, which increased the risk of credential compromise.

Actions taken:

- Migrated from IAM users to IAM roles using STS-based temporary credentials  
- Enforced role-based access for applications and engineers  
- Designed IAM roles aligned to job functions and service boundaries  

- Automated access key remediation:
  - Detected non-compliant keys (age, usage, encryption)
  - Used AWS Lambda with EventBridge and Step Functions to deactivate or delete keys  

- Prevented over-permissive access:
  - Introduced guardrails to detect ``*:*`` patterns  
  - Enforced policy review before deployment  

- Removed credential sharing:
  - Shifted teams to role-based access  
  - Reinforced shared responsibility model  

- Standardised IAM using Terraform modules and defined a consistent process for IAM changes  

Outcome:

- Eliminated long-lived credentials in production  
- Reduced identity-related attack surface  
- Prevented creation of overly permissive policies  
- Improved consistency of IAM practices across accounts  
