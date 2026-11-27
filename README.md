# PT Freeport Indonesia — Mine Operations Dashboard

Dashboard monitoring operasi tambang bawah tanah (underground) PT Freeport Indonesia berbasis web. Seluruh aplikasi berada dalam **satu file HTML** (HTML + CSS + JavaScript murni), tanpa framework dan tanpa proses build.

> **Catatan:** Proyek ini adalah prototipe/simulasi untuk keperluan belajar dan demonstrasi. Data operasional bersifat statis (deterministik) dan **bukan data resmi** perusahaan. Logo dan nama PT Freeport Indonesia dipakai hanya sebagai tema.

---

## Fitur Utama

### Autentikasi & Hak Akses (RBAC)
- Halaman login dengan validasi kredensial, animasi *shake* saat gagal, dan tombol tampil/sembunyi kata sandi.
- Akses halaman dan area dibatasi per peran (role).
- Sesi disimpan di `localStorage`, sehingga pengguna tetap masuk setelah halaman dimuat ulang.
- Tombol **Keluar Sesi** dan **Reset Status** pada modal profil.

### Halaman Dashboard
| Halaman | Isi |
|---|---|
| **Production** | Tonase tambang, plan vs aktual, unit aktif, pekerja per level |
| **Personnel** | Kehadiran, roster, tagging bawah tanah, shift boss |
| **Safety** | Aliran udara vs kebutuhan, alarm gas, aktivitas seismik |
| **Ecology** | Tailing, drainase batuan asam, reklamasi dataran tinggi/rendah, pH, TSS |

### Area Tambang
- **GBC** — Grasberg Block Cave (kapasitas 150.000 mt/hari)
- **DMLZ** — Deep Mill Level Zone (kapasitas 80.000 mt/hari)
- **BGS** — Big Gossan (kapasitas 7.500 mt/hari)

### Level Operasi
Extraction Level → Haulage Level → Mill · Mile 74 → Portsite Amamapare

### Peta Blok Interaktif
- Peta drawpoint (AA, AB, BA, AC, AD, BB, CA, CB, DA) dengan status aktif/idle.
- **Pan, zoom** (scroll/pinch/tombol), dan **layar penuh** dengan animasi transisi.
- Mode **Pilih** dan **Multi-pilih** (atau tahan `Shift`), serta overlay risiko.
- Tandai blok sebagai **Wet muck**, **Restricted** (batasan geoteknik), atau **Normal**.
- 11 lensa peta: Crew & Shift, Equipment Health, Ventilation, Remote Ops & CCTV, Spatial Block Map, Drilling & Blasting, Geothermal & Temp, Camp & Logistics, Access Control, PPE/APD, Haulage & Rail Routing.

### Timeline Shift
- Shift 12 jam (06:00–18:00 WIB) berdasarkan zona waktu **Asia/Jakarta**.
- Penanda waktu dapat digeser (drag) untuk melihat kondisi pada jam tertentu.
- Riwayat **7 hari** terakhir, lengkap dengan shift boss dan jendela peringatan (alert window).

### Panel Kanan
- Sisa cadangan per jenis: **Ore, Cu, Au, Ag**.
- Grafik "Place of development" dengan *range slider*.
- Daftar **Workers** dan **Fleet** (LHD, MineGem Auto-LHD, UG Truck, Rail) dengan progres muatan, siklus, uptime, dan menu aksi unit (tandai kendala, lacak posisi, alihkan blok, kunci target, reset siklus, dll.).

### Fitur Lain
- **Laporan Shift** dengan ekspor **CSV** dan cetak/PDF.
- **Notifikasi** operasional dan log aktivitas per level.
- **Pencarian** area, blok, unit, atau operator (`Ctrl/⌘ + K`).
- Panel CCTV simulasi (Remote Operations Center).
- Status integrasi sistem (MineStar, SCADA, RFID, GeoMoS) dan ringkasan anomali aktif.

---

## Akun Demo

| Username | Kata sandi | Peran | Halaman |
|---|---|---|---|
| `director.ptfi` | `Ptfi@2026` | Mine Operations Director | Semua |
| `prod.gbc` | `Prod@123` | Production Superintendent | Production |
| `hr.shift` | `Hr@12345` | HR & Workforce Lead | Personnel |
| `safety.ug` | `Safe@123` | Underground Safety Officer | Safety |
| `eco.env` | `Eco@1234` | Environmental & Tailings Lead | Ecology |

> ⚠️ **Peringatan keamanan:** kredensial ditulis langsung di sisi klien (JavaScript) dan hanya cocok untuk demo. Untuk produksi, gunakan autentikasi di sisi server (mis. OAuth/JWT) dan jangan menyimpan kata sandi di kode front-end.

---

## Cara Menjalankan

Tidak ada instalasi atau build.

1. Simpan file HTML (mis. `index.html`).
2. Buka langsung di browser modern (Chrome, Edge, Firefox, Safari), **atau** jalankan server lokal:
   ```bash
   # Python
   python -m http.server 8000

   # Node.js
   npx serve .
   ```
3. Buka `http://localhost:8000` lalu masuk dengan salah satu akun demo.

**Koneksi internet** dibutuhkan untuk memuat font Inter (Google Fonts) dan ikon Hugeicons. Jika CDN ikon gagal dimuat, aplikasi otomatis memakai ikon SVG inline sebagai cadangan.

---

## Teknologi

- HTML5, CSS3 (CSS Variables, Grid, Flexbox, media query), JavaScript ES6+ (tanpa dependensi)
- Font: [Inter](https://fonts.google.com/specimen/Inter)
- Ikon: [Hugeicons](https://hugeicons.com/) dengan *fallback* SVG inline
- Penyimpanan: `localStorage`
- Waktu: `Intl.DateTimeFormat` (zona waktu Asia/Jakarta)

---

## Struktur Kode

Semua berada di satu file, dengan urutan:

```
<style>      Desain, animasi, dan breakpoint responsif
<body>       Layar login, shell dashboard (sidebar, top bar, timeline, peta, panel), dock mobile
<script>
 ├─ USERS / RBAC           Akun, peran, hak akses halaman & area
 ├─ Ikon                   Peta ikon Hugeicons + fallback SVG
 ├─ PTFI_API_CONFIG        Konfigurasi endpoint & kunci penyimpanan
 ├─ AREAS                  Data area, blok, sensor, infrastruktur
 ├─ SHIFT_HISTORY          Riwayat shift 7 hari
 ├─ FLEET_ROSTER           Daftar armada per area
 ├─ Model operasional      lvl(), blocks(), prodSeries(), lensVals(), unitCalc()
 ├─ Render                 renderSide/Top/TL/Left/Map/Right/Modal
 ├─ Aksi (act)             Handler klik, flag blok, status unit, laporan, CSV
 ├─ Interaksi              Drag timeline, range slider, pan/zoom/fullscreen peta
 └─ Autentikasi & boot     Login, logout, pemulihan sesi
```

---

## Penyimpanan Lokal

| Kunci `localStorage` | Isi |
|---|---|
| `ptfi_ops_concrete_v2` | Flag blok, penugasan unit, status unit, target terkunci, notifikasi |
| `ptfi_active_session_v1` | Username sesi aktif |

Tombol **Reset Status** pada modal profil menghapus data operasional yang tersimpan dan mengembalikan ke data awal.

---

## Integrasi API (Rencana)

Objek konfigurasi sudah disiapkan:

```js
window.PTFI_API_CONFIG = {
  endpoint: "",                       // URL endpoint JSON produksi
  storageKey: "ptfi_ops_concrete_v2", // kunci penyimpanan lokal
};
```

Saat ini data masih dibaca dari konstanta `AREAS`, `SHIFT_HISTORY`, dan `FLEET_ROSTER`. Pemanggilan `fetch` ke `endpoint` **belum diimplementasikan**. Untuk menghubungkan ke API sungguhan, ganti sumber data tersebut dengan hasil `fetch` lalu panggil `renderAll()`.

---

## Responsif

| Tier | Lebar layar | Tampilan |
|---|---|---|
| Mobile | ≤ 640px | Sidebar disembunyikan, dock bawah bergaya *liquid glass*, kartu geser horizontal, modal bergaya *bottom sheet* |
| Tablet | 641–1023px | Sidebar ringkas, kartu 2 kolom, peta dan panel bertumpuk |
| Laptop/Desktop | 1024–1679px | Tata letak 3 kolom |
| Full HD | ≥ 1680px | Sidebar lebar dengan label, kolom lebih lega |

Mendukung `prefers-reduced-motion` dan gaya cetak (`@media print`).

---

## Pintasan Keyboard

| Tombol | Fungsi |
|---|---|
| `Ctrl/⌘ + K` | Buka pencarian |
| `Esc` | Tutup modal/menu atau keluar dari layar penuh peta |
| `Shift` + klik blok | Pilih beberapa blok |

---

## Kustomisasi

- **Tambah akun/peran:** edit array `USERS`.
- **Tambah area atau blok:** tambahkan entri pada `AREAS` (lengkap dengan `blocks`, `hourlyShare`, `safetyMetrics`, `ecologyMetrics`) dan `FLEET_ROSTER`.
- **Ubah warna/tema:** edit CSS variables di `:root` (`--bg`, `--ink`, `--green`, dll.).
- **Tambah lensa peta:** tambahkan entri pada `LENSES` dan kasus baru di `lensVals()`.

---

## Batasan

- Data bersifat simulasi dan statis, bukan telemetri nyata.
- Autentikasi hanya di sisi klien (tidak aman untuk produksi).
- Integrasi REST API belum tersambung.
- Panel CCTV hanya tampilan simulasi, bukan streaming video.

---

## Lisensi & Disclaimer

Proyek ini dibuat untuk tujuan edukasi dan portofolio. Seluruh merek, logo, dan nama (PT Freeport Indonesia, Caterpillar, MineStar, Siemens, dll.) adalah milik pemiliknya masing-masing dan tidak berafiliasi dengan proyek ini.
