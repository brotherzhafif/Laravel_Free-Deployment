# 🚀 Cara Deploy Laravel 12 ke Vercel (Serverless)

Dokumentasi ini menjelaskan langkah-langkah deploy Laravel ke Vercel menggunakan pendekatan **serverless** dan konfigurasi `vercel.json`.

## ✅ Langkah-Langkah Deploy

### 1. Copy Folder

Copy semua folder dan file yang dibutuhkan ke dalam project Laravel Anda. Termasuk:

- `/api/index.php` sebagai entry point untuk Vercel
- `vercel.json` dan `.vercelignore`
- Struktur Laravel seperti biasa (`app`, `routes`, `public`, dll)

---

### 2. Publish Aset (Jika Menggunakan Library)

Jika Anda menggunakan library seperti **Livewire** atau **Filament**, lakukan publish asset ke folder `public/`:

```bash
php artisan livewire:publish --assets
php artisan storage:link
```

Jika ada library lain, lakukan publish ke public juga.

---

### 3. Konfigurasi `vercel.json`

File `vercel.json` digunakan untuk memberi tahu Vercel bagaimana menangani routing dan lingkungan serverless.

#### Contoh `vercel.json`:

```json
{
  "version": 2,
  "framework": null,
  "functions": {
    "api/index.php": {
      "runtime": "vercel-php@0.7.3"
    }
  },
  "routes": [
    {
      "src": "/(.*\\.(?:css|js|png|jpg|jpeg|gif|svg|ico|ttf|woff|woff2|eot|otf|webp|avif))$",
      "dest": "/public/$1"
    },
    {
      "src": "/livewire/update",
      "dest": "/api/index.php"
    },
    {
      "src": "/livewire/(.*)",
      "dest": "/public/vendor/livewire/$1"
    },
    {
      "src": "/storage/(.*)",
      "dest": "/public/storage/$1"
    },
    {
      "src": "/favicon.ico",
      "dest": "/public/favicon.ico"
    },
    {
      "src": "/(.*)",
      "dest": "/api/index.php"
    }
  ],
  "env": {
    "APP_NAME": "Laravel",
    "APP_ENV": "local",
    "APP_KEY": "generated-key-here",
    "APP_DEBUG": "true",
    "APP_URL": "https://link-to-your-app.vercel.app",
    "APP_CONFIG_CACHE": "/tmp/config.php",
    "APP_EVENTS_CACHE": "/tmp/events.php",
    "APP_PACKAGES_CACHE": "/tmp/packages.php",
    "APP_ROUTES_CACHE": "/tmp/routes.php",
    "APP_SERVICES_CACHE": "/tmp/services.php",
    "VIEW_COMPILED_PATH": "/tmp",
    "CACHE_DRIVER": "array",
    "LOG_CHANNEL": "stderr"
  },
  "outputDirectory": "public"
}
```

#### Penjelasan:

- **src** = lokasi URL yang diminta oleh client (di web)
- **dest** = lokasi file di codebase lokal/server
- **routes**: urutan penting, pastikan route catch-all (`/(.*)`) berada di bagian paling bawah
- **env**: tempat menaruh variabel seperti APP_KEY, DB, dll

  - **APP_ENV WAJIB** diset ke `local` agar Laravel berjalan baik di lingkungan serverless

---

### 4. Environment Variable

Dimasukkan ke bagian `env` di dalam `vercel.json`. Contoh:

```json
"env": {
  "APP_ENV": "local",
  "APP_KEY": "base64:...",
  ...
}
```

- Jangan lupa generate APP_KEY:

```bash
php artisan key:generate --show
```

---

## 📁 Struktur Folder

Pastikan struktur folder project Anda seperti ini:

```
laravel-project/
├── api/
│   └── index.php            # Entry point untuk Vercel
├── public/                  # Asset publik
│   ├── storage/             # Link ke storage Laravel
│   └── ...
├── app/
├── routes/
├── vendor/
├── .vercelignore
├── vercel.json
├── composer.json
└── ...
```

---

## ⚡ Apa Itu _Serverless_?

**Serverless** adalah pendekatan di mana aplikasi berjalan tanpa perlu mengelola server fisik atau virtual secara langsung.

### Karakteristik:

- Fungsi hanya berjalan saat dibutuhkan (on-demand)
- Tidak ada server "always-on"
- Skala otomatis
- Cocok untuk aplikasi sederhana atau menengah

### Batasan:

- **Tidak cocok untuk proses background seperti queue, websocket, atau long-running process**
- **Cold start**: waktu respon pertama bisa sedikit lambat
- Tidak bisa menyimpan data state/file di memory server (harus gunakan database atau cache)

---

## 🚀 Deploy ke Vercel

1. Login dan install [Vercel CLI](https://vercel.com/docs/cli):

   ```bash
   npm i -g vercel
   ```

2. Jalankan perintah deploy:

   ```bash
   vercel --prod
   ```

3. Atau hubungkan dengan GitHub repo, dan biarkan Vercel auto deploy.

---

## 📌 Catatan Tambahan

- Pastikan semua asset dan storage sudah di-link ke public
- Jangan pakai APP_ENV=production karena Laravel akan aktifkan cache dan strict mode
- Untuk fitur queue/cron, gunakan layanan tambahan seperti Laravel Vapor atau Vercel cron

---

## 🔗 Referensi

- [https://vercel.com/docs](https://vercel.com/docs)
- [https://laravel.com/docs](https://laravel.com/docs)
- [https://github.com/vercel-community/vercel-php](https://github.com/vercel-community/vercel-php)
