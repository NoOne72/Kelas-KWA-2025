1. Soal = Database Schema
2. Link =
3. Jawaban =

### **Langkah-langkah Penyelesaian**

Tujuan dari tantangan ini adalah untuk mengekstrak dan menampilkan struktur atau "skema" dari database aplikasi dengan memanfaatkan celah keamanan SQL Injection.

1.  **Menemukan Titik Injeksi 🎯**
    Langkah pertama adalah mengidentifikasi bagian mana dari aplikasi yang rentan. Untuk tantangan ini, celah keamanan berada pada **fitur pencarian produk**. Secara spesifik, *parameter* `q` pada URL yang digunakan untuk pencarian sangat rentan terhadap injeksi.

2.  **Membuat *Payload* `UNION` Injection ✍️**
    Kita akan menggunakan teknik injeksi `UNION SELECT` untuk menggabungkan hasil kueri kita dengan kueri pencarian produk yang asli. Kunci dari teknik ini adalah kita harus menebak jumlah kolom yang benar yang diambil oleh kueri asli.

    Untuk tantangan ini, *payload* yang digunakan adalah:

    ```sql
    test')) UNION SELECT 1, 2, 3, 4, 5, 6, 7, 8, sql FROM sqlite_schema--
    ```

    **Penjelasan *Payload*:**

      * `test'))`: Digunakan untuk menutup kueri asli dengan benar.
      * `UNION SELECT`: Menggabungkan hasil kueri kita.
      * `1, 2, ... 8, sql`: Kita memilih 8 nilai kosong dan mengambil data skema dari kolom `sql`.
      * `FROM sqlite_schema`: Ini adalah tabel khusus di database SQLite yang menyimpan semua informasi tentang struktur database.
      * `--`: Mengomentari sisa kueri asli agar tidak terjadi *error*.

3.  **Mengeksekusi Injeksi dan Mengekstrak Skema 🚀**
    *Payload* tersebut dimasukkan ke dalam URL melalui parameter `q`. Ketika dieksekusi, aplikasi akan menampilkan hasil dari kueri injeksi kita alih-alih menampilkan hasil pencarian produk.

    Hasil yang ditampilkan adalah seluruh **skema database**, yang berisi informasi krusial seperti nama-nama tabel, kolom, dan tipe datanya. Informasi ini sangat berharga untuk memahami cara kerja aplikasi dan merencanakan eksploitasi lebih lanjut.
