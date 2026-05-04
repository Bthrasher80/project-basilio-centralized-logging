# 📡 Centralized Logging & SIEM Deployment
**Project Basilio — Enterprise Cyber Home Lab**

---

## 📌 Objective

Deploy a centralized logging and SIEM solution for the Project Basilio SOC lab that:

- Collects and correlates security events from lab endpoints
- Provides real-time alerting with MITRE ATT&CK mapping
- Enables file integrity monitoring on monitored hosts
- Mirrors enterprise SOC visibility and detection workflows

---

## 🛠️ Tools & Environment

| Component | Details |
|---|---|
| SIEM Platform | Wazuh 4.7.2 (All-in-One deployment) |
| Server OS | Ubuntu 22.04 LTS |
| Hypervisor | Proxmox VE 9.1.2 |
| Enrolled Agent | linux-victim-01 (Ubuntu 22.04) |
| Agent Version | Wazuh Agent v4.7.2 |
| Dashboard Access | HTTPS on port 443 |

---

## 🧱 Deployment Overview

Wazuh was deployed as an all-in-one installation on a dedicated Ubuntu VM (VM ID 104) running on Proxmox. The deployment includes:

- **Wazuh Indexer** — OpenSearch-based log storage and indexing
- **Wazuh Manager** — Event correlation, rule processing, and alerting
- **Wazuh Dashboard** — Web UI for alert visualization and investigation
- **Filebeat** — Log shipping between manager and indexer

Installation was performed using the official Wazuh install script (`wazuh-install.sh -a`) which automated the full stack deployment in a single operation.

---

## 🖥️ Agent Enrollment

A Linux victim machine (linux-victim-01, VM ID 105) was provisioned on Proxmox and enrolled as a Wazuh agent pointing to the Wazuh manager at `192.168.1.170` on port 1514 over TCP.

Agent configuration (`/var/ossec/etc/ossec.conf`) was verified to confirm:
- Correct server address and port
- AES encryption method
- File integrity monitoring (syscheck) enabled
- Monitored directories: `/etc`, `/usr/bin`, `/usr/sbin`, `/bin`, `/sbin`, `/boot`

Upon successful enrollment, the Wazuh dashboard confirmed 2 active agents at 100% coverage.

---

## 🔍 Detections & Alerts

### SSH Brute Force (T1110 — Brute Force)
Simulated SSH login attempts against linux-victim-01 generated alerts mapped to:
- **T1110** — Brute Force (Credential Access)
- **T1110.001** — Password Guessing
- **T1021.004** — Remote Services: SSH (Lateral Movement)
- Rule 2502 — Level 10: User missed password more than one time
- Rule 5710 — Level 5: sshd attempt to login using non-existent user

Compliance frameworks triggered: GDPR IV_35.7.d, HIPAA 164.312.b, PCI DSS 10.2.4, NIST AU.14

### Privilege Escalation (T1548.003 — Sudo and Sudo Caching)
Successful sudo to root execution on linux-victim-01 generated:
- **T1548.003** — Abuse Elevation Control Mechanism
- **T1078** — Valid Accounts (Defense Evasion, Persistence, Initial Access)
- Rule 5402 — Level 3: Successful sudo to ROOT executed

### File Integrity Monitoring (FIM)
Syscheck was enabled and configured to monitor critical directories. A simulated attack script (`/tmp/basilio-test.sh`) was created, modified, and deleted on linux-victim-01, generating FIM alerts mapped to:
- **T1565.001** — Stored Data Manipulation (Impact)
- **T1070.004** — File Deletion (Defense Evasion)
- **T1485** — Data Destruction (Impact)
- Rule 554 — File added to system
- Rule 550 — Integrity checksum changed
- Rule 553 — File deleted

---

## 🔐 Security & SOC Relevance

This deployment enables:
- Real-time detection of credential attacks against lab endpoints
- File integrity monitoring across critical system paths
- MITRE ATT&CK mapped alerting for triage and investigation
- Compliance framework visibility (GDPR, HIPAA, PCI DSS, NIST)
- Foundation for detection engineering and incident response exercises

---

## 🧠 Lessons Learned

- All-in-one Wazuh deployment is efficient for lab environments but resource-intensive
- Agent enrollment requires correct server IP in ossec.conf — version mismatches cause silent failures
- FIM requires syscheck to be enabled and a scan cycle to complete before events appear
- Real attack simulation (even simple scripts) immediately generates meaningful detections

---

## 📸 Evidence

Screenshots in the `/screenshots` directory demonstrate:

- Wazuh VM creation and configuration in Proxmox
- Successful all-in-one installation log
- Wazuh manager and indexer services confirmed active
- Agent enrollment — linux-victim-01 active in dashboard
- SSH brute force alerts with MITRE ATT&CK mapping
- Privilege escalation detection
- FIM events showing file add, modify, and delete lifecycle
- Alert detail view with full compliance framework mapping
- ossec.conf agent configuration

---

## 🏁 Status

**Artifact 2: COMPLETE ✅**
Centralized logging is operational, agents enrolled, and detections validated with MITRE ATT&CK mapping.
