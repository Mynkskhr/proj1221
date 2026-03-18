IAM Risk Reduction – Role-Based Access Migration
================================================

Context
-------
The environment relied heavily on long-lived IAM user credentials and overly permissive policies (e.g. wildcard ``*:*`` actions), increasing the risk of credential compromise and lateral movement.

Objective
---------
Reduce identity-related risk by enforcing least privilege and eliminating long-lived credentials.

Actions Taken
-------------
- Conducted IAM audit using AWS IAM Access Analyzer and CloudTrail logs
- Identified:
  - Over-permissive policies
  - Unused credentials
  - Inline policies without governance
- Migrated from IAM users to IAM roles with STS-based temporary credentials
- Replaced inline policies with scoped managed policies
- Enforced:
  - MFA for privileged access
  - Role assumption patterns (no direct access to production resources)
- Introduced naming conventions and role segmentation:
  - ``app-role-*``
  - ``infra-role-*``
  - ``readonly-*``

Controls Implemented
-------------------
- Least privilege policies per service
- Restricted access to sensitive services (IAM, KMS, S3 critical buckets)
- CloudTrail enabled for all regions
- Periodic access reviews

Outcome
-------
- Eliminated long-lived credentials in production
- Reduced blast radius of compromised identities
- Improved auditability and traceability of access patterns
