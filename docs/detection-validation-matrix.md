# Detection validation matrix

**Status:** Updated KQL is proposed and **not yet verified in the Azure workspace**. Earlier screenshots show the original lab configuration; they do not validate these revisions.

| Rule | Test scenario | Expected result | Actual result | Status |
| --- | --- | --- | --- | --- |
| R1 | Five 4625 events for same account/IP inside one 5-minute bin | One grouped candidate result | Not recorded | Pending |
| R1 | Four matching failures | No result | Not recorded | Pending |
| R1 | Five failures across different accounts/IPs | No single qualifying group | Not recorded | Pending |
| R2 | Three failures in a 10-minute bin, then a 4624 within 15 minutes from same account/IP | Correlation result | Not recorded | Pending |
| R2 | Success from different IP or account | No correlation | Not recorded | Pending |
| R2 | Success >15 minutes after last failure | No correlation | Not recorded | Pending |
| R3 | 4740 account lockout in collected Security log | Account and caller fields returned | Not recorded | Pending |
| R3 | Ordinary 4625 without 4740 | No lockout result | Not recorded | Pending |

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
