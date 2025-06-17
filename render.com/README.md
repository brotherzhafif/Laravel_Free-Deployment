# Deploy Laravel 12 (Dockerized) di Render.com (Gratis)

Panduan ini menjelaskan cara men-deploy Laravel 12 menggunakan Docker di Render.com (paket gratis).

## Langkah Awal

1. **Copy semua file di folder ini** (`Dockerfile`, `conf/nginx/nginx-site.conf`, `scripts/00-laravel-deploy.sh`, dsb) ke root project Laravel Anda.
2. Pastikan struktur project Anda seperti berikut:
   ```
   .
   ├── app/
   ├── bootstrap/
   ├── conf/
   │   └── nginx/
   │       └── nginx-site.conf
   ├── config/
   ├── database/
   ├── public/
   ├── resources/
   ├── routes/
   ├── scripts/
   │   └── 00-laravel-deploy.sh
   ├── storage/
   ├── Dockerfile
   ├── webpack.mix.js
   └── ...
   ```

## Penjelasan File

- **Dockerfile**  
  Menggunakan image `richarvey/nginx-php-fpm` untuk menjalankan Laravel dengan Nginx & PHP-FPM. File ini mengatur environment variable untuk Laravel production.

- **conf/nginx/nginx-site.conf**  
  Konfigurasi Nginx untuk Laravel, sudah dioptimasi untuk Render.com.

- **scripts/00-laravel-deploy.sh**  
  Script otomatis untuk install dependency, cache config/route, dan migrate database saat container start.

- **webpack.mix.js**  
  Konfigurasi Laravel Mix untuk compile asset JS & CSS.

## Setting di Render.com

1. **Buat Web Service baru**

   - Source: Hubungkan ke repo project Anda (GitHub/GitLab).
   - Environment: Docker
   - Build Command: _(kosongkan, Dockerfile sudah handle)_
   - Start Command: _(kosongkan, Dockerfile sudah handle)_

2. **Environment Variables**  
   Tambahkan variable berikut di Render Dashboard:

   - `APP_KEY` : (generate dengan `php artisan key:generate --show`)
   - `DB_CONNECTION`, `DB_HOST`, `DB_PORT`, `DB_DATABASE`, `DB_USERNAME`, `DB_PASSWORD` (isi sesuai database Anda)
   - `APP_ENV=production`
   - `APP_DEBUG=false`

3. **Storage Permission**  
   Render.com akan menjalankan container sebagai root, jadi permission biasanya sudah cukup. Jika perlu, tambahkan di script deploy:

   ```bash
   chmod -R 775 storage bootstrap/cache
   ```

4. **Database**  
   Render.com menyediakan PostgreSQL gratis. Atur `.env` sesuai kredensial database Render Anda.

## Build & Deploy

- Render.com akan otomatis build & deploy saat ada push ke repo.
- Script `00-laravel-deploy.sh` akan otomatis dijalankan saat container start.

## Troubleshooting

- Cek log di dashboard Render jika terjadi error.
- Pastikan `.env` sudah benar dan semua dependency sudah terinstall.

## Kelebihan Render.com (Gratis) dengan Docker

- **Mudah digunakan**: Proses deployment sederhana, cukup push ke repo dan Render akan build otomatis.
- **Mendukung Docker**: Bisa menjalankan aplikasi dengan environment yang sama seperti lokal.
- **Gratis**: Tersedia paket gratis untuk testing, development, atau demo.
- **Integrasi Git**: Otomatis build setiap ada perubahan di repository.
- **Custom Domain**: Mendukung custom domain walau di paket gratis.

## Kekurangan Render.com (Gratis) dengan Docker

- **Sleep Mode**: Service akan "tidur" jika tidak ada traffic selama 15 menit, sehingga akses pertama akan lambat (cold start).
- **Resource Terbatas**: CPU, RAM, dan storage sangat terbatas di paket gratis.
- **No Persistent Storage**: Data yang disimpan di disk akan hilang saat service restart/deploy.
- **Timeout**: Build dan deploy memiliki batas waktu tertentu.
- **Tidak Cocok untuk Production**: Hanya disarankan untuk development, testing, atau demo.

---

## 🔄 Kebutuhan Laravel: Request Berkala (Cronjob, Scheduler, dsb) ✅

Jika aplikasi Laravel kamu membutuhkan request berkala (misal: ping biar aplikasi tidak mati, auto-update, queue, scheduler, dsb), kamu bisa memanfaatkan fitur cron job di Render.

### ✅ Cara Menjaga Laravel di Render Tetap Aktif (Anti "Tidur")

Jika menggunakan Render Free Plan, aplikasi bisa "tidur" (sleep) jika tidak ada trafik selama 15 menit. Untuk mencegah ini:

#### 🔁 Opsi 1: Gunakan Cron Job di Render (Native Feature)

1. Masuk ke dashboard Render > klik tombol "New" > "Cron Job"
2. Isi form:
   - Name: `keep-awake`
   - Schedule: setiap 5 menit (misal: `*/5 * * * *`)
   - Command:
     ```
     curl https://your-app.onrender.com/
     ```
   - Environment: pilih sesuai kebutuhan

Ini akan melakukan request ke aplikasi kamu secara berkala sehingga aplikasi dianggap aktif dan tidak tidur.

#### 💡 Opsi 2: Gunakan Cron di Laravel (Task Scheduler)

Jika sudah mengaktifkan Laravel Scheduler (`php artisan schedule:run`), kamu juga bisa setup cron job di Render:

- Di bagian "Advanced" saat buat web service, tambahkan cron job:
  ```
  * * * * * cd /opt/render/project/src && php artisan schedule:run >> /dev/null 2>&1
  ```
- Ini khusus untuk task Laravel, bukan untuk keep-alive. Untuk menjaga aplikasi tetap hidup, tetap gunakan `curl` seperti opsi 1.

#### 🔒 Catatan Penting

- Free Plan di Render hanya tidur jika idle, tidak tidur jika aktif terus.
- Render tidak membatasi cron job selama tidak membebani sistem.
- Bisa pakai `curl`, `wget`, atau script Laravel sendiri untuk ping balik ke URL kamu.

#### Contoh Cron Job: Ping Aplikasi Tiap 5 Menit

```
curl -fsS https://your-app.onrender.com/ > /dev/null
```

Taruh di cron bawaan Render, atau bisa juga dari server luar (UptimeRobot, GitHub Actions, dsb).

#### 🚀 Kesimpulan

- Bisa pakai cron job di Render untuk menjaga Laravel tetap aktif (anti tidur).
- Jalankan `curl` ke URL aplikasi kamu secara berkala.
- Pastikan pakai `https://...` dan responsnya tidak error (200 OK cukup).

Jika butuh file setup lengkap untuk deploy Laravel ke Render (dengan cron + start command), silakan request!

---

**Selesai!**  
Aplikasi Laravel 12 Anda sekarang berjalan di Render.com dengan Docker.
