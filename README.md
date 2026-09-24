# TryHackMe SOC Hands-on Labs

Repositori ini mendokumentasikan investigasi teknis, analisis ancaman, ekstraksi *Indicators of Compromise* (IoC), dan rekomendasi mitigasi dari laboratorium praktikum Security Operations Center (SOC) di TryHackMe. Pendekatan dokumentasi difokuskan pada alur kerja investigasi analis SOC Tier 1.

## 🧭 Peta Jalan & Status Progres Investigasi

| No | Modul Investigasi | Topik & Room Utama | Status | Tautan Laporan |
| :---: | :--- | :--- | :---: | :---: |
| **01** | **Email & Phishing Analysis** | Phishing Analysis Fundamentals & In Action | ✅ Completed | [Lihat Laporan](./01-Email-Phishing-Analysis/) |
| **02** | **Endpoint & Host Forensics** | Windows Event Logs, Sysmon, Core Processes | ✅ In Progress | [Lihat Detail](./02-Endpoint-Windows-Forensics/) |
| **03** | **Network Traffic Analysis** | Wireshark Packet Analysis, NetworkMiner | ⏳ Planned | [Lihat Detail](./03-Network-Traffic-Analysis/) |
| **04** | **SIEM & Log Operations** | Splunk Searching (SPL), Wazuh EDR/SIEM | ⏳ Planned | [Lihat Detail](./04-SIEM-Log-Investigation/) |
| **05** | **Threat Intel & Frameworks** | MITRE ATT&CK, Cyber Defense Frameworks | ⏳ Planned | [Lihat Detail](./05-Threat-Intel-Frameworks/) |

---

## 🛡️ Standar Penanganan Artefak
Seluruh artefak berbahaya seperti alamat IP, domain penyerang, dan tautan URL dinetralkan menggunakan standar **defanging** (`hxxp`, `[.]`) agar aman disimpan dalam repositori publik.
