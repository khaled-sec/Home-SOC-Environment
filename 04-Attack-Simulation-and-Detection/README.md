# Module 04: Attack Simulation and Detection

> Part of the [Home SOC Environment](https://github.com/khaled-sec/Home-SOC-Environment).

⚠️ **All attacks are executed in an isolated, air-gapped virtual lab (VMware) with no internet-facing components. No real malware is distributed; payloads are generated solely for detection engineering purposes.**

With AD DS (Module 02) and Splunk + Sysmon ingestion (Module 03) in place, this module simulates real attacks against the domain-joined Windows 10 endpoint and validates detection in Splunk — completing the loop from attack to alert.

---

## Attacks

| # | Title | Technique | Tool | Detection |
|---|---|---|---|---|
| 1 | [Brute Force (SMB)](./Attack_1_Brute_Force.md) | T1110 – Brute Force | Metasploit (`smb_login`) | Failed→Success correlation, Splunk scheduled alert |
| 2 | [Malicious Payload Delivery & Execution](./Attack_2_Malicious_Payload.md) | T1204.002 – User Execution / T1071.001 | Metasploit (`msfvenom`, `multi/handler`) | Sysmon EventID 1 — new executable from Downloads folder |

---
---

## Attack 1 — Brute Force (SMB)

**Target:** Domain-joined Windows 10 — `192.168.1.20`

Brute-forced the domain account `AD\Khaled` over SMB using Metasploit. Built a Splunk detection correlating failed and successful logons (Event ID 4625/4624) for the same account, and configured a scheduled alert (High severity) that fires when the pattern is met.

📄 Full write-up → [Attack_1_Brute_Force.md](./Attack_1_Brute_Force.md)

---


## Attack 2 — Malicious Payload Delivery & Execution

**Target:** Domain-joined Windows 10 — `192.168.1.20`

Generated a disguised Meterpreter payload (`invoice.exe`) using `msfvenom`, delivered and executed it on the endpoint to open a reverse Meterpreter session. Built a Splunk detection using Sysmon Event ID 1, generalized to flag **any** executable/script launched from a Downloads folder (`.exe`, `.msi`, `.ps1`, `.bat`) rather than hardcoding the sample filename — and configured a scheduled, High-severity alert.

📄 Full write-up → [Attack_2_Malicious_Payload.md](./Attack_2_Malicious_Payload.md)

---
## Environment

Same lab as prior modules — see [Home-SOC-Environment.md](https://github.com/khaled-sec/Home-SOC-Environment) for full architecture and component details.
