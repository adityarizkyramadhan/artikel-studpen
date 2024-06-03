---
title: "Deployment pada Server"
author: "Aditya Rizky Ramadhan"
toc: true
number-sections: true
highlight-style: pygments
format:
  html:
    code-fold: true
    html-math-method: katex
  pdf:
    geometry:
      - top=30mm
      - left=20mm
  docx: default
---

# Deployment pada Server

Deployment adalah kegiatan yang bertujuan untuk menyebarkan aplikasi yang telah dikerjakan oleh programmer. Cara penyebarannya sangat beragam, tergantung dari jenis aplikasinya, misalnya:

- Web, deploy ke hosting pada server.
- Mobile, deployment ke Playstore atau Appstore
- Service Provider, deployment API (backend) ke server.


# Deployment API pada VPS

VPS (Virtual Private Server) adalah server virtual yang berjalan diatas server fisik. VPS ini biasanya digunakan untuk deployment aplikasi web, service provider, dan lainnya. Keuntungan dari menggunakan VPS antara lain:

- Dapat diakses dari manapun selama terhubung ke internet.
- Dapat diatur sesuai kebutuhan.
- Dapat dijadikan sebagai server untuk aplikasi yang membutuhkan resource yang besar.

Kekurangan dari VPS antara lain:

- Memerlukan biaya sewa.
- Memerlukan pengetahuan tentang administrasi server.
- Memerlukan waktu untuk konfigurasi.
- Memerlukan pemahaman tentang keamanan server.
- Memerlukan pemahaman tentang backup dan restore.
- Memerlukan pemahaman tentang monitoring.

## Langkah-langkah Deployment

Berikut adalah langkah-langkah deployment API pada VPS secara sederhana:

### 1. Persiapan

- Siapkan VPS yang akan digunakan.
- Siapkan domain yang akan digunakan. Dapat menggunakan domain yang dibeli dan ditransfer ke cloudflare.
- Siapkan aplikasi yang akan di-deploy.


### 2. Instalasi Nginx

Nginx adalah web server yang ringan dan cepat. Nginx digunakan sebagai reverse proxy server untuk melayani aplikasi yang akan di-deploy.

```bash
sudo apt update
sudo apt install nginx
```

### 3. Konfigurasi Nginx

Konfigurasi Nginx dilakukan pada file `/etc/nginx/sites-available/namadomain`. Berikut adalah contoh konfigurasi Nginx untuk reverse proxy ke aplikasi yang berjalan pada port 8000:

```nginx
server {
        server_name  hello.adityaariizkyy.my.id;
        location / {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_pass http://127.0.0.0:5757;
        }


        listen 80;
        listen [::]:80;
}
```

Setelah itu lakukan link konfigurasi ke `/etc/nginx/sites-enabled`:

```bash
sudo ln -s /etc/nginx/sites-available/namadomain /etc/nginx/sites-enabled/
```

Lakukan test konfigurasi Nginx:

```bash
sudo nginx -t
```

Jika konfigurasi sudah benar, restart Nginx:

```bash
sudo systemctl restart nginx
```

### 4. Instalasi PM2

PM2 adalah process manager untuk Node.js. PM2 digunakan untuk menjalankan aplikasi Node.js secara terus menerus.

```bash
sudo npm install -g pm2
```

### 5. Menjalankan Aplikasi

Build aplikasi golang:

```bash
go build -o main
```

Jalankan aplikasi menggunakan PM2:

```bash
pm2 start main
```

### 6. Buat Menjadi HTTPS dengan Certbot

Certbot adalah aplikasi yang digunakan untuk membuat sertifikat SSL. Sertifikat SSL digunakan untuk mengamankan komunikasi antara client dan server.

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx
```

