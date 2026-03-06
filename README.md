# Azure Sentinel SOC Detection Lab

This project demonstrates the creation of a cloud-based SOC monitoring lab using Microsoft Sentinel. 

The environment simulates authentication attacks against an Azure virtual machine and implements custom detection rules to identify brute force attempts, successful compromises, and account lockouts using Kusto Query Language (KQL).

---

# Architecture Overview

This environment uses the following components:

- Azure Virtual Machine (Windows)
- Azure Monitor Agent
- Data Collection Rule (DCR)
- Log Analytics Workspace
- Microsoft Sentinel
- Custom Analytics Detection Rules

Security events generated on the VM are collected by the Azure Monitor Agent and forwarded to Log Analytics. Microsoft Sentinel then analyzes these logs using KQL-based detection rules to identify suspicious activity.

---

# Architecture Diagram

<img width="352" height="512" alt="sentinel_soc_architecture" src="https://github.com/user-attachments/assets/e82df7fb-e7d0-49d4-9c36-04c22c28d377" />

---

# Detection Rules Implemented

## Rule 1 — Brute Force Login Detection (Event ID 4625)

Detects repeated failed authentication attempts from the same IP address against a single account within a short time window.

Purpose:
- Identify brute force login attempts.

Detection Logic:
- Monitor Event ID **4625**
- Count failed attempts grouped by account and IP address
- Trigger alert when attempts exceed threshold

---

## Rule 2 — Failed Logins Followed by Successful Login

Detects a sequence where multiple failed logins are followed by a successful authentication for the same account.

Purpose:
- Identify brute force attacks that eventually succeed.

Detection Logic:
- Monitor Event IDs **4625** and **4624**
- Correlate events for the same account and IP
- Trigger alert when success occurs shortly after repeated failures

---

## Rule 3 — Account Lockout Detection (Event ID 4740)

Detects when a user account becomes locked due to repeated authentication failures.

Purpose:
- Identify password spraying or aggressive brute force activity.

Detection Logic:
- Monitor Event ID **4740**
- Extract affected account and originating computer
- Generate alert when account lockout occurs

---

# Attack Simulation

The following attack scenarios were simulated to test the detections:

### Brute Force Attempt
Multiple incorrect passwords were used during RDP login attempts against the VM.

### Successful Compromise Scenario
Several failed login attempts were followed by a successful login to simulate a compromised account.

### Account Lockout
Repeated login failures triggered Windows account lockout protections.

---

# Incident Investigation Workflow

When alerts are triggered, analysts perform the following steps:

1. Identify the affected account from the alert entity.
2. Review authentication logs for the account.
3. Investigate the originating IP address.
4. Validate whether the activity is expected.
5. Determine if the account has been compromised.

Detailed investigation steps are documented in:
docs/incident-response-guide.md


---

# Key Skills Demonstrated

This project demonstrates:

- Security log analysis
- KQL query development
- Detection engineering
- Attack simulation
- SOC investigation workflow
- Microsoft Sentinel configuration
- Azure security monitoring

---

# Tools Used

- Microsoft Sentinel
- Azure Log Analytics
- Azure Monitor Agent
- Kusto Query Language (KQL)
- Windows Security Event Logs

---

---

---

# Screenshots

## Repository Structure

### Project Folder Layout
<img width="1903" height="940" alt="screenshots-01-repo-structure" src="https://github.com/user-attachments/assets/5e52800b-d42c-4d55-bd1d-47cec1868401" />

### Final Repository View
<img width="1807" height="841" alt="final_repo_view" src="https://github.com/user-attachments/assets/e35f6d6d-5a85-4a52-bc27-e1943befdc52" />

---

## Architecture

### SOC Monitoring Architecture Diagram
<img width="352" height="512" alt="sentinel_soc_architecture" src="https://github.com/user-attachments/assets/d42e0762-dd10-48d8-8633-0238aa635ba1" />

### Architecture Diagram Editor View
<img width="673" height="719" alt="architecture_editor_view" src="https://github.com/user-attachments/assets/c0b94bfb-3964-41b7-83fa-0dd4faa30331" />

---

## Detection Rules

### Rule 1 – Brute Force Detection (4625)
<img width="1887" height="945" alt="rule1_configuration" src="https://github.com/user-attachments/assets/a39cc2d5-8b00-44ca-a8e3-1071796d5a33" />

### Rule 2 – Failed Logins Followed by Successful Login
<img width="1910" height="953" alt="rule2_configuration" src="https://github.com/user-attachments/assets/0eb9f05d-852a-4263-a3e6-251cc72e6274" />

### Rule 3 – Account Lockout Detection (4740)
<img width="1918" height="961" alt="rule3_query" src="https://github.com/user-attachments/assets/6828b3ff-b5e0-4d5d-9efb-423ed885aaee" />

### Rule 3 Entity Mapping
<img width="782" height="356" alt="rule3_entity_mapping" src="https://github.com/user-attachments/assets/6fef1447-7622-4e29-9931-23fcb136ee11" />

---

## Detection Validation

### Query Showing Detection Results
<img width="1605" height="880" alt="query_detection_results" src="https://github.com/user-attachments/assets/deea18c8-29ba-4f8b-a7f9-f10531543a67" />

### Example Sentinel Incident
<img width="1609" height="903" alt="sentinel_incident_example" src="https://github.com/user-attachments/assets/77041434-6016-488f-a74b-47fdac07a39b" />

---

## Documentation

### Incident Response Guide
<img width="1780" height="849" alt="incident_response_guide" src="https://github.com/user-attachments/assets/bd9b069b-8add-450e-9c8b-894ad806aadb" />

### KQL Rules in Repository
<img width="1799" height="827" alt="repo_kql_rules" src="https://github.com/user-attachments/assets/048f5618-17d5-4b4d-85db-4924f89876d7" />

# Author

SOC Lab Project by **Ashkon Irani**

