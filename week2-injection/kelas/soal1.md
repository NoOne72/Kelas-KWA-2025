Soal - Login Admin
Link Soal - https://juice-shop.herokuapp.com/#/login
Jawaban : 

### **Langkah-langkah Penyelesaian**

Inti dari tantangan ini adalah memanipulasi permintaan login untuk mengakali sistem agar memberikan akses sebagai admin tanpa mengetahui kata sandinya.

Berikut adalah langkah-langkah yang dilakukan:

1.  **Mencegat Permintaan Login**
    Langkah pertama adalah mencegat (*intercept*) permintaan HTTP POST yang dikirimkan ke server saat mencoba login. Dengan menggunakan *tool* seperti Burp Suite, kita bisa melihat bahwa data email dan kata sandi dikirim dalam format JSON.

2.  **Memodifikasi Data Permintaan**
    Setelah permintaan dicegat, bagian *payload* JSON dimodifikasi. Pada kolom email, nilai yang seharusnya alamat email diubah menjadi sebuah kode injeksi SQL, yaitu:

    ```sql
    admin@juice-sh.op' OR '1'='1' --
    ```

    Tujuan dari kode ini adalah untuk "merusak" perintah SQL di *backend*. Bagian `OR '1'='1'` akan membuat kondisi pengecekan selalu bernilai benar (*true*), dan `--` akan membuat sisa kueri (termasuk pengecekan kata sandi) diabaikan.

3.  **Mengirim Permintaan yang Telah Diinjeksi**
    Permintaan yang sudah dimodifikasi tersebut kemudian diteruskan ke server. Karena kueri SQL di *backend* berhasil dimanipulasi dan kondisinya selalu dianggap benar, sistem memberikan akses login sebagai pengguna `admin`, meskipun kata sandi yang dimasukkan salah atau bahkan tidak ada.
