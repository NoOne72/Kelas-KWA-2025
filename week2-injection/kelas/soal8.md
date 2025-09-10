1. Soal = User Credentials
2. Link =
3. Jawaban =

### **Langkah-langkah Penyelesaian**

Tujuan dari tantangan ini adalah untuk mengekstrak data sensitif—terutama kredensial pengguna seperti email dan kata sandi—dari database dengan memanfaatkan celah SQL Injection.

1.  **Menemukan Titik Injeksi yang Rentan 🎯**
    Setelah mencoba berbagai titik seperti form login dan tidak menemukan hasil, perhatian dialihkan ke **fitur pencarian produk**. *Endpoint* API untuk pencarian ini terbukti menjadi titik lemah karena ia berinteraksi langsung dengan database untuk mengambil data produk.

2.  **Mencoba dan Gagal: Proses *Trial and Error* 💥**
    Langkah selanjutnya adalah mencoba mengekstrak data dari tabel `USERS` menggunakan teknik `UNION SELECT`.

    Percobaan pertama dengan *payload* seperti `test' UNION SELECT * FROM USERS /**/` mengalami kegagalan. Ini disebabkan karena **jumlah kolom** antara tabel `Products` (yang dipanggil oleh fitur pencarian) dan tabel `USERS` (yang ingin kita lihat) tidak cocok, sehingga menyebabkan *error* SQL.

3.  **Menyusun *Payload* Akhir dan Mengekstrak Data 🔑**
    Melalui proses coba-gagal, struktur kolom dari tabel `Products` berhasil diidentifikasi. Dengan informasi ini, *payload* injeksi disesuaikan agar memiliki jumlah dan urutan kolom yang sama, sehingga kueri `UNION` bisa berjalan dengan sukses.

    Untuk menyelesaikan tantangan, *payload* akhir yang digunakan adalah:

    ```sql
    test')) UNION SELECT username, password, role, deletedAt, isActive, createdAt, id, email, profileImage FROM USERS--
    ```

    Secara spesifik, kolom `email` harus disertakan dalam `SELECT` untuk memicu penyelesaian tantangan.

    Setelah *payload* ini dieksekusi melalui URL pencarian, aplikasi tidak lagi menampilkan produk, melainkan menampilkan seluruh data dari tabel `USERS`, termasuk *username*, *password*, dan *email*, langsung di halaman hasil pencarian.
