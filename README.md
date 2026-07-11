# Home SOC Environment (Self-Built)

A self-designed SOC lab to demonstrate hands-on infrastructure and detection engineering skills — built and documented.

**Status:** Core environment built and validated — from network setup, through AD DS and SIEM deployment, to attack simulation with working Splunk detections and alerts.

⚠️ **All attacks are executed in an isolated, air-gapped virtual lab (VMware) with no internet-facing components. No real malware is distributed; payloads are generated solely for detection engineering purposes.**

![Home SOC Architecture](assets/architecture-diagram.png)

| Machine | Role | IP Address |
|---|---|---|
| Kali Linux | Attacker | `192.168.1.30` |
| Windows Server 2019 | Domain Controller (AD DS) + SOC server (Splunk) | `192.168.1.10` |
| Windows 10 | Domain-joined victim/endpoint | `192.168.1.20` |

**Domain:** `SERVER.LOCAL` (NetBIOS: `AD`) · **Splunk index:** `endpoint_win10`

---

## Modules

| # | Module | Summary | Status |
|---|---|---|---|
| 01 | [Architecture & Network Setup](./01-Architecture-and-Network-Setup) | Static IPs + connectivity verification across all VMs | ✅ |
| 02 | [Active Directory Domain Setup](./02-Active-Directory-Domain-Setup) | AD DS forest `SERVER.LOCAL`, Windows 10 domain-joined | ✅ |
| 03 | [Splunk Deployment](./03-Splunk-Deployment) | Splunk Enterprise + Universal Forwarder + Sysmon → `endpoint_win10` | ✅ |
| 04 | [Attack Simulation & Detection](./04-Attack-Simulation-and-Detection) | SMB brute force + payload execution → SPL detection → scheduled alerts | ✅ |

### Attack 1 — Brute Force (SMB) · MITRE ATT&CK T1110

Brute-forced `AD\Khaled` over SMB using Metasploit, then built a Splunk detection correlating failed/successful logons (4625/4624):

```spl
index=endpoint_win10 (EventCode=4625 OR EventCode=4624)
| stats count(eval(EventCode=4625)) as Failed count(eval(EventCode=4624)) as Success by Account_Name
| where Failed>=5 AND Success>0
```

Configured a scheduled, throttled **High**-severity alert. Full write-up (incident response steps, screenshots) in [Attack_1_Brute_Force.md](./04-Attack-Simulation-and-Detection/Attack_1_Brute_Force.md).


---
### Attack 2 — Malicious Payload Delivery & Execution · MITRE ATT&CK T1204.002 / T1071.001

Delivered a disguised Meterpreter payload (`invoice.exe`) via `msfvenom`, executed it on the domain-joined endpoint, and built a generalized Splunk detection (Sysmon Event ID 1) for any executable/script launched from a Downloads folder. Configured a scheduled **High**-severity alert. Full write-up (incident response steps, mitigations, screenshots) in [Attack_2_Malicious_Payload_Execution.md](./04-Attack-Simulation-and-Detection/Attack_2_Malicious_Payload_Execution.md).


---

## What I Learned

- **Detection is only half the job** — the incident response plan (contain → investigate → recover) matters as much as the SPL query that fired the alert.
- **Dedicated indexing scales better** than dumping everything into `main`, especially as more endpoints get added.
- **Sysmon + Universal Forwarder is a natural pairing** — once the forwarder reads Windows Event Logs, adding Sysmon's channel is minimal extra effort for a major jump in detection depth (process lineage, network connections, credential access).
- **Correlating failed and successful logons** (not just counting failures) is what separates a real brute-force detection from noisy false positives.
- **Centralized identity management via AD DS** means a single account or Group Policy change propagates instantly across every domain-joined machine — powerful, but also a bigger blast radius if compromised.
- **Sysmon Event ID 1 supports IOC-agnostic detection** — instead of hardcoding a single filename, the Attack 2 query was generalized to flag any executable/script launched from a Downloads folder, making the detection reusable against future payloads rather than just the one sample tested.

---

## Skills Demonstrated

- Network design & AD DS deployment
- Splunk SIEM deployment, indexing strategy, Universal Forwarder + Sysmon
- SPL detection engineering & scheduled/throttled alerting
- MITRE ATT&CK mapping (T1110,T1204.002)
- Incident response planning
- Attack simulation across multiple techniques (credential-based & execution-based)
