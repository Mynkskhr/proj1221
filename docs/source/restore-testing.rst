Q. Last restore test you ran (what failed, what changed after)
==============================================================

Across my experience, starting from IBM and continuing through enterprise AWS environments (OpenText, Diehl Metering), I have consistently treated disaster recovery as an operational responsibility rather than a theoretical setup.

In IBM, I worked in a hybrid DR model between AWS and on-premise datacenter environments, where recovery required coordination between cloud services and physical infrastructure teams. This established a strong foundation in understanding real-world recovery dependencies.

In later AWS environments, I worked with multi-region, high-availability platforms supporting enterprise workloads with strict SLA and compliance requirements.


Restore Scenario
----------------
- Multi-region AWS production workload
- Recovery of application stack backed by RDS and supporting infrastructure
- Validation through controlled restore / tabletop exercises simulating region-level or service-level failures

Findings
--------
During restore validation and tabletop exercises, the following gaps were identified:

- Recovery time exceeded expected RTO due to manual dependencies
- Infrastructure rebuild required implicit knowledge (not fully automated)
- Application configurations were environment-dependent and not fully externalised
- Coordination gaps between teams (application vs infrastructure)

Actions Taken
-------------
- Improved infrastructure recoverability:
  - Standardised infrastructure recreation using Terraform
  - Ensured reproducible environments across regions

- Reduced manual dependencies:
  - Defined clear recovery workflows
  - Eliminated ad-hoc steps in restoration process

- Externalised application configuration:
  - Moved configurations to environment variables and parameter management systems
  - Reduced dependency on hardcoded values

- Introduced structured DR validation approach:
  - Conducted tabletop exercises to simulate failure scenarios
  - Ensured teams understood recovery responsibilities and sequence

- Created and refined recovery runbooks:
  - Step-by-step recovery procedures
  - Defined ownership and escalation paths

RTO / RPO Understanding in Practice
----------------------------------
- RTO (Recovery Time Objective):
  Focused on reducing recovery time by automating infrastructure rebuild and minimising manual intervention.

- RPO (Recovery Point Objective):
  Ensured backup frequency and replication strategies aligned with acceptable data loss thresholds.

These were validated not just through configuration, but through actual restore and simulation exercises.

Outcome
-------
- Reduced recovery time through automation and standardisation
- Improved confidence in disaster recovery readiness across teams
- Eliminated hidden dependencies in recovery workflows
- Established repeatable and testable DR processes
- Strengthened platform resilience for enterprise workloads
