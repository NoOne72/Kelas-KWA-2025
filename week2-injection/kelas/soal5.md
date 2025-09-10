1. Soal = Christmas Offer
2. Link =
3. Jawaban =

### **Langkah-langkah Penyelesaian**

Tujuan dari tantangan ini adalah untuk menemukan dan membeli produk spesial Natal yang telah disembunyikan dari daftar produk normal. Produk ini tidak terlihat karena di dalam database, ia ditandai sebagai "telah dihapus" (*deleted*).

1.  **Menemukan Produk yang Tersembunyi**
    Celah keamanan yang sama pada **fitur pencarian produk** dimanfaatkan kembali. Kali ini, *payload* SQL Injection dirancang untuk mencari produk yang statusnya sudah "dihapus".

    *Payload* yang digunakan adalah:

    ```sql
    test')) UNION SELECT * FROM PRODUCTS WHERE deletedAt IS NOT NULL--
    ```

    **Penjelasan *Payload*:**

      * `UNION SELECT * FROM PRODUCTS`: Meminta database untuk menampilkan semua data dari tabel `PRODUCTS`.
      * `WHERE deletedAt IS NOT NULL`: Ini adalah bagian kuncinya. Kondisi ini secara spesifik mencari produk yang kolom `deletedAt`-nya **tidak kosong**, yang menandakan bahwa produk tersebut telah "dihapus" secara logis dari tampilan.

    Setelah dieksekusi, injeksi ini berhasil menampilkan produk-produk yang tersembunyi, termasuk penawaran Natal. Dari hasil tersebut, kita mengetahui bahwa **`ProductId`** untuk produk ini adalah **`10`**.

2.  **Menambahkan Produk ke Keranjang Secara Paksa**
    Karena produk ini tidak terlihat di antarmuka web, kita tidak bisa menambahkannya ke keranjang secara normal. Solusinya adalah dengan memanipulasi permintaan jaringan:

      * **Tambah Produk Lain:** Pertama, tambahkan produk apa saja ke keranjang belanja.

      * **Cegat Permintaan:** Gunakan *tool* seperti Burp Suite untuk mencegat (*intercept*) permintaan `POST` yang dikirim saat menambahkan produk tersebut.

      * **Ubah `ProductId`:** Di dalam permintaan yang dicegat, cari parameter `ProductId`. Ubah nilainya dari ID produk normal menjadi `10` (ID produk Natal yang sudah kita temukan).

      * **Kirim Permintaan:** Kirim permintaan yang sudah dimodifikasi tersebut.

    Setelah permintaan diproses, produk spesial Natal yang tersembunyi berhasil ditambahkan ke keranjang belanja Anda, dan tantangan pun selesai.

