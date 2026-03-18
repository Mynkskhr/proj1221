Q. Last restore test you ran (what failed, what changed after)
==============================================================

Context
-------
Backups existed, but restore procedures were not tested, creating uncertainty around actual recoverability.

Objective
---------
Validate RPO/RTO assumptions and ensure recovery processes are reliable and repeatable.

Scenario Tested
---------------
Full restore of an RDS-backed application environment.

Findings
--------
- Restore time exceeded expected RTO
- Application configuration was partially hardcoded
- Manual steps required for environment reconstruction

Actions Taken
-------------
- Automated restore workflow:
  - Infrastructure recreated using Terraform
  - Database restored from latest snapshot
- Externalised configuration:
  - Environment variables
  - AWS Systems Manager Parameter Store
- Removed hardcoded dependencies
- Created step-by-step recovery runbook

Runbook Includes
----------------
1. Identify recovery point
2. Restore infrastructure (Terraform)
3. Restore database snapshot
4. Update application configuration
5. Validate service health

Outcome
-------
- Reduced recovery time significantly
- Ensured repeatable and testable DR process
- Increased confidence in production resilience
