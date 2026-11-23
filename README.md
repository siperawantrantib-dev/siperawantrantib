# SIPERAWAN TRANTIB — Frontend Statis + Backend Apps Script

Aplikasi ini dibuat agar **HTML tidak dijalankan di Google Apps Script**. File `index.html` dijalankan sebagai website statis (misalnya GitHub Pages). Google Apps Script hanya menjadi API/backend yang membaca dan menulis Google Spreadsheet serta menyimpan foto ke Google Drive.

## Struktur paket
- `index.html` — seluruh tampilan dan JavaScript frontend, termasuk lambang resmi Satpol PP yang sudah disematkan langsung; dipublikasikan ke GitHub Pages/hosting statis.
- `code.gs` — API backend saja; ditempel ke project Google Apps Script yang terhubung dengan Spreadsheet.

## 1. Siapkan backend Google Apps Script
1. Buka Google Spreadsheet tujuan.
2. Pilih **Extensions → Apps Script**.
3. Buka file `Code.gs` dan ganti isinya dengan seluruh isi `code.gs` dari paket ini.
4. Tidak perlu membuat file HTML bernama `index` di Apps Script.
5. Simpan, pilih fungsi `setup`, lalu klik **Run** satu kali dan berikan izin akses Spreadsheet/Drive.
6. Pilih **Deploy → New deployment → Web app**.
7. Atur **Execute as: Me**. Atur **Who has access** sesuai kebijakan instansi (untuk uji coba dapat menggunakan akses yang mengizinkan pengguna aplikasi memanggil endpoint).
8. Klik **Deploy**, lalu salin URL Web App yang berakhiran `/exec`.

## 2. Pasang URL backend ke frontend
1. Buka `index.html` dengan editor teks.
2. Cari bagian `KONFIGURASI BACKEND`.
3. Ganti nilai berikut dengan URL `/exec` hasil deployment:
   ```js
   const APP_EXEC_URL = 'PASTE_URL_WEB_APP_APPS_SCRIPT_DI_SINI';
   ```
   Contoh:
   ```js
   const APP_EXEC_URL = 'https://script.google.com/macros/s/AKfycbxxxxxxxx/exec';
   ```
4. Simpan `index.html`.

## 3. Publikasikan frontend
1. Unggah `index.html` ke root repository GitHub.
2. Aktifkan **Settings → Pages → Deploy from a branch → main → /(root)**.
3. Buka URL GitHub Pages. Aplikasi berjalan di browser; bukan melalui URL `/exec`.

## Cara komunikasi
- Pembacaan data: JSONP melalui `doGet` Apps Script.
- Penulisan/hapus/impor data: POST JSON `text/plain` melalui `doPost`.
- Frontend tidak memakai `google.script.run` dan backend tidak menggunakan `HtmlService`.
- Sheet `TRANTIB` dan header disiapkan oleh `setup()`/API.
- Folder `FOTO_TRANTIB` dibuat otomatis saat foto pertama kali disimpan.

## Catatan keamanan dan penggunaan
- URL `/exec` di dalam `index.html` bukan rahasia; siapa pun yang dapat melihat kode frontend dapat membacanya. Batasi akses deployment dan tambahkan autentikasi/otorisasi backend sebelum memakai data sensitif atau membuka aplikasi ke publik.
- Setelah memperbarui `code.gs`, deploy **New version** pada deployment Apps Script yang sama. Bila URL deployment berubah, perbarui konstanta `APP_EXEC_URL` di `index.html`.
- Jangan membuat file HTML `index` di Apps Script. Apps Script hanya menjalankan `doGet`/`doPost` sebagai API.
