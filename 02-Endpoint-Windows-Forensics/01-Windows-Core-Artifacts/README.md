# 01 - Windows Core Artifacts & Triage Baseline

## Ringkasan Eksekutif
Dokumentasi ini mencakup prosedur investigasi host dan pengumpulan artefak dasar pada sistem operasi Windows menggunakan utilitas bawaan (*native utilities*). Prosedur ini mengacu pada tahap **Detection & Analysis** standar NIST SP 800-61r2 untuk memvalidasi integritas aset, mengidentifikasi mekanisme retensi/eksekusi tidak sah (*persistence*), serta memetakan potensi celah pergerakan lateral (*lateral movement*).

---

## 1. Identifikasi Host & Baseline Sistem (Host Identification)
Dalam penanganan insiden, langkah pertama adalah memvalidasi detail aset yang terdampak guna memastikan konteks alert di SIEM berasal dari target yang tepat.

- **Utilitas:** `msinfo32.exe` (System Information)
- **Temuan Teknis:**
  - **Hostname:** `THM-WINFUN2`
  - **Arsitektur:** AMD64 / Windows Server
- **PoC:**
  <!-- PASTE SCREENSHOT SYSTEM SUMMARY (msinfo32) DI SINI -->

---

## 2. Audit Mekanisme Startup & Persistence (MITRE ATT&CK T1547)
Penyerang kerap menambahkan script atau binary pada program startup agar malware aktif otomatis setiap sesi boot/login.

- **Utilitas:** `msinfo32.exe` -> `Software Environment` -> `Startup Programs`
- **Temuan Teknis:**
  - **Program Name:** `RunWallpaperSetup`
  - **Command:** `runwallpapersetup.cmd`
  - **User Context:** `THM-WINFUN2\Administrator`
- **Relevansi SOC L1:** Membedakan proses autostart normal dari indikasi persistence backdoor pada sesi user berhak akses tinggi (*privileged*).
- **PoC:**
  <!-- PASTE SCREENSHOT STARTUP PROGRAMS DI SINI -->

---

## 3. Enumerasi Berbagi Berkas & Jalur Lateral Movement (MITRE ATT&CK T1021.002)
Folder bersama (*network share*) yang terbuka atau disembunyikan (*hidden shares*) kerap dimanfaatkan penyerang untuk pengintaian (*reconnaissance*) atau penyebaran payload malware antar-mesin.

- **Utilitas:** `compmgmt.msc` (Computer Management) -> `Shared Folders` -> `Shares` & CLI `net share`
- **Temuan Teknis:**
  - **Administrative Default Shares:** `ADMIN$`, `C$`, `IPC$`
  - **Custom / Hidden Share Terdeteksi:** `sh4r3dF0Ld3r$` (folder tersembunyi menggunakan identitas karakter `$`).
- **Relevansi SOC L1:** Analis wajib menandai share non-standar yang disembunyikan sebagai anomali yang perlu diaudit hak aksesnya.
- **PoC:**
  <!-- PASTE SCREENSHOT COMPMGMT SHARES DI SINI -->

---

## 4. Pemetaan Utilitas Native & Baseline LOLBins (MITRE ATT&CK T1059)
Audit biner administratif resmi Windows yang berpotensi disalahgunakan (*Living off the Land*).

- **Utilitas:** `msconfig.exe` (System Configuration) -> Tab `Tools`
- **Pemetaan Perintah Eksekusi:**
  - **Panggilan IPConfig:** `C:\Windows\system32\cmd.exe /k %windir%\system32\ipconfig.exe`
  - **Biner Registry Editor:** `regedt32.exe` / `regedit.exe`
  - **Variabel ComSpec:** `%SystemRoot%\system32\cmd.exe`
- **Relevansi SOC L1:** Menjadi basis deteksi process execution jika biner tersebut dipanggil oleh parent process anomali (misalnya browser atau file office).
- **PoC:**
  <!-- PASTE SCREENSHOT MSCONFIG TOOLS (IPCONFIG) DI SINI -->
