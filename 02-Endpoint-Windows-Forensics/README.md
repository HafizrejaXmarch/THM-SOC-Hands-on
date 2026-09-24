# 02 - Endpoint & Host Forensics: Windows Core Artifacts & Triage Baseline

## Ringkasan Eksekutif
Dokumentasi ini mencakup prosedur investigasi host dan pengumpulan artefak dasar pada sistem operasi Windows menggunakan utilitas bawaan (*native utilities*). Prosedur ini mengacu pada tahap **Detection & Analysis** dalam standar NIST SP 800-61r2 untuk memvalidasi integritas aset, mengidentifikasi mekanisme retensi/eksekusi tidak sah (*persistence*), serta memetakan potensi celah pergerakan lateral (*lateral movement*).

---

## 1. Identifikasi Host & Baseline Sistem (Host Identification)
Dalam penanganan insiden, langkah pertama adalah memvalidasi detail aset yang terinfeksi guna menentukan tingkat kritikalitas dan kepemilikan host.

- **Utilitas:** `msinfo32.exe` (System Information) & `winver.exe` (About Windows)
- **Artefak yang Diekstraksi:**
  - **System Name (Hostname):** `THM-WINFUN2`
  - **Target OS & Arsitektur:** Windows Server / Windows 10 (AMD64)
  - **Registered License Owner:** `Windows User`
- **Relevansi SOC L1:** Mengonfirmasi baseline identitas host di SIEM saat alert masuk dan memastikan log berasal dari workstation/server yang sah.

---

## 2. Audit Mekanisme Startup & Indikasi Persistence (MITRE ATT&CK T1547)
Penyerang sering menggunakan startup folder atau registry run key agar malware tetap berjalan otomatis setiap kali sistem dinyalakan.

- **Utilitas:** `msinfo32.exe` -> `Software Environment` -> `Startup Programs`
- **Temuan Teknis:**
  - Program eksekusi: `RunWallpaperSetup`
  - Perintah eksekusi: `runwallpapersetup.cmd`
  - Konteks Pengguna: `THM-WINFUN2\Administrator`
- **Relevansi SOC L1:** Menjadi titik awal inspeksi (*triage*) saat mencurigai adanya backdoor atau script berbahaya yang disisipkan pada startup akun berhak istimewa (*privileged account*).

---

## 3. Enumerasi Berbagi Berkas & Jalur Lateral Movement (MITRE ATT&CK T1021.002)
Folder bersama (*network share*) yang terbuka atau disembunyikan (*hidden shares*) kerap dieksploitasi untuk pengintaian internal (*internal reconnaissance*) dan penyebaran malware antar-mesin.

- **Utilitas:** `compmgmt.msc` (Computer Management) -> `Shared Folders` -> `Shares` & CLI `net share`
- **Temuan Teknis:**
  - **Administrative Default Shares:** `ADMIN$`, `C$`, `IPC$` (akses bawaan untuk manajemen jarak jauh).
  - **Custom / Hidden Share:** `sh4r3dF0Ld3r$` (share folder non-standar yang disembunyikan menggunakan suffix `$`).
- **Relevansi SOC L1:** Analis harus mampu membedakan *share* bawaan Windows dari *share* tidak sah yang dibuat penyerang untuk eksfiltrasi data staging atau transfer payload.

---

## 4. Pemetaan Utilitas Diagnostik & Baseline LOLBins (MITRE ATT&CK T1059)
Penyerang kerap memanfaatkan biner administratif resmi Windows untuk menghindari deteksi antivirus (*Living off the Land*).

- **Utilitas:** `msconfig.exe` (System Configuration) -> Tab `Tools`
- **Pemetaan Perintah Eksekusi:**
  - **Audit Konfigurasi IP Jaringan:**  
    `C:\Windows\System32\cmd.exe /k %windir%\system32\ipconfig.exe`
  - **Akses Registry Windows:**  
    `regedt32.exe` / `regedit.exe`
  - **Variabel Lingkungan Sistem (ComSpec):**  
    `%SystemRoot%\system32\cmd.exe`
- **Relevansi SOC L1:** Menjadi dasar aturan deteksi (*detection engineering*); alert harus dipicu apabila biner administratif ini dipanggil oleh proses induk (*parent process*) yang tidak lazim (seperti `winword.exe` atau `powershell.exe` memanggil `cmd.exe`).

---

## 5. Hubungan ke Event Log & Langkah Investigasi Lanjutan
Artefak GUI di atas berkorelasi langsung dengan event security log berikut:
- **Event ID 4688 (Process Creation):** Mendeteksi eksekusi biner diagnostik (`ipconfig.exe`, `cmd.exe`).
- **Event ID 5140 / 5145 (Network Share Object Access):** Mendeteksi koneksi dan akses file ke folder tersembunyi `sh4r3dF0Ld3r$`.
- **Event ID 4697 / 7045 (Service Creation):** Mendeteksi instalasi layanan baru yang mencurigakan (seperti temuan `PsShutdown`).
