1. Soal = Ephemeral Accountant
2. Link =
3. Jawaban =
4. 
### **Langkah-langkah Penyelesaian**

Tantangan ini sangat unik. Tujuannya bukan untuk membobol akun yang sudah ada, melainkan untuk "menciptakan" pengguna sementara (*ephemeral*) di dalam kueri SQL saat proses login. Kita akan membuat seolah-olah pengguna `acc0unt4nt@juice-sh.op` ada di database, padahal sebenarnya tidak.

1.  **Menganalisis Permintaan Login**
    Proses login standar menggunakan permintaan `POST` yang mengirimkan data `email` dan `password` dalam format JSON. Titik inilah yang akan kita manfaatkan untuk injeksi.

2.  **Membuat *Payload* Injeksi Pengguna Sementara**
    Kita akan menggunakan teknik `UNION SELECT` untuk membuat satu baris data pengguna palsu yang strukturnya sama persis dengan tabel pengguna di database. *Payload* ini disuntikkan seluruhnya ke dalam kolom `email`.

    Permintaan login yang telah dimodifikasi akan terlihat seperti ini:

    ```json
    {
      "email": "' UNION SELECT * FROM (SELECT 20 AS `id`, 'acc0unt4nt@juice-sh.op' AS `username`, 'acc0unt4nt@juice-sh.op' AS `email`, 'test1234' AS `password`, 'accounting' AS `role`, '123' AS `deluxeToken`, '1.2.3.4' AS `lastLoginIp`, '/assets/public/images/uploads/default.svg' AS `profileImage`, '' AS `totpSecret`, 1 AS `isActive`, 12983283 AS `createdAt`, 133424 AS `updatedAt`, NULL AS `deletedAt`) AS tmp WHERE '1'='1';--",
      "password": "test1234"
    }
    ```

    **Poin Kunci dari *Payload*:**

      * Seluruh perintah `UNION SELECT` disuntikkan ke dalam **kolom `email`**.
      * Perintah ini secara manual mendefinisikan setiap kolom yang dibutuhkan untuk sebuah akun, seperti `id`, `username`, `email`, `password`, dan `role`.
      * Di dalam injeksi, kita menetapkan `email` menjadi `'acc0unt4nt@juice-sh.op'` dan `password` menjadi `'test1234'`.
      * Penting: **Kolom `password`** pada JSON harus diisi dengan kata sandi yang sama (`test1234`) agar proses otentikasi terhadap pengguna sementara ini berhasil.

3.  **Mengeksekusi Injeksi dan Login**
    Permintaan `POST` yang telah dimodifikasi ini dikirim ke server menggunakan *tool* seperti Burp Suite. Server akan menjalankan kueri login, menemukan baris pengguna sementara yang kita ciptakan melalui `UNION`, lalu memvalidasi kata sandinya.

    Karena kata sandi yang kita berikan di JSON cocok dengan kata sandi di baris sementara, server akan memberikan token sesi yang valid. Dengan ini, kita berhasil login sebagai pengguna `acc0unt4nt@juice-sh.op` yang sebenarnya tidak pernah ada secara permanen di database.
