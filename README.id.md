<p align="center">
  <img src="QyuPipe.png" width="128" height="128" alt="QyuPipe Logo" />
</p>

# QyuPipe

> Baca ini dalam: [English](README.md) | **Bahasa Indonesia**

> **Klien YouTube ringan yang dioptimalkan untuk perangkat retro berlayar 1:1, BlackBerry 10 (QNX), dan Android 4.3 (Jelly Bean API 18).**

---

## 📖 Tentang Proyek

**QyuPipe** adalah aplikasi klien YouTube pihak ketiga yang dibuat khusus untuk perangkat berlayar kotak, terutama **BlackBerry Q10** dan **BlackBerry Classic (Q20)** yang menjalankan **BlackBerry 10 (QNX Kernel)** dengan subsistem **Android Runtime 4.3 (API 18)**.

Aplikasi ini dikembangkan dari basis kode **[notPipe](https://github.com/gohoski/notPipe)** versi 0.3 buatan gohoski. Di versi ini, sistemnya sudah dirombak total: mengatasi masalah sertifikat SSL/TLS modern di Android lawas, memangkas konsumsi RAM Dalvik, memastikan hardware decoder Snapdragon berjalan stabil, serta menyesuaikan navigasi keyboard fisik QWERTY untuk layar 720×720.

---

## ✨ Fitur Utama

### 1. Desain Khusus Layar 1:1 & Subtitle di Area Kosong
- Tampilan disesuaikan penuh untuk layar kotak 720×720 piksel.
- Video tetap berputar di rasio asli 16:9 di bagian atas. Ruang kosong di bawah video (*letterbox*) dimanfaatkan khusus untuk teks **Closed Caption (CC)**, jadi teks terjemahan tidak pernah menutupi gambar video.

### 2. Pilihan Bahasa Subtitle Bebas
- Mendukung format subtitle WebVTT, SubRip (.srt), dan YouTube TimedText XML.
- Sistem otomatis memprioritaskan bahasa asli dari video (tidak memaksa terjemahan otomatis mesin yang kaku).
- Tekan tombol CC untuk menyalakan/mematikan subtitle, atau tekan tahan (*long-click*) untuk membuka dialog dan memilih bahasa yang tersedia.

### 3. Mode Layar Mati (Putar Audio di Latar Belakang)
- Putar audio saja saat layar mati. Mode ini hemat baterai dan ramah panel AMOLED karena layar menampilkan warna hitam pekat (`#000000`).
- Menggunakan **Foreground Service** dan **Partial WakeLock** (`PARTIAL_WAKE_LOCK`), sehingga QNX tidak membekukan aplikasi saat tombol daya fisik ditekan.
- Transisi dari video ke audio berjalan instan tanpa buffering ulang.

### 4. Streaming Stabil Tanpa Masalah SSL
- Mengunci format stream pada **MP4 360p (itag 18)** yang menggabungkan video H.264 dan audio AAC dalam satu berkas.
- Stream dilewatkan melalui proxy lokal internal untuk memotong galat jabat tangan SSL/TLS (`SSLv3 handshake failure`) yang biasa terjadi di Android 4.3 saat mengakses server modern.
- Dilengkapi sistem pemulihan otomatis saat jaringan mengalami gangguan atau *timeout* (`-1004`).

### 5. Database SQLite Lokal & Paginasi Cepat
- Data video yang disukai, riwayat tontonan, dan langganan channel disimpan langsung ke tabel **SQLite** lokal.
- Menggunakan sistem paginasi (*lazy loading* 20 item per gulir) agar memori Dalvik di BlackBerry Q10 tidak kehabisan RAM (*OutOfMemory*).

### 6. Saran Pencarian Otomatis (Predictive Search)
- Kolom pencarian langsung memunculkan rekomendasi kata kunci secara *real-time*.
- Dilengkapi jeda pengetikan (*debounce* 300 ms) agar pencarian tetap lancar saat mengetik cepat lewat keyboard fisik.

### 7. Cadangkan & Pulihkan Akun (.txt / JSON)
- Kamu bisa mengekspor daftar channel langganan dan video disukai ke berkas teks sederhana (`.txt` berformat JSON) di folder Download.
- Saat aplikasi di-update ke APK versi baru, data akun lama bisa langsung diimpor kembali tanpa hilang.

### 8. Fitur Navigasi Praktis
- **Salin Tautan Cepat:** Tombol bagikan langsung menyalin tautan YouTube (`https://youtu.be/<id>`) ke *clipboard* sistem tanpa memunculkan menu aplikasi pihak ketiga.
- **Placeholder Hitam:** Thumbnail yang sedang dimuat memakai latar hitam solid agar tampilan daftar tidak berkedip (*no flicker*).
- **Halaman Profil Channel:** Menampilkan info channel, foto profil, tombol subscribe lokal, dan daftar video yang diunggah.

---

## 🛠️ Spesifikasi Teknis

| Bagian | Keterangan |
| :--- | :--- |
| **Target Perangkat** | BlackBerry Q10, BlackBerry Classic (Q20), BlackBerry Passport, dll. |
| **Sistem Operasi** | BlackBerry 10 OS (QNX Microkernel) |
| **Runtime Android** | Android 4.3 Jelly Bean (**API Level 18**) |
| **Arsitektur CPU** | `armeabi-v7a` (Qualcomm Snapdragon S4 Plus) |
| **Format Media** | MP4 Container (Video: H.264 360p, Audio: AAC Stereo 44.1 kHz) |
| **Batas Cache Gambar** | `LruCache` dibatasi maksimal **12 MB** |
| **Koneksi HTTP** | OkHttp 3.12.13 (versi stabil terakhir untuk Android 4.x) |

---

## ⚠️ Batasan yang Perlu Diketahui

1. **Belum Mendukung Live Streaming:** Video siaran langsung (Live) belum bisa diputar karena pemutar media bawaan Android 4.3 tidak mendukung format HLS/DASH modern.
2. **Tidak Ada Menu YouTube Shorts:** Format video vertikal 9:16 sengaja tidak dipasang antarmuka khusus karena tidak cocok dengan layar kotak 1:1 milik Q10.
3. **Resolusi Maksimal 360p:** Video dibatasi di 360p agar proses *decoding* hardware Snapdragon S4 tetap dingin, hemat baterai, dan tidak memakan banyak RAM.
4. **Tidak Menggunakan Akun Google:** Tidak ada fitur login akun Google karena keterbatasan Google Play Services di BB10. Semua data langganan dan favorit dikelola secara mandiri lewat database lokal dan fitur cadangan berkas `.txt`.

---

## 🔨 Cara Kompilasi (Build APK)

### Prasyarat
- **Java Development Kit (JDK):** JDK 8 atau JDK 11 (disarankan JDK 8).
- **Android SDK Build Tools:** 28.0.3 dengan platform API 28.

### Perintah Build
Jalankan perintah ini di folder proyek:

**Windows (PowerShell / CMD):**
```powershell
.\gradlew.bat assembleDebug
```

**Linux / macOS:**
```bash
./gradlew assembleDebug
```

### Lokasi Berkas APK
Setelah proses kompilasi selesai, berkas APK bisa ditemukan di:
- **Khusus BlackBerry Q10 (ARMv7, disarankan):**
  ```
  app/build/outputs/apk/debug/app-armeabi-v7a-debug.apk
  ```
- **Versi Universal:**
  ```
  app/build/outputs/apk/debug/app-universal-debug.apk
  ```

---

## 📱 Cara Pemasangan di BlackBerry Q10

> [!TIP]
> **Khusus OS 10.2.1 ke atas:** Kamu tidak perlu menyalakan Development Mode. Cukup buka **Settings** -> **App Manager** -> **Installing Apps**, lalu aktifkan **"Allow installation of apps from other sources"**.

1. Hubungkan BlackBerry Q10 ke laptop/PC dengan kabel USB, lalu salin file `app-armeabi-v7a-debug.apk` ke memori internal atau MicroSD (bisa juga dikirim lewat browser/Bluetooth).
2. Buka aplikasi **File Manager** bawaan BlackBerry 10.
3. Cari file APK tadi, klik, lalu pilih tombol **Install** di pojok kanan atas.
4. Setelah selesai, ikon **QyuPipe** langsung muncul di layar utama dan siap digunakan.

---

## ⚖️ Sumber Daya & Kredit

- Proyek ini dirilis di bawah lisensi **MIT License** - lihat berkas [LICENSE](LICENSE) untuk detail lengkap.
- Terima kasih banyak untuk proyek awal [notPipe](https://github.com/gohoski/notPipe) buatan gohoski, serta pengembang API publik Invidious dan Piped atas penyediaan backend alternatif YouTube.
