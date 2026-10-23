# Lab Setup

## 1. Machines (all on a host-only network)

| VM | Role | Minimum specs |
|---|---|---|
| Ubuntu 22.04 Server | Wazuh manager + indexer + dashboard (Docker) | 4 vCPU, 8 GB RAM |
| Windows 10/11 Eval | Endpoint with Wazuh agent + Sysmon | 2 vCPU, 4 GB RAM |
| Ubuntu 22.04 | Linux endpoint with Wazuh agent (SSH enabled) | 1 vCPU, 2 GB RAM |

## 2. Deploy Wazuh (single node, Docker)

Use the official deployment repo, which ships the compose file and certificate generator:

```bash
git clone https://github.com/wazuh/wazuh-docker.git -b v4.9.0
cd wazuh-docker/single-node
docker compose -f generate-indexer-certs.yml run --rm generator
docker compose up -d
```

Open `https://<manager-ip>`, log in with the default credentials from the Wazuh docs, and
**change the admin password immediately**.

## 3. Enrol the agents

In the dashboard go to **Agents → Deploy new agent**, choose the OS, and run the generated install
command on each endpoint. On Windows, also install **Sysmon** and enable **PowerShell Script Block
Logging** (Group Policy → Windows PowerShell → Turn on PowerShell Script Block Logging).

Add these to the Windows agent's `ossec.conf` to collect Sysmon and PowerShell logs:

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
<localfile>
  <location>Microsoft-Windows-PowerShell/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

## 4. Load the custom rules

```bash
docker exec -it single-node-wazuh.manager-1 bash
# inside the container, paste rules/local_rules.xml into:
vi /var/ossec/etc/rules/local_rules.xml
/var/ossec/bin/wazuh-logtest     # optional: test a sample log line
exit
docker restart single-node-wazuh.manager-1
```

## 5. Generate test activity (inside the lab only)

| Rule | How to trigger it safely |
|---|---|
| 100100 / 100101 | From another lab VM, try SSH with a wrong password 8+ times, then log in correctly |
| 100110 / 100111 | On Windows: `net user labtest P@ssw0rd! /add`, then `net localgroup Administrators labtest /add` (delete the account afterwards) |
| 100120 | Run `Invoke-AtomicTest T1059.001` from Atomic Red Team |

Check **Threat Hunting → Events** in the dashboard and follow the matching playbook.
