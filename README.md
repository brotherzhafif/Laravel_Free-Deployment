# 🚀 Laravel 12 Deployment Examples: Vercel (Serverless) & Render.com (Docker)

Repositori ini berisi contoh dan template untuk men-deploy Laravel 12 ke dua platform populer:

- **Vercel** (serverless, PHP runtime)
- **Render.com** (Docker, Nginx + PHP-FPM)

---

## 📦 Struktur Folder

```
PHP_Serverless-Deployment/
├── vercel/         # Contoh & template deploy Laravel ke Vercel (serverless)
├── render.com/     # Contoh & template deploy Laravel ke Render.com (Docker)
```

---

## 1️⃣ Deploy Laravel ke Vercel (Serverless)

- **Lokasi:** [`vercel/`](./vercel/)
- **Fitur:** Deploy Laravel tanpa server, menggunakan PHP runtime Vercel.
- **Cocok untuk:** Demo, prototipe, aplikasi ringan tanpa kebutuhan background job.
- **Dokumentasi:** [`vercel/readme.md`](./vercel/readme.md)

**Highlight:**

- Routing diatur lewat `vercel.json`
- Entry point: `api/index.php`
- Asset publik di `public/`
- Tidak cocok untuk queue, websocket, atau proses background

---

## 2️⃣ Deploy Laravel ke Render.com (Docker)

- **Lokasi:** [`render.com/`](./render.com/)
- **Fitur:** Deploy Laravel menggunakan Docker, Nginx, dan PHP-FPM.
- **Cocok untuk:** Development, testing, demo, atau aplikasi kecil.
- **Dokumentasi:** [`render.com/README.md`](./render.com/README.md)

**Highlight:**

- Menggunakan `Dockerfile` berbasis `richarvey/nginx-php-fpm`
- Konfigurasi Nginx custom di `conf/nginx/nginx-site.conf`
- Script otomatis deploy di `scripts/00-laravel-deploy.sh`
- Asset management dengan `webpack.mix.js`

---

## ⚖️ **Render.com vs Vercel**

| Fitur                              | **Render.com**                                | **Vercel** (Serverless)            |
| ---------------------------------- | --------------------------------------------- | ---------------------------------- |
| 🧠 Arsitektur                      | **Traditional server (container)**            | **Serverless (function-based)**    |
| 🌐 Web Service                     | Menjalankan **server penuh** (PHP, Node, dll) | Menggunakan **fungsi (API route)** |
| 🔁 Proses background (queue, cron) | ✅ Bisa pakai worker                           | ❌ Terbatas, butuh workaround       |
| 🧩 Laravel Support                 | ✅ Native Laravel (Nginx + PHP-FPM)            | ⚠️ Butuh penyesuaian `vercel.json` |
| 💡 State/Session di memory         | ✅ Bisa                                        | ❌ Tidak bisa (stateless)           |
| 🧊 Cold Start                      | ❌ Tidak ada                                   | ✅ Ada (pertama kali akses lambat)  |
| 💵 Biaya                           | Flat atau usage-based                         | Usage-based per fungsi             |


## 📚 Referensi

- [Laravel Docs](https://laravel.com/docs)
- [Vercel Docs](https://vercel.com/docs)
- [Render.com Docs](https://render.com/docs)
- [vercel-php Runtime](https://github.com/vercel-community/vercel-php)

---

**Pilih folder sesuai platform yang ingin Anda gunakan, lalu ikuti README di masing-masing subfolder.**
