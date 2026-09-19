# Modul 01: Email Phishing Triage & Forensics

## Ringkasan Eksekutif
Analisis ini mendokumentasikan investigasi terhadap 3 vektor serangan phishing berbasis email nyata dengan berbagai taktik manipulasi: pemalsuan identitas merek (Brand Impersonation), pengintaian via pelacak web (Tracking Pixel), dan pengalihan kredensial melalui dokumen lampiran (PDF Lure).

---

## Kasus 1: PayPal Payment Receipt (Brand Impersonation & Link Obfuscation)

### Analisis Serangan
- **Taktik Psikologis:** Penyerang mengirimkan tagihan fiktif sebesar $120.00 USD untuk memicu rasa panik penerima agar segera membatalkan transaksi palsu tersebut.
- **Header Spoofing:** Nama pengirim menampilkan identitas resmi `service@paypal.com`, namun jalur pengiriman asli berasal dari domain tidak sah.
- **Link Masking:** Tombol aksi `Cancel the order` menyembunyikan tautan asli dengan pemendek URL (`https://is.gd/6oCJ4m`) guna melewati penyaring tautan email.

### Bukti Konsep (PoC)
![PayPal Email Body](./screenshots/task2-paypal-body.png)
<img width="940" height="491" alt="image" src="https://github.com/user-attachments/assets/256814a5-96b9-4993-abb6-95e14b2a8a6e" />

*Gambar 1.1: Tampilan badan email tanda terima pembayaran fiktif.*

![HTML Link Analysis](./screenshots/task2-html-link.png)
*Gambar 1.2: Kode HTML tombol membongkar tautan pemendek is.gd.*

![URL Unshorten](./screenshots/task2-url-unshorten.png)
*Gambar 1.3: Hasil pelacakan tautan asli via layanan ekspansi URL.*

### Indikator Keberadaan Ancaman (IoC)
| Tipe IoC | Nilai Artefak (Defanged) | Deskripsi |
| :--- | :--- | :--- |
| **Display Name** | `service@paypal[.]com` | Identitas merek yang dipalsukan |
| **Malicious URL** | `hxxps[://]is[.]gd/6oCJ4m` | Tautan pemendek pengalih tujuan |

---

## Kasus 2: Package Delivery Lure (Email Tracking Pixel)

### Analisis Serangan
- **Taktik:** Menggunakan nomor resi pelacakan kargo palsu (`# LZ8942357486EN`) dengan pengirim anonim `Distribution Center <contact@beginpro.club>`.
- **Reconnaissance:** Penyerang menyematkan elemen gambar tak terlihat (`Tracking.png`) pada kode sumber HTML. Saat email dibuka, permintaan GET otomatis terkirim ke server penyerang untuk merekam status aktif email, waktu akses, dan alamat IP publik korban.

### Bukti Konsep (PoC)
![Tracking Email](./screenshots/task3-tracking-email.png)
*Gambar 2.1: Header email dan tautan pelacakan paket tiruan.*

![Tracking Pixel HTML](./screenshots/task3-pixel-html.png)
*Gambar 2.2: Bukti tag gambar pelacak internal dan target domain devret.xyz.*

### Indikator Keberadaan Ancaman (IoC)
| Tipe IoC | Nilai Artefak (Defanged) | Deskripsi |
| :--- | :--- | :--- |
| **Sender Email** | `contact@beginpro[.]club` | Alamat pengirim mencurigakan |
| **Target Domain** | `devret[.]xyz` | Host penampung tautan & tracking pixel |
| **Tracking Artifact** | `hxxp[://]devret[.]xyz/Creatives/Tracking[.]png` | Gambar pelacak aktivitas penerima |

---

## Kasus 3: Netflix Account On Hold (PDF Attachment Lure)

### Analisis Serangan
- **Typosquatting:** Pengirim menggunakan ejaan manipulatif `Netllx billing <z99@musacombi.online>` dengan huruf 'l' menggantikan 'i'.
- **Attachment Delivery:** Badan email memancing korban mengunduh file lampiran `Payment-up....pdf`. Dokumen tersebut memuat tombol pembaruan akun pembayaran yang mengarahkan ke portal pencurian kredensial (*Credential Harvesting*).

### Bukti Konsep (PoC)
![Netflix Header](./screenshots/task5-netflix-header.png)
*Gambar 3.1: Header email dengan indikasi salah eja merek Netllx.*

![Netflix PDF Lure](./screenshots/task5-pdf-lure.png)
*Gambar 3.2: Ajakan memperbarui informasi pembayaran via dokumen PDF.*

### Indikator Keberadaan Ancaman (IoC)
| Tipe IoC | Nilai Artefak (Defanged) | Deskripsi |
| :--- | :--- | :--- |
| **Sender Email** | `z99@musacombi[.]online` | Alamat domain palsu peniru Netflix |
| **Attachment** | `Payment-up....pdf` | Dokumen PDF pembawa tautan phishing |

---

## Rekomendasi Mitigasi SOC
1. Blokir domain `devret[.]xyz`, `musacombi[.]online`, dan domain pemendek URL publik pada Email Security Gateway (SEG).
2. Nonaktifkan pemuatan gambar eksternal secara otomatis di klien email untuk menggagalkan pelacakan email (*tracking pixel*).
3. Jalankan pemindaian otomatis dan isolasi (*sandboxing*) terhadap dokumen PDF yang menyematkan tautan eksternal.
