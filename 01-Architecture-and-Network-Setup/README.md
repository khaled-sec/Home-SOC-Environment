# Module 01: Architecture & Network Setup

## Overview

This module documents the network topology and connectivity setup for the Home SOC Environment before deploying Active Directory (Module 02) and Splunk (Module 03).

---

## Step 1: VM Topology

![Architecture Diagram](assets/architecture-diagram.png)

| Machine | Role | IP Address |
|---------|------|-------------|
| Kali Linux | Attacker | 192.168.1.30 |
| Windows Server 2019/2022 | Domain Controller / SOC (Splunk) | 192.168.1.10 |
| Windows 10 | Domain-joined Victim | 192.168.1.20 |

---

## Step 2: Network Configuration

## Step 2: Network Configuration

Configured static IPs on each machine to ensure consistent addressing across reboots (important for Splunk forwarder targeting and AD DNS resolution).

![Windows Server static IP](assets/server_ip.png)
![Windows 10 static IP](assets/win_ip.png)
![Kali Linux static IP](assets/kali_ip.png)
---

## Step 3: Verify Connectivity

From Kali, confirmed connectivity to the domain controller and victim machine:

```bash
ping 192.168.1.10   # Windows Server (DC / Splunk)
ping 192.168.1.20   # Windows 10 (victim)
```

![Ping output](assets/ping.png)

This confirms all machines are reachable before proceeding with AD DS setup and Splunk deployment.

---

## Lessons Learned


- Verifying basic connectivity (ping) before touching AD or Splunk saves time troubleshooting "why isn't data flowing" issues that are actually just network issues.
- Keeping all VMs on the same isolated virtual network (VMware) avoids interference with the host network and keeps the lab self-contained.
