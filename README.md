## ▶️ Cara Menjalankan 🚀 Pentaho Server (Community Edition) with Docker

Menjalankan **Pentaho BI Server CE 9.4.6** secara lokal menggunakan **Docker** dan **PostgreSQL** sebagai metadata repository.

---

## 📦 Kebutuhan

- Docker
- Docker Compose
- Koneksi internet (untuk menarik image pertama kali)

---

## 📂 Struktur Proyek
```
.
├── docker-compose.yml          # Konfigurasi utama
├── postgres/
│   └── data/                  # Data persisten PostgreSQL
├── pentaho/
│   ├── solution/              # Direktori solusi Pentaho
│   └── logs/                  # Log server Pentaho
└── mysql/
    └── data/                  # Data persisten MySQL
```

---

## ▶️ Cara Menjalankan

Masuk ke folder:
```bash
cd Pentaho
docker compose up -d -- build
```
---

## 🤗 for Mac

kalau muncul peringatan seperti  
! pentaho-server The requested image's platform (linux/amd64) does not match the detected host platform (linux/arm64/v8) and no specific platform was requested 0.0s

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

---

## 🧼 Menghentikan Server
```bash
docker compose down
```

---

## Mysql
setting docker-compose.yml seperti berikut:
```bash
services:
  pentaho-server:
    image: oliveiha/pentaho-server:9.4.6_openjdk8
    platform: linux/amd64
    container_name: pentaho-server
    ports:
      - "8080:8080"
    environment:
      DB_HOST: db          # PostgreSQL (default)
      MYSQL_HOST: mysql    # Tambahkan ini untuk MySQL
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
      - mysql             # Tambahkan dependency ke MySQL
    restart: unless-stopped

  db:
    image: postgres:14-alpine
    platform: linux/amd64
    container_name: pentaho-db
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
      POSTGRES_DB: pentaho
    volumes:
      - ./postgres/data:/var/lib/postgresql/data
    restart: unless-stopped

  mysql:
    image: mysql:8.0
    platform: linux/amd64
    container_name: pentaho-mysql
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: pentaho_mysql
      MYSQL_USER: pentaho
      MYSQL_PASSWORD: pentahopass
    volumes:
      - ./mysql/data:/var/lib/mysql
    ports:
      - "3306:3306"
    restart: unless-stopped
```

kemudian jalankan ulang docker dengan
```bash
docker compose down
docker compose up -d
```

nyalakan xampp

## 🛠️ Configuration

Database Connections
```
MySQL (Default)
Host: mysql
Port: 3306
Database: pentaho_mysql
User: root
```
```
PostgreSQL
Host: db
Port: 5432
Database: pentaho
User: postgres / password
```

---

## Menambahkan Data Source di Pentaho
Navigasi ke Administration → Data Sources
Untuk MySQL:
```
Nama Koneksi: MySQL_Docker
Tipe: MySQL
Host: mysql
Database: pentaho_mysql
user: admin
Password: password
``` 

### Test Ping pentaho ke database
pada terminal, masukan:
```bash
docker exec -it pentaho-server ping mysql
```

## Verif akhir
buat tabel contoh MySQL:
```
USE pentaho_mysql;
CREATE TABLE test (id INT, name VARCHAR(50));
INSERT INTO test VALUES (1, 'Contoh Data');
```

Coba koneksi lagi di Pentaho dengan query:
```bash
SELECT * FROM test;
```

Jika gagal, cek log Pentaho:
```bash
docker logs pentaho-server | grep -i "mysql"
```

## 🔧 Troubleshooting
Masalah Umum
**1. "Connection failed" error**
- Verifikasi container berjalan: docker ps
- Cek log MySQL: docker logs pentaho-mysql
- Pastikan driver terinstall:
```bash
docker cp mysql-connector-java-8.0.28.jar pentaho-server:/opt/pentaho/pentaho-server/tomcat/lib/
docker-compose restart pentaho-server
```

**2. Database tidak dapat diakses**
Tes koneksi MySQL
```bash
docker exec -it pentaho-mysql mysql -u pentaho -ppentahopass pentaho_mysql
```

---

## 🧹 Pembersihan
Untuk menghentikan dan menghapus semua container:
```bash
docker-compose down -v
```

#### 👨‍💻 Author
Chrisella inspirate with Elfan Pradita