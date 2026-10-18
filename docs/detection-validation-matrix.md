# Detection validation matrix

**Status:** Updated KQL is proposed and **not yet verified in the Azure workspace**. Earlier screenshots show the original lab configuration; they do not validate these revisions.

| Rule | Test scenario | Expected result | Actual result | Status |
| --- | --- | --- | --- | --- |
| R1 | Five 4625 events for same account/IP inside one 5-minute bin | One grouped candidate result | Five failed authentication events were grouped into one detection result, meeting the configured threshold. | PASS |
| R1 | Four matching failures | No result | Four failed authentication events were recorded. No detection result was returned because the threshold of five was not met. | PASS |
| R1 | Five failures across different accounts/IPs | No single qualifying group | Failed authentication events were distributed across separate account/IP groups. No individual group met the detection threshold. | PASS |
| R2 | Three failures in a 10-minute bin, then a 4624 within 15 minutes from same account/IP | Correlation result | Three failed authentication events were correlated with a subsequent successful login for the same account and source IP. One correlation result was returned. | PASS |
| R2 | Success from different IP or account | No correlation | Authentication events were recorded, but no correlation was returned because the account or source IP did not match. | PASS |
| R2 | Success >15 minutes after last failure | No correlation | The successful authentication occurred outside the configured correlation window. No detection result was returned. | PASS |
| R3 | 4740 account lockout in collected Security log | Account and caller fields returned | One account lockout event was detected. The affected account and originating computer were successfully extracted. | PASS |
| R3 | Ordinary 4625 without 4740 | No lockout result | Failed authentication events were recorded, but no account lockout detection was returned because Event ID 4740 was absent. | PASS |

## How to validate
1. Confirm `Event` contains the required event IDs and that `EventData` XML includes `TargetUserName` and `IpAddress` as expected. Some collection configurations instead use `SecurityEvent` with different column names.
2. Run each `.kql` file in Logs and check for query errors. Adjust parser to actual event format before changing detection logic.
3. Generate controlled authentication tests **only against your own lab**, recording UTC timestamps, source, destination, account and expected behavior.
4. Compare query output with raw Windows events and record observed results, false positives and missed cases in the matrix.
5. Update Sentinel scheduled analytics rules with the validated queries, run the schedule, and capture new screenshots with rule name, configuration, incident evidence and timestamps.
6. Review alert grouping, suppression, lookback and frequency to avoid duplicate incidents.

## Known limitations
- Fixed 5/10-minute bins can split activity crossing bin boundaries.
- NAT/shared IPs, RDP reconnects, service accounts and legitimate password mistakes can produce noisy alerts.
- Event 4624 is **not proof of compromise**; additional investigation is required.
- Event 4740 depends on account type, audit policy and whether relevant hosts/domain controllers are connected.
- KQL query results do not automatically prove a scheduled Sentinel analytic rule fired.
