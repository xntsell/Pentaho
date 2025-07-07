# 🚀 Pentaho Server (Community Edition) with Docker

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