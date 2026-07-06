# Module 04: Attack Simulation and Detection

> Part of the [Home SOC Environment](https://github.com/khaled-sec/Home-SOC-Environment).

With AD DS (Module 02) and Splunk + Sysmon ingestion (Module 03) in place, this module simulates real attacks against the domain-joined Windows 10 endpoint and validates detection in Splunk — completing the loop from attack to alert.

---

## Attacks

| # | Title | Technique | Tool | Detection |
|---|---|---|---|---|
| 1 | [Brute Force (SMB)](./Attack_1_Brute_Force.md) | T1110 – Brute Force | Metasploit (`smb_login`) | Failed→Success correlation, Splunk scheduled alert |

---

## Attack 1 — Brute Force (SMB)

**Target:** Domain-joined Windows 10 — `192.168.1.20`

Brute-forced the domain account `AD\Khaled` over SMB using Metasploit. Built a Splunk detection correlating failed and successful logons (Event ID 4625/4624) for the same account, and configured a scheduled alert (High severity) that fires when the pattern is met.

📄 Full write-up → [Attack_1_Brute_Force.md](./Attack_1_Brute_Force.md)

---

## Environment

Same lab as prior modules — see [Home-SOC-Environment.md](https://github.com/khaled-sec/Home-SOC-Environment) for full architecture and component details.
