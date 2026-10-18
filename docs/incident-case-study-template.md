# SOC Incident Case Study - Suspicious Windows Authentication Activity

**Case ID:** SOC-LAB-2026-001  
**Analyst:** Ashkon Irani  
**Investigation Date:** October 18, 2026  
**Environment:** Microsoft Sentinel / Azure Windows Server 2022  
**Incident Type:** Suspected Brute-Force Authentication Attempt  
**Severity:** Medium (simulated triage classification)  
**MITRE ATT&CK:** T1110 - Brute Force  
**Disposition:** Confirmed authorized security testing; no malicious compromise identified  
**Investigation Type:** Controlled SOC lab simulation

> **Case context:** This case study documents an analyst-style investigation of genuine Windows authentication events generated through controlled testing. The events and KQL detection results were observed in Microsoft Sentinel. The case identifier, severity classification, and response workflow are illustrative; no automated Sentinel incident ID was verified.

## 1. Executive Summary

On October 18, 2026, a series of failed Windows authentication attempts was generated against the local account `soclabtest` on the Azure virtual machine `vm-soc-lab`.

Microsoft Sentinel collected the authentication events through Azure Monitor Agent and a Data Collection Rule. A KQL detection query identified six failed authentication attempts within a five-minute window, exceeding the configured threshold of five failures.

Further investigation identified seven failed authentication events followed by a successful login to the same account approximately seven minutes after the final failed attempt.

Additional controlled authentication testing triggered a Windows account lockout event (Event ID 4740).

From a SOC perspective, this sequence warranted investigation because repeated authentication failures followed by successful access can be consistent with password guessing or credential compromise.

After reviewing the test activity and confirming its authorized origin, the activity was classified as **benign security testing**, rather than a genuine malicious intrusion.

## 2. Scope and Evidence

| Evidence Field | Details |
|---|---|
| Affected Account | `vm-soc-lab\soclabtest` |
| Affected Host | `vm-soc-lab` |
| Operating System | Windows Server 2022 |
| Cloud Environment | Microsoft Azure |
| SIEM Platform | Microsoft Sentinel |
| Log Analytics Workspace | `law-sentinel-soc-lab` |
| Data Source | Windows Security Event Log |
| Log Analytics Table | `SecurityEvent` |
| Relevant Event IDs | 4625, 4624, 4740 |
| Source IP | Not established from the captured evidence |
| Authentication Origin | Authorized testing executed locally against the lab VM |
| Sentinel Incident ID | Not applicable — manual KQL investigation |
| Investigation Scope | Single Windows VM and temporary local test account |

### Evidence References

**Evidence 1 - Failed Authentication Event**

[View Event ID 4625 Evidence](../screenshots/02-failed-logon.png)

Confirms that Windows failed authentication events were successfully collected in Log Analytics.

**Evidence 2 - Brute-Force Detection**

[View Brute-Force Detection](../screenshots/03-brute-force-detection.png)

Shows six failed authentication attempts detected within the configured five-minute threshold window.

**Evidence 3 - Failed-to-Successful Login Correlation**

[View Authentication Correlation](../screenshots/04-failed-to-success-correlation.png)

Shows seven failed authentication events correlated with a subsequent successful login.

**Evidence 4 - Account Lockout Detection**

[View Account Lockout Event](../screenshots/05-account-lockout-detection.png)

Confirms that Event ID 4740 was generated and ingested following repeated authentication failures.

## 3. Incident Timeline (UTC)

| Time (UTC) | Evidence | Analyst Interpretation |
|---|---|---|
| October 18, before 23:03 | Failed authentication activity recorded | Initial evidence of unsuccessful login attempts against the test account |
| October 18, 23:03:36 | Latest failed authentication in the correlation result | Establishes the final failed login preceding successful access |
| October 18, 23:10:27 | Event ID 4624 - successful authentication | Successful login approximately 6 minutes 51 seconds after the last failure |
| October 18, later during testing | Event ID 4740 — account lockout | Additional invalid authentication attempts triggered the configured lockout threshold |
| October 18, during analysis | KQL detection queries returned matching results | Authentication sequence identified and reviewed in Microsoft Sentinel |

**Timeline note:** The timestamps above reflect the recorded authentication sequence. The precise timestamp of the subsequent account lockout should be taken from its original Event ID 4740 record if required for a formal incident report.

## 4. Investigation

### 4.1 Initial Detection and Triage

The investigation began by examining Windows Event ID 4625, which indicates a failed authentication attempt.

A KQL query grouped failed authentication events by account and computer within five-minute intervals.

The detection threshold was configured to identify groups containing at least five failed attempts.

**Observed results:**

- Affected account: `soclabtest`
- Affected computer: `vm-soc-lab`
- Failed authentication attempts: 6
- Detection threshold: 5
- Detection result: Positive match

The concentration of authentication failures within a short period was consistent with a password-guessing pattern.

However, repeated failures alone do not establish malicious intent.

### 4.2 Authentication Correlation

The investigation was expanded to determine whether the account experienced a successful authentication following the failures.

Windows Event ID 4624 was correlated with Event ID 4625 using the account name, computer, and authentication timestamps.

**Observed results:**

- Seven failed authentication events
- One subsequent successful authentication
- Same target account and computer
- Approximately seven minutes between the final failure and successful login

This sequence increased the hypothetical investigation priority because a successful authentication after repeated failures may indicate that valid credentials were eventually obtained.

The event sequence alone was insufficient to conclude that account compromise occurred.

### 4.3 Account Lockout Investigation

Additional authentication testing was conducted against the temporary account.

The local Windows account lockout threshold was configured to five failed attempts.

Eight incorrect authentication attempts were executed, resulting in a genuine Windows Event ID 4740.

The account lockout event was successfully forwarded to Log Analytics and identified using KQL.

This confirmed that the monitoring pipeline could detect account lockout activity associated with repeated authentication failures.

### 4.4 Source and Account Analysis

The investigation focused on the temporary local account `soclabtest`.

The account was created specifically for the SOC lab and was not associated with a production user or business service.

The authentication activity originated from authorized tests executed against the lab VM.

The captured evidence did not establish an external attacker-controlled IP address, malicious endpoint, or unauthorized remote session.

Therefore, the observed authentication pattern was treated as a simulated attack rather than evidence of an actual intrusion.

### 4.5 Alternative Explanations

In a production environment, similar authentication patterns could result from several causes:

- A legitimate user repeatedly entering an incorrect password.
- An application or service using outdated stored credentials.
- A scheduled task attempting authentication with expired credentials.
- An attacker attempting to guess an account password.
- Automated authentication testing performed by an authorized security team.

The appropriate response would depend on account ownership, source IP reputation, device history, logon type, and surrounding endpoint activity.

In this case, the known test activity provided a benign explanation for the observed events.

## 5. Analyst Assessment

**Final Classification:** Benign Positive - Authorized Security Testing

**Confidence:** High that the observed authentication activity was generated by authorized lab testing.

The detection queries successfully identified authentication patterns that could warrant investigation in a real environment.

However, the activity was intentionally generated as part of the SOC lab.

No evidence was established of unauthorized access, credential theft, malicious persistence, or lateral movement.

The case therefore demonstrates successful detection of suspicious authentication patterns, rather than confirmation of a genuine cyberattack.

## 6. Response and Containment

### 6.1 Actions Performed During Testing

- Generated controlled Windows authentication failures.
- Confirmed successful and failed authentication events in the Windows Security log.
- Verified ingestion into Microsoft Sentinel.
- Executed KQL queries to identify authentication anomalies.
- Correlated failed and successful authentication activity.
- Generated and verified an account lockout event.
- Captured supporting screenshots for investigation documentation.

### 6.2 Recommended Response in a Production SOC

If the same pattern occurred in a production environment without an authorized testing explanation, the following actions would be considered:

1. Confirm the account owner and determine whether the authentication activity was expected.
2. Review source IP addresses, logon types, and originating devices.
3. Examine successful logins for unusual locations, devices, or session activity.
4. Correlate authentication events with endpoint security alerts and identity logs.
5. Review account privileges and recent administrative activity.
6. Escalate to incident response personnel if compromise is suspected.
7. Consider password resets, session revocation, account restrictions, or temporary isolation when supported by the evidence.
8. Document findings, actions, and the final incident disposition.

These actions describe a recommended SOC workflow and were not all performed during the lab.

## 7. Detection Tuning Recommendations

The following improvements would strengthen the detection logic in a production environment.

**Brute-Force Detection**

- Tune authentication failure thresholds using historical activity.
- Group events by account and source IP where those fields are available.
- Account for authentication bursts crossing fixed time-window boundaries.
- Consider service accounts and known authentication systems to reduce false positives.

**Failed-to-Successful Authentication Correlation**

- Include source IP and logon type when available.
- Prioritize privileged accounts and high-value systems.
- Evaluate successful authentication against normal account behavior.
- Avoid treating every successful login following failures as confirmed compromise.

**Account Lockout Monitoring**

- Identify accounts experiencing repeated lockouts.
- Correlate lockouts with preceding failed authentication events.
- Investigate recurring lockouts involving privileged or sensitive accounts.
- Distinguish expected account-management activity from suspicious authentication behavior.

## 8. Investigation Limitations

This investigation was conducted in a controlled Azure lab rather than a production environment.

The following limitations apply:

- Authentication events were deliberately generated through authorized testing.
- No external malicious actor was involved.
- Source IP attribution was not validated.
- The investigation used manually executed KQL queries.
- No automated Sentinel incident identifier was established.
- Endpoint compromise, persistence, and lateral movement were not demonstrated.
- Detection accuracy and false-positive rates were not measured against production traffic.

These limitations do not invalidate the observed detection results but define the scope of the exercise.

## 9. Conclusion

The investigation successfully demonstrated the identification and analysis of suspicious Windows authentication patterns using Microsoft Sentinel.

Three primary detection scenarios were validated:

1. **Brute-force detection:** Six failed authentication attempts identified within five minutes.
2. **Failed-to-successful authentication correlation:** Seven failed logins followed by a successful authentication approximately seven minutes later.
3. **Account lockout monitoring:** Event ID 4740 successfully generated, collected, and detected.

The case was classified as authorized security testing after reviewing the known source and purpose of the authentication activity.

This exercise demonstrates practical experience with SIEM event analysis, KQL detection development, authentication event correlation, alert triage concepts, and structured SOC investigation documentation.

---

**Analyst:** Ashkon Irani  
**Case Reference:** SOC-LAB-2026-001  
**Final Disposition:** Benign Positive - Authorized Security Testing  
**Case Status:** Closed
