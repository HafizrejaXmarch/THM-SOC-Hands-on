# Modul 02: Endpoint & Host Forensics

## Ringkasan Eksekutif
Modul ini mendokumentasikan investigasi forensik host Windows secara berjenjang: identifikasi artefak dasar sistem operasi, audit jejak aktivitas melalui Windows Event Logs, dan deteksi ancaman tingkat lanjut menggunakan System Monitor (Sysmon).

---

## Struktur Pembelajaran & Status Progres

| No | Topik Praktikum | Fokus Analisis | Status | Tautan Laporan |
|:---|:---|:---|:---|:---|
| **01** | **Windows Core Artifacts & Triage** | Baseline sistem, persistensi startup, SMB hidden shares, audit biner native (`msconfig`, `msinfo32`, `compmgmt`). | ✅ Completed | [Buka Laporan & PoC](./01-Windows-Core-Artifacts/) |
| **02** | **Windows Event Log Analysis** | Struktur `.evtx`, query log via PowerShell (`Get-WinEvent`), audit event security & logon. | ⏳ Planned | [Buka Laporan & PoC](./02-Windows-Event-Logs/) |
| **03** | **Advanced Detection with Sysmon** | Deteksi proses mencurigakan, korelasi network beaconing, privilege escalation, dan threat hunting. | ⏳ Planned | [Buka Laporan & PoC](./03-Sysmon-Detection/) |

---

## Metodologi Investigasi Host
Setiap tahap praktikum mendokumentasikan:
1. **Host Artifact Triage:** Ekstraksi data konfigurasi lokal dan integritas file.
2. **Telemetry Correlation:** Menghubungkan proses yang tereksekusi dengan log aktivitas Windows.
3. **Detection Baseline:** Pemetaan indikator aktivitas ke taktik dan teknik MITRE ATT&CK.
