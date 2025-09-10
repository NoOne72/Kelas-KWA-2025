1. Soal = Jim login
2. Link =
3. Jawab:

### **Langkah-langkah Penyelesaian**

Tantangan ini mirip dengan "Login Admin", namun tujuannya adalah masuk sebagai pengguna bernama Jim dengan memanfaatkan celah SQL Injection yang sama.

1.  **Identifikasi Target Pengguna**
    Langkah pertama adalah mengetahui email yang digunakan oleh Jim. Untuk tantangan ini, email yang digunakan adalah `jim@juice-sh.op`.

2.  **Membuat dan Mengirim *Payload* Injeksi**
    Pada halaman login, masukkan detail berikut:

      * **Kolom Email:** Masukkan email Jim, yaitu `jim@juice-sh.op`.
      * **Kolom Kata Sandi (Password):** Alih-alih memasukkan kata sandi asli, masukkan *payload* injeksi SQL berikut:
        ```sql
        ' or 1=1--
        ```

    *Payload* ini dirancang untuk mengubah logika kueri di database, membuatnya mengabaikan pemeriksaan kata sandi dan selalu mengembalikan hasil yang benar.

3.  **Mendapatkan Akses**
    Setelah menekan tombol login, sistem akan memproses kueri yang telah dimanipulasi. Karena kondisi kueri (`' or 1=1`) selalu benar, otentikasi berhasil dilewati, dan Anda akan langsung masuk ke dalam akun milik Jim tanpa perlu mengetahui kata sandi yang sebenarnya.
