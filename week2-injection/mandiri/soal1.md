1. Soal = retrieve hidden data
2. Link Soal = https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data
3. Jawaban =

Langkah pengerjaan: 
- Pilih salah satu kategori untuk memunculkan path `category` lalu klik kanan dan send to repeater
<img width="1278" height="1557" alt="image" src="https://github.com/user-attachments/assets/fa50f4d2-95e7-49e7-bbe1-2f5ec9c0881a" />
Masukkan Payload yang dibutuhkan yaitu `GET /filter?category='+OR+1=1-- HTTP/2` dan produk yang tidak ditampilkan akan muncul pada response, lab akan dinyatakan selesai
<img width="2019" height="1205" alt="image" src="https://github.com/user-attachments/assets/4ab253ac-9ab5-457c-bf81-36fe771c257e" />
