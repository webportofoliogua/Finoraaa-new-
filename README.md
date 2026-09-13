# 💚 Finora — Personal Finance Journal

**Finora** adalah aplikasi web pencatatan keuangan pribadi yang ringan, cepat, dan berjalan sepenuhnya dari **satu file HTML** — tanpa server, tanpa proses build, tanpa `npm install`. Buka filenya di browser, dan aplikasi siap dipakai.

Dirancang dengan gaya antarmuka *neumorphic* yang lembut, mendukung tema terang & gelap, serta dioptimalkan untuk penggunaan di ponsel maupun desktop.

> 📖 Untuk penjelasan teknis mendalam tentang setiap modul, fitur, dan logika di balik aplikasi ini, lihat [`DOCUMENTATION.md`](./DOCUMENTATION.md).

---

## ✨ Fitur Utama

**Pencatatan Keuangan**
- Catat transaksi pemasukan, pengeluaran, dan transfer antar rekening
- Saran kategori otomatis berdasarkan deskripsi transaksi
- Jurnal transaksi lengkap dengan pencarian & filter (jenis, kategori, rekening, urutan)

**Multi-Rekening**
- Tambah rekening sebanyak yang diinginkan (Bank, E-Wallet, Tunai, Kartu Kredit, Investasi)
- Kustomisasi warna kartu rekening (palet warna atau warna kustom)
- Sembunyikan saldo per rekening ala aplikasi perbankan (ikon 👁)

**Anggaran & Tabungan**
- Buat anggaran (budget) bulanan per kategori dengan peringatan otomatis saat mendekati/melebihi batas
- Kategori bebas dibuat sendiri, tidak terbatas daftar bawaan
- Target tabungan dengan progres visual, fitur tambah & kurangi saldo
- **Sistem streak menabung harian** — ikon api menyala jika konsisten menabung minimal Rp 10.000/hari

**Laporan & Ekspor**
- Grafik pemasukan vs pengeluaran (harian/bulanan), donut chart kategori pengeluaran
- **Ekspor rekap transaksi bulanan ke PDF** — pilih bulan mana saja, lengkap dengan ringkasan dan tabel rinci

**Notifikasi**
- Pengingat tagihan (jatuh tempo & terlambat bayar)
- Pengingat streak menabung harian
- Peringatan anggaran mendekati/melebihi batas

**Akun & Keamanan**
- Login lokal (email + password) tersimpan aman di perangkat, **atau**
- Login cloud opsional (email/password & Google, via Firebase) untuk sinkronisasi data lintas perangkat
- Auto-lock: tetap login saat refresh, wajib login ulang saat browser/tab benar-benar ditutup
- Ganti foto profil dari galeri (otomatis dikompres)

**Personalisasi**
- Tema terang/gelap
- Mata uang tampilan otomatis mengikuti kurs real-time (IDR, USD, EUR, SGD, JPY)
- Animasi transisi halaman & modal yang halus, menghormati preferensi *reduced motion*

---

## 🛠️ Teknologi yang Digunakan

Finora sengaja dibangun **tanpa framework** — murni HTML, CSS, dan JavaScript (vanilla), agar tetap portabel sebagai satu file tunggal.

| Teknologi | Kegunaan |
|---|---|
| **HTML5 + CSS3** | Struktur & tampilan (custom properties untuk theming, animasi CSS native) |
| **JavaScript (Vanilla ES6+)** | Seluruh logika aplikasi & manajemen state manual |
| **[Chart.js](https://www.chartjs.org/)** | Grafik pada Dashboard & halaman Laporan |
| **[jsPDF](https://github.com/parallax/jsPDF) + jsPDF-AutoTable** | Ekspor laporan transaksi ke PDF |
| **[Firebase](https://firebase.google.com/) (opsional)** | Autentikasi email/password & Google, serta sinkronisasi data via Cloud Firestore |
| **Google Fonts** (Inter, Space Grotesk) | Tipografi |
| **localStorage / sessionStorage** | Persistensi data & manajemen sesi login di browser |
| **[open.er-api.com](https://www.exchangerate-api.com/)** | Sumber data kurs mata uang real-time (gratis, tanpa API key) |

Semua dependensi eksternal dimuat lewat CDN — tidak ada file `node_modules`.

---

## 📋 Prasyarat & Instalasi

Karena Finora adalah aplikasi single-file, **tidak ada instalasi dalam arti tradisional**. Yang dibutuhkan hanya:

- Browser modern (Chrome, Edge, Firefox, atau Safari versi terbaru)
- Koneksi internet — dipakai untuk memuat font, library CDN (Chart.js, jsPDF), dan mengambil kurs mata uang real-time. Aplikasi tetap bisa dibuka offline, namun beberapa fitur (grafik, ekspor PDF, kurs otomatis) memerlukan koneksi saat pertama kali dimuat.

### Menjalankan secara lokal

```bash
# 1. Clone repository ini
git clone https://github.com/<username>/finora.git
cd finora

# 2. Buka langsung di browser
# (opsi termudah, cukup double-click file-nya)
open finora.html        # macOS
start finora.html       # Windows
xdg-open finora.html    # Linux
```

Atau, untuk pengalaman yang lebih mendekati produksi (URL berbasis `http://` alih-alih `file://`), jalankan lewat server statis sederhana:

```bash
# Menggunakan Python
python3 -m http.server 8000
# lalu buka http://localhost:8000/finora.html

# Atau menggunakan Node.js (npx, tanpa instalasi global)
npx serve .
```

### Mengaktifkan Sinkronisasi Cloud (Opsional)

Secara default, Finora berjalan dalam **mode lokal** — data tersimpan di browser saja. Untuk mengaktifkan login email/Google beserta sinkronisasi data lintas perangkat:

1. Buat proyek gratis di [Firebase Console](https://console.firebase.google.com/).
2. Aktifkan **Authentication** (metode Email/Password dan Google), serta **Cloud Firestore**.
3. Salin konfigurasi proyek Firebase Anda.
4. Buka `finora.html`, cari konstanta `FIREBASE_CONFIG`, lalu isi dengan kredensial dari langkah 3.
5. Simpan dan buka ulang file — layar login otomatis berubah menjadi mode cloud (email & tombol Google akan muncul).

> Panduan langkah demi langkah yang lebih detail tersedia sebagai komentar tepat di atas `FIREBASE_CONFIG` di dalam kode.

---

## 📁 Susunan Proyek

```
finora/
├── finora.html          # Seluruh aplikasi (HTML + CSS + JS dalam satu file)
├── README.md             # Dokumen ini
├── DOCUMENTATION.md       # Dokumentasi teknis mendalam tiap modul/fitur
└── LICENSE                # Lisensi MIT
```

Di dalam `finora.html` sendiri, kode disusun dalam blok-blok bertanda komentar, kurang lebih sebagai berikut:

```
<head>          → meta tag, font, library CDN
<style>         → seluruh CSS (tema, komponen, layout, animasi)
<body>
  <div id="app"> → kontainer yang diisi ulang oleh JavaScript
  <script>
    ├─ Helper (format tanggal, angka, mata uang)
    ├─ Data awal (kategori, rekening, transaksi contoh)
    ├─ state{}             → satu objek besar berisi seluruh kondisi aplikasi
    ├─ Persistensi (localStorage & Firestore)
    ├─ Autentikasi (lokal & Firebase)
    ├─ renderApp()          → fungsi render utama
    ├─ render<Halaman>()    → satu fungsi per halaman (Dashboard, Budget, dst.)
    └─ Fungsi aksi (submit, delete, toggle, dst.)
```

Penjelasan detail tiap bagian ada di [`DOCUMENTATION.md`](./DOCUMENTATION.md).

---

## 🚀 Contoh Penggunaan

**1. Membuat akun & masuk**
Buka `finora.html` → isi email & kata sandi (minimal 6 karakter) di layar pendaftaran → otomatis masuk ke Dashboard.

**2. Mencatat transaksi**
Klik tombol **+** (mengambang di kanan bawah pada desktop, atau di tengah bilah navigasi bawah pada mobile) → pilih jenis (Pemasukan/Pengeluaran/Transfer) → isi nominal, deskripsi, kategori, rekening → **Simpan Transaksi**.

**3. Membuat anggaran bulanan**
Buka menu **Budget** → **Buat Budget** → pilih kategori (atau ketik nama kategori baru) → isi nominal batas per bulan → **Simpan**.

**4. Menabung dan menjaga streak**
Buka menu **Tabungan** → pilih target tabungan → **Tambah** → isi nominal minimal Rp 10.000 agar streak menyala.

**5. Mengunduh laporan bulanan**
Buka menu **Laporan** → pilih bulan pada kolom tanggal → klik **Unduh PDF**.

**6. Mengganti mata uang tampilan**
Buka **Profil → Mata Uang** → pilih mata uang yang diinginkan. Kurs akan diperbarui otomatis secara berkala.

---

## 🤝 Kontribusi

Kontribusi dalam bentuk apa pun sangat diterima — baik laporan bug, ide fitur, maupun perbaikan kode.

1. **Fork** repository ini.
2. Buat branch baru untuk perubahanmu:
   ```bash
   git checkout -b fitur/nama-fitur-anda
   ```
3. Lakukan perubahan pada `finora.html`. Karena proyek ini berbentuk single-file tanpa proses build, cukup pastikan perubahanmu:
   - Tidak merusak alur render (`renderApp()`) yang sudah ada — lihat [bagian Arsitektur](./DOCUMENTATION.md#2-filosofi--arsitektur) sebelum mengubah logika render.
   - Diuji langsung dengan membuka file di browser sebelum mengirim perubahan.
4. Commit dengan pesan yang jelas:
   ```bash
   git commit -m "Menambahkan fitur: ..."
   ```
5. Push ke fork-mu dan buka **Pull Request** ke branch utama repository ini, dengan deskripsi singkat mengenai apa yang diubah dan mengapa.

Untuk perubahan besar, disarankan membuka **Issue** terlebih dahulu untuk didiskusikan sebelum mulai mengerjakan.

---

## 📄 Lisensi

Proyek ini dilisensikan di bawah **Lisensi MIT** — lihat berkas [`LICENSE`](./LICENSE) untuk teks lengkapnya.

Secara singkat, lisensi MIT mengizinkan siapa pun untuk menggunakan, menyalin, memodifikasi, menggabungkan, menerbitkan, mendistribusikan, dan/atau menjual salinan perangkat lunak ini secara bebas, selama pemberitahuan hak cipta dan izin ini disertakan dalam setiap salinan atau bagian penting dari perangkat lunak.

---

<p align="center">Dibuat dengan 💚 oleh <b>Irsyad Maulana</b> · <a href="https://instagram.com/syadd_m4u">@syadd_m4u</a></p>
