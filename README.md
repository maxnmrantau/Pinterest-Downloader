# Pinterest Downloader (Gambar & Video)
<img width="275" height="439" alt="image" src="https://github.com/user-attachments/assets/420a37f3-65e2-4f66-a56a-97f2df30309c" />


Ekstensi Chrome (Manifest V3) yang menambahkan **tombol download** di setiap gambar dan video Pinterest. Cukup arahkan kursor ke pin, klik tombol merah, dan file langsung terunduh — tanpa membuka halaman pin dulu.

## Fitur

- Tombol download muncul saat kursor berada di atas gambar atau video, baik di feed, hasil pencarian, maupun halaman detail pin.
- Gambar diunduh dalam kualitas **asli (originals)**. Jika tidak tersedia, otomatis turun ke 1200px atau 736px.
- Video diunduh sebagai **.mp4**. Jika file mp4 tidak tersedia, video HLS diunduh dengan cara menggabungkan semua segmennya langsung di browser.
- Indikator status di tombol (loading, berhasil, gagal) dan notifikasi kecil di bawah layar.
- Tidak mengirim data apa pun ke server pihak ketiga. Semua proses berjalan di browser kamu.

## Instalasi

Ekstensi ini belum ada di Chrome Web Store, jadi dipasang secara manual:

1. Unduh repo ini (**Code → Download ZIP**) lalu ekstrak, atau clone:
   ```bash
   git clone https://github.com/maxnmrantau/pinterest-downloader.git
   ```
2. Buka `chrome://extensions` di Chrome (atau browser berbasis Chromium seperti Edge dan Brave).
3. Aktifkan **Developer mode** di pojok kanan atas.
4. Klik **Load unpacked**, lalu pilih folder `pinterest-downloader` (folder yang berisi `manifest.json`).
5. Buka atau muat ulang halaman Pinterest.

## Cara pakai

1. Buka [pinterest.com](https://www.pinterest.com) dan login seperti biasa.
2. Arahkan kursor ke gambar atau video. Tombol download bulat berwarna merah akan muncul:
   - di **feed**: pojok kiri bawah gambar,
   - di **tampilan besar** (pin yang sedang dibuka): pojok kanan atas.
3. Klik tombolnya. Ikon berubah menjadi spinner, lalu centang hijau saat unduhan dimulai.

Hasil unduhan disimpan di folder Downloads. Gambar dan video mp4 masuk ke subfolder `Pinterest/`, sedangkan video hasil gabungan HLS masuk langsung ke folder Downloads.

## Cara kerja singkat

| Berkas | Fungsi |
| --- | --- |
| `manifest.json` | Konfigurasi ekstensi (MV3), izin `downloads`, dan akses ke `*.pinterest.com` serta `*.pinimg.com`. |
| `content.js` | Mendeteksi gambar/video di bawah kursor, menampilkan tombol, mencari URL media, dan menggabungkan segmen HLS bila perlu. |
| `content.css` | Tampilan tombol dan notifikasi. |
| `background.js` | Service worker yang memilih URL yang valid lalu memulai unduhan lewat `chrome.downloads`. |
| `icons/` | Ikon ekstensi. |

**Gambar:** URL thumbnail diubah ke versi `originals`, lalu dicek satu per satu sampai ada yang valid.

**Video:** URL dicari berurutan dari data pin (API internal Pinterest), data halaman pin, dan URL yang sedang dimuat player. Jika hanya ada stream HLS (`.m3u8`), ekstensi mencoba menebak file mp4-nya, dan bila gagal, mengunduh serta menggabungkan segmen HLS.

## Batasan

- Hanya aktif di domain `*.pinterest.com`. Domain regional seperti `pinterest.co.uk` atau `pinterest.fr` belum didukung (bisa ditambahkan di `manifest.json` pada bagian `matches` dan `host_permissions`).
- Hanya mengenali gambar dan video berukuran minimal sekitar 140 px, supaya avatar dan ikon kecil diabaikan.
- Satu unduhan diproses dalam satu waktu.
- Unduhan HLS ditampung di memori browser sebelum disimpan, jadi video yang sangat panjang atau berukuran besar bisa memakan banyak RAM.
- Video HLS yang **terenkripsi** tidak didukung. Jika audio dan video dipisah di stream HLS, hasil gabungan bisa tanpa suara.
- Pin video yang di feed hanya tampil sebagai gambar sampul kadang terdeteksi sebagai gambar. Buka pin-nya dulu agar video terdeteksi dengan benar.
- Pinterest bisa membatasi permintaan yang terlalu banyak dalam waktu singkat. Jika unduhan mulai gagal terus, beri jeda sebentar.
- Pinterest sering mengubah struktur halaman dan API internalnya, sehingga ekstensi ini bisa berhenti bekerja sewaktu-waktu.

## Troubleshooting

- **Tombol tidak muncul:** muat ulang ekstensi di `chrome://extensions`, lalu **tutup dan buka lagi** tab Pinterest. Memuat ulang ekstensi saja tidak mengganti script di tab yang sudah terbuka.
- **Memastikan versi yang aktif:** hover tombol download untuk melihat versinya di tooltip, atau buka Console (F12) dan cari pesan `[PinDL] vX.Y.Z aktif`.
- **Video gagal diunduh:** buka Console (F12) lalu klik download lagi. Log `[PinDL] video tidak ditemukan. Debug: {...}` menunjukkan sumber URL mana yang kosong. Sertakan log ini jika membuka issue.

## Disclaimer

Proyek ini dibuat untuk keperluan pribadi dan edukasi. Konten di Pinterest dilindungi hak cipta milik pembuat aslinya. Gunakan hanya untuk konten milik sendiri atau yang kamu punya izin untuk menyimpannya, dan patuhi [Ketentuan Layanan Pinterest](https://policy.pinterest.com/terms-of-service). Proyek ini tidak berafiliasi dengan Pinterest.

## Lisensi

[MIT](LICENSE)
