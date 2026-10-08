# SIAGA — Sistem Informasi & Administrasi Warga

RT 10 / RW 021 Villa Indah Pulo Timaha

Setiap file HTML bisa dibuka langsung tanpa folder CSS/JS terpisah — semua stylesheet dan ilustrasi hero sudah ditanam langsung ke file HTML. Satu-satunya file pendukung adalah `config.js`, tempat semua nomor WhatsApp/telepon dan data kas diatur.

## ⚙️ Status RT 10 (versi awal — tanpa data warga)

Proyek ini diubah dari basis RT 08 menjadi **RT 10 / RW 021**. Semua cara kerja sama, tetapi **data masih kosong**:

| Yang masih kosong | Diisi di mana |
|---|---|
| Daftar rumah / blok | `config.js` → `daftarRumah` (format `"A1 No.12"`) |
| Data kepala keluarga | `warga-data.js` → `WARGA_DATA` |
| Nomor WA warga | `warga-kontak.js` → `WARGA_KONTAK` |
| Nama & nomor WA/telepon pengurus, rekening bendahara | `config.js` |
| Firebase (project BARU untuk RT 10) & UID admin/bendahara/humas | `config.js` + `PANDUAN-FIREBASE.md` |
| Alamat GitHub Pages & Firebase untuk aplikasi Android | cari teks `GANTI-USERNAME` dan `GANTI-PROJECT` di folder `android/` |

Selama Firebase belum diisi, fitur yang butuh database menampilkan pesan "belum dikonfigurasi"; halaman lain tetap normal.
Setelah membuat Firebase baru, tempel isi `firebase-rules.json` di Rules dan ganti UID RT 08 lama dengan UID akun RT 10 (cari `GANTI-UID-ADMIN`, dst).


## Langkah wajib sebelum dipakai warga

Buka `config.js` dengan text editor apa pun (Notepad, TextEdit, atau langsung "Edit file" di GitHub), lalu isi:

1. **Nomor WhatsApp pengurus** (`waKetua`, `waSekretaris`, `waBendahara`, `waKeamanan`) — format `62` + nomor tanpa `0` di depan, contoh `6281234567890`. Tanpa ini, tombol Ajukan/Lapor/Hubungi di halaman Layanan, Kontak, Transparansi, dan UMKM tidak akan berfungsi.
2. **Nomor telepon** untuk halaman Kontak (`teleponKetua`, dst).
3. (Opsional) `linkGrupWA` untuk tombol "Grup WhatsApp Warga" di footer, dan `linkDokumen` untuk folder dokumen RT (Google Drive dll).
4. **`adminPassword`** — password untuk masuk ke halaman `admin.html`. Ganti secara berkala.

Simpan `config.js`, lalu upload ulang bersama file HTML lainnya. Semua halaman otomatis memakai data terbaru — tidak perlu edit satu-satu di tiap file HTML.

## Halaman Admin (`admin.html`) — kelola konten tanpa edit kode

Pengurus RT bisa login ke `admin.html` (pakai `adminPassword` di atas) untuk
mengelola 5 hal, tanpa perlu sentuh kode sama sekali — otomatis tampil di
halaman publik begitu disimpan:

| Tab di Admin | Otomatis tampil di halaman |
|---|---|
| 📅 Agenda | `agenda.html`, Beranda |
| 📢 Informasi | `informasi.html`, Beranda |
| 🖼️ Galeri | `galeri.html` — admin tinggal upload foto, otomatis dikompres |
| 🛍️ UMKM | `umkm.html` — admin tinggal upload foto usaha & data usaha |
| 💰 Keuangan | `transparansi.html` — catat 1 transaksi (masuk/keluar), saldo & rekap bulanan otomatis terhitung |

Fitur ini butuh **Firebase Realtime Database** sebagai tempat penyimpanan
bersama (gratis) supaya semua HP melihat data yang sama secara real-time.
Selama `firebaseConfig` di `config.js` masih kosong, halaman-halaman di atas
akan menampilkan pesan "belum dikonfigurasi" — fitur lain di situs tetap
normal.

Lihat **`PANDUAN-FIREBASE.md`** untuk langkah lengkap membuat project Firebase,
mengisi `firebaseConfig`, dan memasang security rules-nya (wajib, ada di
panduan tersebut).

## Layanan → Surat PDF (siap tanda tangan & stempel)

Empat layanan di `layanan.html` — **Surat Pengantar, Warga Baru, Warga Pindah, Pinjam Fasilitas** —
sekarang membuat **surat PDF** (lengkap kop RT, nomor, tabel data, ruang tanda tangan & stempel Ketua RT),
bukan lagi sekadar pesan teks WhatsApp. Aspirasi, Dokumen RT, dan Lapor Lingkungan tetap lewat WhatsApp teks.

Alur warga: isi form → **Buat Surat PDF** → **Kirim ke WhatsApp** pengurus → pengurus cetak/tanda tangan/stempel.

- File baru: **`surat-pdf.js`** (pembuat PDF tanpa library luar; wajib ikut di-upload bersama `layanan.html`).
  Kolom form dan redaksi tiap surat ada di objek `JENIS` di file itu — boleh diedit.
- **Kop surat resmi**: Pemerintah Kabupaten Bekasi → Kecamatan Babelan → RT 010 / RW 021 → alamat perumahan → email,
  dengan **logo RW (kiri)** dan **logo RT (kanan)** serta garis ganda. Teks kop diatur di `config.js`
  (`kopPemerintah`, `kopKecamatan`, `kopRTRW`, `alamatKopSurat`, `emailKopSurat`, plus `tempatSurat`).
  Logo ada di `assets/logo-rw.png` dan `assets/logo-rt.png` — ganti file itu (nama sama) kalau logo berubah.
  Nama Ketua diambil dari isian `namaKetua`.
- Nomor surat dibiarkan titik-titik (`........ / RT.10 / RW.021 / IX / 2026`) untuk diisi pengurus.
- PDF dibuat di HP warga; **tidak disimpan di server/Firebase**.

Cara PDF sampai ke WhatsApp (WhatsApp tidak mengizinkan situs mengirim file otomatis, jadi warga tetap menekan Kirim):
1. **Aplikasi Android Warga versi 1.1+** — membuka chat pengurus dengan PDF sudah terlampir (perlu build ulang APK, lihat `android/`).
2. **Browser HP (Chrome/Safari)** — muncul lembar bagikan dengan PDF terlampir → pilih WhatsApp → pilih kontak pengurus.
3. **Cadangan (laptop/aplikasi lama)** — PDF terunduh dan chat WhatsApp pengurus terbuka; PDF dilampirkan manual.

## Cara kerja fitur (tanpa server/backend)

Karena situs ini murni statis (cocok untuk GitHub Pages), semua form dan tombol aksi (Lapor Lingkungan, Ajukan Surat, Hubungi UMKM, dll) bekerja dengan cara membuka chat WhatsApp berisi pesan otomatis ke nomor pengurus terkait — bukan mengirim ke database. Ini yang membuat portal bisa langsung dipakai warga tanpa perlu membangun server sendiri.

Data yang butuh diperbarui berkala (Agenda, Informasi, Galeri, UMKM, dan
Laporan Keuangan) dikelola lewat `admin.html` + Firebase seperti dijelaskan
di atas, bukan diedit langsung di kode HTML.

## Deploy ke GitHub Pages

1. Upload seluruh file (HTML + `config.js`) ke repository, sejajar (satu folder).
2. Pastikan `index.html` berada di root.
3. Aktifkan Settings > Pages > Deploy from branch > main / root.


## Iuran Tambahan (dana tidak rutin, bisa diaktif/nonaktifkan)

Untuk pengumpulan dana di luar iuran bulanan (perbaikan jalan, santunan, acara, dll).

- **Bendahara** → tab **🎯 Iuran Tambahan** di `bendahara.html`: buat judul (nominal per rumah & target total boleh kosong = sukarela), tombol **Aktifkan / Nonaktifkan**, lihat progres, Excel, dan **Rincian + Kwitansi PDF** (`kwitansiTambahan` di `surat-pdf.js`).
- **Warga** → `tambahan.html` (muncul tombol di Beranda & banner di Bayar Iuran **hanya saat ada yang aktif**): transfer lalu lapor + foto bukti.
- **Humas** → kartu "🎯 Iuran Tambahan" di `humas.html` (hanya tampil bila ada yang aktif): catat cash, kwitansi PDF, kabari Bendahara untuk setoran.
- Alur uang sama seperti iuran bulanan: transfer/cash dikonfirmasi Bendahara → otomatis jadi **Pemasukan Kas RT** (kategori "Iuran Tambahan") → dipublikasikan lewat tombol Publikasi ke Transparansi.
- **Wajib**: gabungkan aturan baru di `firebase-rules-tambahan-saja.json` ke Firebase Rules (lihat panduan di dalam file `firebase-rules.json`).

## Tampilan (redesign UI 2026)

Seluruh tampilan memakai satu design system: `assets/siaga.css` (token warna navy/emas, tipografi Plus Jakarta Sans + Inter, tombol, kartu, form, tabel) dan `assets/siaga-ui.js` (ikon Lucide inline, navbar, drawer, pencarian halaman, bottom-nav mobile). Kedua file dimuat di setiap halaman setelah CSS inline lama, jadi logika Firebase/form tidak berubah.

- Ganti warna/radius/bayangan cukup di blok `:root` pada `assets/siaga.css`.
- Emoji di tampilan otomatis diganti ikon oleh `siaga-ui.js` (teks pesan WhatsApp tidak tersentuh). Ikon baru: tambahkan path SVG Lucide pada daftar `ICONS` di `assets/siaga-ui.js`.
- Header, bottom-nav, dan footer halaman publik ada di tiap file HTML (blok `sg-header`, `sg-bottom`, `sg-footer`); ubah menu di sana.
