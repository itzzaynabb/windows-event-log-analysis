# Windows Endpoint Security: Authentication Log Analysis & Brute-Force Detection

## 📌 Project Overview
In a Security Operations Center (SOC), monitoring endpoint authentication logs is essential for identifying unauthorized access, brute-force attempts, and credential stuffing. This project demonstrates how to detect, analyze, and investigate authentication anomalies using Windows Security Event Logs.

---

## 🛠️ Tools & Environment
- **Operating System:** Windows 10/11
- **Tool:** Windows Event Viewer (`eventvwr.msc`)
- **Log Source:** Security (`C:\Windows\System32\winevt\Logs\Security.evtx`)

---

## 🔍 Key Windows Event IDs Analyzed

| Event ID | Description | Security Significance |
| :--- | :--- | :--- |
| **4624** | An account was successfully logged on | Baseline user activity, verifying session initiation. |
| **4625** | An account failed to log on | Core indicator of password guessing, brute force, or credential spraying. |
| **4672** | Special privileges assigned to new logon | Administrative logon or privilege elevation. |
| **4740** | A user account was locked out | Occurs when consecutive 4625 failures exceed lockout thresholds. |

---

## 🔬 Attack Simulation & Detection

### 1. Simulated Scenario
Simulated an authentication brute-force attempt by generating consecutive invalid credential submissions against a target account within a compressed time window.

### 2. Forensic Findings
- **Anomaly Pattern:** Identified **4 consecutive Event ID 4625 (Audit Failure)** events occurring within a 10-second interval (8:02:30 PM – 8:02:40 PM).
- **Logon Type Evaluation:** Verified `Logon Type 2 (Interactive)` representing physical/console authentication, as opposed to `Logon Type 10 (RemoteInteractive/RDP)` or `Logon Type 3 (Network)`.
- **Status & Failure Code Analysis:**
  - Evaluated Sub Status `0xC000006A` (valid username, incorrect password supplied).
- **Post-Attack Success:** Observed subsequent transition to **Event ID 4624 (Audit Success)**, highlighting the critical analyst workflow: determining whether an attacker succeeded or a legitimate user recovered credentials.
<img width="1627" height="782" alt="Screenshot 2026-09-20 200549" src="https://github.com/user-attachments/assets/f085b496-e221-46e3-accf-ab6e162345ee" />

## 🛡️ Blue Team Defensive Recommendations
1. **Account Lockout Thresholds:** Configure Group Policy Objects (GPO) to lock accounts after 5 invalid attempts to neutralize automated brute forcing.
2. **Multi-Factor Authentication (MFA):** Enforce MFA across all administrative and remote access channels to render stolen passwords ineffective.
3. **SIEM Correlation Rules:** Configure automated alerts when Event ID 4625 occurrences exceed 3 failures within a 60-second window per workstation or IP.
