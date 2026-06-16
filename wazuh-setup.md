\# Wazuh SIEM Setup



\## Overview

Wazuh provides Security Information and Event Management (SIEM) and Extended Detection and Response (XDR) capabilities for the home network lab. It monitors the Raspberry Pi for threats, file integrity violations, and suspicious activity in real time.



\## Architecture

\- \*\*Wazuh Manager + Dashboard:\*\* Running on laptop (JOSHUA) via Docker

\- \*\*Wazuh Agent:\*\* Running on Raspberry Pi 5 (pihole)

\- \*\*Communication:\*\* Via Tailscale (100.84.2**.** ↔ 100.122.2**.8*)



\## Prerequisites

\- Docker Desktop with WSL2 on Windows 11

\- Tailscale connectivity between laptop and Pi



\## Manager Installation (Laptop)



\### 1. Clone Wazuh Docker repo

```bash

git clone https://github.com/wazuh/wazuh-docker.git -b v4.9.2

cd wazuh-docker/single-node

```



\### 2. Generate SSL certificates

```bash

docker compose -f generate-indexer-certs.yml run --rm generator

```



\### 3. Start Wazuh stack

```bash

docker compose up -d

```



\### Containers

| Container | Role |

|-----------|------|

| single-node-wazuh.manager-1 | SIEM manager, rule engine |

| single-node-wazuh.indexer-1 | Data indexing (OpenSearch) |

| single-node-wazuh.dashboard-1 | Web UI (port 443) |



### Dashboard Access
- URL: https://localhost
- Username: ****
- Password: [set during deployment — see docker-compose.yml]


\## Agent Installation (Raspberry Pi)



\### Install matching version

```bash

sudo WAZUH\_MANAGER='100.84.2**.**' apt install wazuh-agent=4.9.2-1 -y

sudo sed -i 's/MANAGER\_IP/100.84.2**.**/' /var/ossec/etc/ossec.conf

sudo systemctl enable wazuh-agent

sudo systemctl start wazuh-agent

```



\## Detection Capabilities Active

| Capability | Status |

|-----------|--------|

| SSH brute force detection | ✅ |

| File Integrity Monitoring | ✅ |

| Privilege escalation detection | ✅ |

| MITRE ATT\&CK mapping | ✅ |

| Vulnerability assessment | ✅ |

| Log collection (syslog, auth) | ✅ |



\## Test Results

SSH brute force test triggered the following alerts:

\- Rule 5710: sshd: Attempt to login using a non-existent user (Level 5)

\- Rule 2502: syslog: User missed the password more than one time (Level 10)

\- Rule 5503: PAM: User login failed (Level 5)



MITRE ATT\&CK tactics detected:

\- Credential Access (10 events)

\- Lateral Movement (7 events)

\- Defense Evasion (3 events)

\- Privilege Escalation (2 events)

\- Initial Access (1 event)

