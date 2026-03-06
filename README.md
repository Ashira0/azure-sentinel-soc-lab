# Azure Sentinel SOC Detection Lab

This project demonstrates the implementation of a small Security Operations Center (SOC) detection environment using Microsoft Sentinel.

The lab simulates authentication attacks against an Azure VM and builds custom detection rules to identify malicious login behavior.

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

![Architecture](<img width="352" height="512" alt="sentinel_soc_architecture" src="https://github.com/user-attachments/assets/e82df7fb-e7d0-49d4-9c36-04c22c28d377" />)

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

# Author

SOC Lab Project by **Ashkon Irani**
