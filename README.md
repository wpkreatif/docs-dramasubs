# 📖 Cara Install Dramasubs

Panduan lengkap instalasi **Dramasubs** dari awal hingga aplikasi siap digunakan.

---

## 📁 File Tree

```
dramasubs/
├── app/
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── Admin/
│   │   │   │   └── SettingsController.php    # Admin panel controller
│   │   │   ├── BrowseController.php          # Halaman browse/arsip
│   │   │   ├── Controller.php                # Base controller
│   │   │   ├── EpisodeController.php         # Player episode
│   │   │   ├── FilmController.php            # Detail film
│   │   │   ├── HomeController.php            # Halaman utama
│   │   │   ├── ManifestController.php        # PWA manifest
│   │   │   ├── MeloloController.php          # Melolo section
│   │   │   ├── NetshortController.php        # Netshort section
│   │   │   ├── SearchController.php          # Pencarian
│   │   │   └── SitemapController.php         # Sitemap generator
│   │   └── Middleware/
│   │       └── AdminMiddleware.php           # Proteksi admin panel
│   ├── Models/
│   ├── Providers/
│   └── Services/
│       ├── DramaBoxService.php               # API DramaBox
│       ├── MeloloService.php                 # API Melolo
│       └── NetshortService.php               # API Netshort
├── bootstrap/
├── config/
├── database/
├── public/
│   ├── css/
│   ├── js/
│   ├── uploads/                              # Upload logo, favicon, dll
│   ├── .htaccess
│   ├── index.php
│   └── robots.txt
├── resources/
│   ├── css/
│   ├── js/
│   └── views/
│       ├── admin/
│       │   ├── login.blade.php               # Halaman login admin
│       │   └── settings.blade.php            # Halaman settings admin
│       ├── layouts/
│       │   └── app.blade.php                 # Layout utama
│       ├── browse.blade.php
│       ├── episode.blade.php
│       ├── film.blade.php
│       ├── history.blade.php
│       ├── home.blade.php
│       ├── privacy.blade.php
│       ├── search.blade.php
│       └── terms.blade.php
├── routes/
│   └── web.php                               # Semua route aplikasi
├── storage/
├── .env                                      # Konfigurasi environment
├── .env.example                              # Template environment
├── composer.json
├── package.json
├── vite.config.js
└── Cara-Install.md                           # File ini
```

---

## 📋 System Requirements

| Software | Versi Minimum |
| -------- | ------------- |
| PHP      | 8.4          |
| Composer | 2.x           |
| Node.js  | 20+           |
| NPM      | 9+            |

### Ekstensi PHP yang Dibutuhkan

- `php-mbstring`
- `php-xml`
- `php-curl`
- `php-sqlite3` (default) atau `php-mysql`
- `php-zip`
- `php-bcmath`

---

## 🚀 Langkah Instalasi

### 1. Download File

```bash
Masuk ke member-area : https://akses.toko.im
Masuk Menu Access dan download file yang tersedia
```

### 2. Generate Application Key

```bash
Jika hosting tidak mempunyai terminal, lewati langkah ini
php artisan key:generate
```

### 3. Konfigurasi `.env`

Buka file `.env` dan sesuaikan:

```env
APP_NAME=Dramasubs
APP_URL=https://domain-kamu.com
APP_ENV=production
APP_DEBUG=false

# Database (default SQLite)
DB_CONNECTION=sqlite

# Session & Cache
SESSION_DRIVER=file
CACHE_STORE=file
```

> **💡 Tip:** Jika mau menggunakan Redis, ubah konfigurasi berikut:
>
> ```env
> CACHE_STORE=redis
> 
> REDIS_CLIENT=phpredis
> REDIS_HOST=127.0.0.1
> REDIS_PASSWORD=Password_redis_Anda
> REDIS_PORT=6379
> ```

### 9. Buat Storage Link

**Via Terminal:**

```bash
php artisan storage:link
```

**Via Browser (Shared Hosting tanpa akses terminal):**

Login ke admin panel terlebih dahulu, lalu akses:

```
https://domain-kamu.com/admin/storage-link
```

### 4. Set Permissions (Linux/VPS)

```bash
chmod -R 775 storage bootstrap/cache
chown -R www-data:www-data storage bootstrap/cache
```

---

## ▶️ Menjalankan Aplikasi

### Development (Lokal)

```bash
php artisan serve
```

Akses di browser: `http://localhost:8000`

### Production (VPS/Shared Hosting)

Arahkan document root web server (Nginx/Apache) ke folder `public/`.

**Contoh konfigurasi Nginx:**

```nginx
server {
    listen 80;
    server_name domain-kamu.com;
    root /var/www/dramasubs/public;
    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass unix:/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

```apache
<IfModule mod_rewrite.c>
    RewriteEngine On

    # Menangani Authorization Header
    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

    # Jika file atau direktori tidak ada di root, arahkan ke public
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]

    # Mengalihkan semua request ke folder public/index.php
    RewriteRule ^(.*)$ public/$1 [L]
</IfModule>
```

---

## 🔐 Admin Panel

### Akses Login

```
URL:      https://domain-kamu.com/admin/login
Password: login
```

### Fitur Admin Panel

| Menu             | Deskripsi                                    |
| ---------------- | -------------------------------------------- |
| **Settings**     | Konfigurasi nama, URL, logo, SEO, ads, PWA   |
| **Clear Cache**  | Hapus cache aplikasi (`/admin/clear-cache`)  |
| **Optimize**     | Optimasi aplikasi (`/admin/optimize`)        |
| **Config Clear** | Hapus config cache (`/admin/config-clear`)   |
| **View Clear**   | Hapus view cache (`/admin/view-clear`)       |
| **Storage Link** | Buat symlink storage (`/admin/storage-link`) |

### Ganti Password Admin

Melalui admin panel, di bagian **Settings** → masukkan password baru di field **New Password** → **Simpan**.

---

## 🗺️ Sitemap

### Generate Sitemap

```bash
php artisan sitemap:generate
```

Output:

```
🗺️  Generating sitemap...
📦 Fetching DramaBox posts...
  ✓ 119 DramaBox posts
📦 Fetching Netshort posts...
  ✓ 55 Netshort posts
📦 Fetching Melolo posts...
  ✓ 48 Melolo posts

✅ Sitemap generated! 512 URLs → public/sitemap.xml
```

File sitemap akan tersimpan di `public/sitemap.xml` dan bisa diakses di:

```
https://domain-kamu.com/sitemap.xml
```

### Auto-Generate via Cron Job

Sitemap otomatis ter-generate setiap **6 jam** melalui Laravel Scheduler.

Tambahkan cron entry di server:

```bash
crontab -e
```

Tambahkan baris ini:

```cron
* * * * * cd /path/to/dramasubs && php artisan schedule:run >> /dev/null 2>&1
```

> **💡 Tip:** Ganti `/path/to/dramasubs` dengan path absolut project kamu, misalnya `/var/www/dramasubs`.

### Daftarkan Sitemap

Daftarkan URL sitemap di:

- [Google Search Console](https://search.google.com/search-console)
- [Bing Webmaster Tools](https://www.bing.com/webmasters)

---

## 🏗️ Deploy ke Shared Hosting (cPanel)

1. Upload semua file ke folder di luar `public_html`
2. Upload isi folder `public/` ke `public_html/`
3. Edit `public_html/index.php`:

```php
// Ubah path sesuai lokasi project
require __DIR__.'/../dramasubs/vendor/autoload.php';
$app = require_once __DIR__.'/../dramasubs/bootstrap/app.php';
```

4. Pastikan file `.env` sudah dikonfigurasi
5. Akses `https://domain-kamu.com/admin/login` → Login
6. Klik **Storage Link** di admin panel untuk buat symlink

---

## ❓ Troubleshooting

| Masalah                   | Solusi                                                     |
| ------------------------- | ---------------------------------------------------------- |
| Halaman blank / 500 error | Cek `storage/logs/laravel.log`, pastikan permission benar  |
| CSS/JS tidak muncul       | Jalankan `npm run build` ulang                             |
| Gambar tidak tampil       | Buat storage link via `/admin/storage-link`                |
| Login admin gagal         | Pastikan `ADMIN_PASSWORD` di `.env` berisi hash yang benar |
| Cache tidak update        | Akses `/admin/clear-cache` dan `/admin/config-clear`       |
