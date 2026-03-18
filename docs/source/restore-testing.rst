Q. Last restore test you ran (what failed, what changed after)
==============================================================

Across my experience from IBM to AWS environments at OpenText and Diehl Metering, I treat disaster recovery as something that must be tested in real scenarios, not just designed.

One practical restore scenario I handled was in a pre-production AWS environment where an incorrect Terraform change was merged by an apprentice, which unintentionally overrode parts of the infrastructure and caused a major disruption.

Restore Scenario
----------------
- Pre-production AWS environment managed via Terraform  
- Incorrect PR merge led to unintended infrastructure changes  
- Required restoring environment to last known stable state  

What Failed
-----------
- Infrastructure state was impacted due to incorrect Terraform apply  
- Some resources were modified or recreated unexpectedly  
- Dependency issues appeared between services after the change  

Actions Taken
-------------
- Identified last known good Terraform state from remote backend (S3)  
- Reverted Terraform code to previous stable version  
- Re-ran Terraform apply to bring infrastructure back to expected state  

- Validated services after restore:
  - Checked application connectivity  
  - Verified dependent services and configurations  

- Analysed root cause of failure:
  - Lack of strict validation before merge  
  - Insufficient guardrails for production-like environments  

What Changed After
-------------------
- Introduced stricter PR review and approval process  
- Added validation checks in CI/CD before Terraform apply  
- Restricted who can approve infrastructure changes  
- Improved separation between environments (pre-prod vs prod controls)  

- Strengthened Terraform workflow:
  - Better plan review visibility  
  - Clear rollback approach using previous state  

Outcome
-------
- Successfully restored infrastructure to stable state  
- Reduced risk of similar issues through stronger controls  
- Improved confidence in recovery using Terraform state  

This reinforced the importance of treating Terraform state and version control as a recovery mechanism, not just a deployment tool.
