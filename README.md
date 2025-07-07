# 🚀 Pentaho Server (Community Edition) with Docker

Menjalankan **Pentaho BI Server CE 9.4.6** secara lokal menggunakan **Docker** dan **PostgreSQL** sebagai metadata repository.

---

## 📦 Kebutuhan

- Docker
- Docker Compose
- Koneksi internet (untuk menarik image pertama kali)

---

## ▶️ Cara Menjalankan# 🚀 Pentaho Server (Community Edition) with Docker

Menjalankan **Pentaho BI Server CE 9.4.6** secara lokal menggunakan **Docker** dan **PostgreSQL** sebagai metadata repository.

---

## 📦 Kebutuhan

- Docker
- Docker Compose
- Koneksi internet (untuk menarik image pertama kali)

---

## ▶️ Cara Menjalankan

Masuk ke folder:
```bash
cd Pentaho
docker compose up -d -- build
```

## 🤗 for Mac

kalau muncul peringatan seperti  ! pentaho-server The requested image's platform (linux/amd64) does not match the detected host platform (linux/arm64/v8) and no specific platform was requested 0.0s
artinya kamu menjalankan Docker di Mac M1/M2 (chip ARM64), tapi image pentaho-server menggunakan platform amd64. Docker bisa tetap menjalankannya lewat emulasi (qemu), tapi bisa lambat atau bermasalah tergantung image-nya.

Ganti docker-compose.yml seperti berikut:
```bash
services:
  pentaho-server:
    image: oliveiha/pentaho-server:9.4.6_openjdk8
    platform: linux/amd64         # ⬅️ Tambahkan ini untuk mendukung Mac M1/M2
    container_name: pentaho-server
    ports:
      - "8080:8080"
    environment:
      DB_HOST: db
      DB_USER: postgres
      DB_PASS: password
      DB_PORT: 5432
      TIMEZONE: "Asia/Jakarta"
      LOCALE: "en_US.UTF-8 UTF-8"
    volumes:
      - ./pentaho/solution:/opt/pentaho/pentaho-server/pentaho-solutions
      - ./logs:/opt/pentaho/pentaho-server/logs
    depends_on:
      - db
    restart: unless-stopped

  db:
    image: postgres:14-alpine
    platform: linux/amd64         # ⬅️ Tambahkan ini juga untuk database agar aman
    container_name: pentaho-db
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
      POSTGRES_DB: pentaho
    volumes:
      - ./postgres/data:/var/lib/postgresql/data
    restart: unless-stopped
```
Kemudian jalankan lagi
```bash
docker compose up -d
```

Akses aplikasi:

🌐 http://localhost:8080/pentaho

Login default:
Username: admin
Password: password

📂 Penjelasan Folder
Folder	Fungsi
pentaho/solution/	Tempat menyimpan file dashboard/report Pentaho
postgres/data/	Volume data untuk PostgreSQL (metadata repository)
logs/	Menyimpan log dari Pentaho Server

🧼 Menghentikan Server
```bash
docker compose down
```
👨‍💻 Author
Chrisella inspirate with Elfan Pradita

Masuk ke folder:

```bash
cd pentaho-docker
docker compose up -d

Akses aplikasi:

🌐 http://localhost:8080/pentaho

Login default:

Username: admin

Password: password

📂 Penjelasan Folder
Folder	Fungsi
pentaho/solution/	Tempat menyimpan file dashboard/report Pentaho
postgres/data/	Volume data untuk PostgreSQL (metadata repository)
logs/	Menyimpan log dari Pentaho Server

🧼 Menghentikan Server
bash
Copy
Edit
docker compose down
👨‍💻 Author
Built with ❤️ by Elfan Pradita