# PT Freeport Indonesia — Mine Operations Dashboard

Prototipe dashboard operasi tambang bawah tanah (Grasberg Block Cave, Deep Mill Level Zone, dan Big Gossan) dalam **satu file HTML** tanpa build tool, tanpa framework, dan tanpa backend.

> **Penting:** seluruh angka operasional pada dashboard ini adalah **data simulasi** yang diskalakan dari angka publik. Prototipe ini **tidak berafiliasi** dengan PT Freeport Indonesia.

---

## Fitur

- **4 halaman utama:** Production, Personnel, Safety, dan Ecology, masing-masing dengan kartu level, metrik, dan progress bar sendiri.
- **3 area tambang:** Grasberg Block Cave (GBC), Deep Mill Level Zone (DMLZ), dan Big Gossan (BG).
- **Peta blok interaktif** (drawpoint AA–DA + kolom infrastruktur N/A):
  - geser (drag), zoom (tombol +/−, scroll, Ctrl + scroll), dan mode layar penuh;
  - klik blok untuk memfilter, **Shift + klik** atau mode *multi-pilih* untuk memilih beberapa blok;
  - overlay risiko untuk blok **WET MUCK** dan **RESTRICTED**;
  - menu titik tiga pada tiap blok: fokuskan, tandai wet muck, batasi akses, normalkan.
- **11 lensa peta:** Crew & Shift, Equipment Health, Ventilation & Airflow, Remote Ops & CCTV, Spatial Block Map, Drilling & Blasting, Geothermal & Temp, Camp & Logistics, Access Control, PPE/APD Compliance, Haulage & Rail Routing.
- **Timeline 06:00–18:00** yang bisa diseret untuk replay per jam, ditambah pemilih hari (Day 20–26) dan jendela peringatan.
- **Panel kanan:** sisa cadangan (Ore / Cu / Au / Ag), grafik stream per blok, rentang yang bisa diatur, daftar pekerja, dan **Fleet** (LHD, MineGem, UG Truck, Rail) lengkap dengan uptime, cycle time, loading, dan aksi per unit.
- **Simulator live:** data bergerak tiap 2 detik, uptime unit berjalan tiap detik, dan breakdown acak muncul sebagai notifikasi.
- **Utilitas:** pencarian global, notifikasi, laporan shift, ekspor **CSV**, cetak/PDF, dan salin tautan.
- **Responsif:** sidebar pada layar lebar, tata letak bertumpuk pada tablet/ponsel, dan reduced-motion didukung.

## Cara Menjalankan

Tidak perlu instalasi.

1. Simpan kode sebagai `index.html`.
2. Buka langsung di browser modern (Chrome, Edge, Firefox, Safari), atau jalankan server statis:

   ```bash
   # opsi 1
   npx serve .
   # opsi 2
   python3 -m http.server 8000
   ```

3. Buka `http://localhost:8000`.

**Koneksi internet bersifat opsional.** Font Inter (Google Fonts) dan ikon Hugeicons dimuat dari CDN. Jika gagal dimuat, font beralih ke font sistem dan ikon otomatis memakai SVG inline bawaan.

## Kontrol & Pintasan

| Aksi | Cara |
| --- | --- |
| Pencarian global | `Ctrl/⌘ + K` atau tombol Search |
| Tutup modal, menu, atau layar penuh | `Esc` |
| Pilih banyak blok | `Shift + klik` atau mode multi-pilih |
| Geser peta | Drag pada area peta |
| Zoom peta | Tombol +/−, scroll (desktop), atau `Ctrl + scroll` |
| Ubah jam | Seret penanda pada timeline |
| Kembali ke waktu nyata | Tombol **Today** / **Replay** |
| Buka/tutup sidebar | Tombol panah pada sisi sidebar |

## Struktur Kode

Semua ada di satu file `index.html`:

| Bagian | Isi |
| --- | --- |
| `<style>` | Design token (`:root`), komponen, breakpoint responsif, dan blok `FIX` / `PATCH-n` untuk perapian tampilan |
| `PS`, `HGC`, `I()` | Ikon: font Hugeicons bila tersedia, jika tidak memakai SVG inline |
| `AREAS`, `LEVELS`, `BLOCKS`, `LENSES`, `PAGES` | Data statis dan konfigurasi |
| `S` | State global aplikasi |
| `lvl()`, `blocks()`, `fleet()`, `lensVals()` | Model data simulasi (deterministik berbasis hash + sedikit drift acak) |
| `render*()` | Fungsi render per bagian UI, memakai `patch()` (DOM morph) agar transisi CSS dan scroll tetap utuh |
| `act()` | Pusat penanganan aksi lewat atribut `data-a` |
| Timeline / Map / Simulator | Penanganan pointer untuk timeline, pan/zoom peta, dan `setInterval` simulator |

## Kustomisasi

- **Tanggal "hari ini":** ubah konstanta `TODAY` (saat ini `26`, label `NOV`).
- **Area tambang:** edit objek `AREAS` (plan harian `np`, pace, sisa cadangan `remain`, kadar `grade`, jumlah unit dan pekerja).
- **Blok:** edit array `BLOCKS` (`act` aktif/tidak, posisi grid `c`/`r`, bobot `w`).
- **Warna & tema:** ubah variabel CSS pada `:root` (`--bg`, `--green`, `--or`, `--red2`, dan lainnya).
- **Zona waktu:** jam "sekarang" mengikuti `Asia/Jakarta` (fungsi `jakartaHour()`), dibatasi pada rentang 06:00–18:00.

## Menghubungkan ke Data Nyata

Dashboard ini memakai data mock. Untuk sistem produksi, ganti fungsi model dengan data dari API:

| Sumber data | Contoh protokol | Fungsi yang diganti |
| --- | --- | --- |
| Fleet management / dispatch | REST / API Gateway | `fleet()`, `unitCalc()` |
| SCADA / PLC crusher & conveyor | OPC-UA / MQTT | bagian infrastruktur N/A di `renderMap()` |
| Tagging personel bawah tanah | RFID / Wi-Fi tag | `lvlWorkers()`, `blocks()` |
| CCTV | RTSP / WebRTC | `camHTML()` |

Rekomendasi: ambil data lewat `fetch`/WebSocket, simpan ke state `S`, lalu panggil `renderAll()`.

## Keterbatasan

- Data bersifat simulasi sehingga tidak boleh dipakai untuk pengambilan keputusan operasional.
- Tidak ada penyimpanan permanen: perubahan seperti flag blok, status unit, dan notifikasi hilang saat halaman dimuat ulang.
- Tidak ada autentikasi atau kontrol akses pengguna.
- Tombol "Share" hanya menyalin URL halaman saat ini.
- Panel CCTV hanya tampilan simulasi tanpa umpan video nyata.

## Referensi Publik (Skala Angka)

- Kapasitas nameplate GBC 150 kt/hari, DMLZ 80 kt/hari, dan Big Gossan 7,5 kt/hari: makalah Mass Mining 2024 ([papers.acg.uwa.edu.au](https://papers.acg.uwa.edu.au/d/2435_O-04/O-04.pdf)).
- Pemulihan bertahap GBC setelah mud flow 8 September 2025: [mining.com](https://www.mining.com/?p=1206574).
- Struktur tambang: [SEC Technical Report Summary](https://www.sec.gov/Archives/edgar/data/831259/000083125922000012/a2021trsptfi-exhibit.htm).

## Lisensi

Belum ditentukan. Tambahkan berkas `LICENSE` sesuai kebutuhan proyek Anda.
