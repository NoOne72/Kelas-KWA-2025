1. Soal = Login Bender
2. Link =
3. Jawaban =

### **Langkah-langkah Penyelesaian**

Tantangan ini berfokus pada eksploitasi celah SQL Injection untuk masuk sebagai pengguna bernama "Bender" tanpa perlu mengetahui kata sandinya.

1.  **Menemukan Email Bender 🕵️‍♂️**
    Langkah pertama adalah mencari tahu alamat email yang digunakan oleh Bender. Hal ini dapat dilakukan dengan cara masuk sebagai **admin**, lalu mengakses halaman administrasi pengguna untuk melihat daftar semua pengguna terdaftar. Dari sana, kita menemukan bahwa email Bender adalah `bender@juice-sh.op`.

2.  **Melakukan Injeksi di Halaman Login 💉**
    Setelah mengetahui emailnya, kembali ke halaman login. Alih-alih hanya memasukkan email, kita akan memasukkan *payload* SQL Injection secara langsung di **kolom email**.

    *Payload* yang digunakan adalah:

    ```sql
    bender@juice-sh.op' AND '1'='1' --
    ```

    **Penjelasan *Payload*:**

      * `bender@juice-sh.op'`: "Menutup" input string email yang valid.
      * `AND '1'='1'`: Menambahkan kondisi yang hasilnya **selalu benar**.
      * `--`: Mengubah sisa dari kueri SQL menjadi komentar, sehingga bagian pengecekan kata sandi akan diabaikan oleh database.

    Kolom kata sandi bisa dikosongkan atau diisi dengan teks acak, karena tidak akan diperiksa.

3.  **Mendapatkan Akses ✅**
    Setelah menekan tombol login, sistem akan memproses kueri yang telah dimanipulasi. Karena kondisi kueri selalu benar dan pengecekan kata sandi dilewati, otentikasi berhasil, dan Anda akan langsung masuk ke dalam akun milik Bender.
