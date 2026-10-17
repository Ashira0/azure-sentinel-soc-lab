# Azure Sentinel SOC Detection Lab

A hands-on Windows authentication monitoring lab using **Microsoft Sentinel**, Azure Monitor Agent, Data Collection Rules, Log Analytics and KQL. This repository includes original lab screenshots and **revised detection queries awaiting validation**.

> **Implementation status (October 2026):** The screenshots document the earlier lab implementation. The KQL files were subsequently improved to include account/IP grouping, time windows and more explicit correlation. **The revised queries have not been tested or deployed in the workspace yet.** See [validation matrix](docs/detection-validation-matrix.md) and [screenshot checklist](docs/screenshot-update-checklist.md).

## Architecture

Windows VM → Azure Monitor Agent / Data Collection Rule → Log Analytics (`Event`) → Microsoft Sentinel analytics → alerts and incidents.

![Original lab architecture](screenshots/sentinel_soc_architecture.png)

## Detection content

| Rule | Signal | Proposed logic | Status |
| --- | --- | --- | --- |
| [R1: Failed logon burst](kql/rule1_bruteforce_4625.kql) | Windows 4625 | ≥5 failures by account/source IP per 5-minute bin | Revised; validation pending |
| [R2: Failures followed by success](kql/rule2_failed_then_success_4625_4624.kql) | Windows 4625 + 4624 | ≥3 failures per 10-minute bin then same account/IP succeeds within 15 minutes | Revised; validation pending |
| [R3: Account lockout](kql/rule3_account_lockout_4740.kql) | Windows 4740 | Extract account and caller computer for triage | Revised; validation pending |

Thresholds are starting assumptions for this lab, not production baselines. A 4624 success after failures is **suspicious context, not proof of compromise**. Event 4740 availability depends on auditing and collection of the relevant host/domain controller.

## Original lab activity

The original lab used simulated RDP authentication failures and successes to explore Windows security events and Sentinel analytics. The existing screenshots capture the original configuration, query output and an example incident. They should **not** be interpreted as evidence of execution of the revised KQL.

## Validation and analyst workflow

- [Detection validation matrix and test plan](docs/detection-validation-matrix.md)
- [SOC incident response guide](docs/incident-response-guide.md)
- [Incident case study template — not a completed investigation](docs/incident-case-study-template.md)
- [Screenshot refresh checklist](docs/screenshot-update-checklist.md)
- [Lab environment notes](docs/lab_environment_details.md)

## Screenshots (original lab, prior to query revisions)

| Evidence | Image |
| --- | --- |
| Rule 1 original configuration | ![Original Rule 1](screenshots/rule1_configuration.png) |
| Rule 2 original configuration | ![Original Rule 2](screenshots/rule2_configuration.png) |
| Rule 3 original query | ![Original Rule 3](screenshots/rule3_query.png) |
| Rule 3 entity mapping | ![Original entity mapping](screenshots/rule3_entity_mapping.png) |
| Original query results | ![Original query output](screenshots/query_detection_results.png) |
| Original Sentinel incident example | ![Original incident example](screenshots/sentinel_incident_example.png) |

## Skills and tooling

Microsoft Sentinel · Azure Monitor Agent · Log Analytics · Windows Security Events · Kusto Query Language (KQL) · authentication event analysis · detection development and documentation.

## Next steps

1. Verify field extraction against collected `EventData` and run each query in the workspace.
2. Record expected vs observed behavior in the validation matrix, including negative tests.
3. Update scheduled analytics rules, entity mapping, alert grouping and suppression.
4. Capture fresh screenshots showing revised configurations and observed incidents.
5. Complete an evidence-backed incident case study using the template.

**Author:** Ashkon Irani
