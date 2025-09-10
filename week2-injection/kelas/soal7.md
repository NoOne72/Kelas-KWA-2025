1. Soal = No SQL Manipulation
2. Link =
3. Jawaban =

### **Langkah-langkah Penyelesaian**

Tantangan ini bertujuan untuk mengeksploitasi celah keamanan **NoSQL Injection**. Tujuannya adalah untuk memanipulasi fitur pembaruan ulasan (*review*) agar kita bisa mengubah **semua** ulasan produk sekaligus dalam satu kali permintaan, bukan hanya satu per satu.

1.  **Menganalisis Permintaan Normal**
    Langkah pertama adalah memahami bagaimana cara kerja fitur pembaruan ulasan yang normal. Dengan menggunakan *tool* seperti Burp Suite, kita bisa melihat bahwa saat memperbarui sebuah ulasan, aplikasi mengirimkan permintaan `PATCH` dengan *body* JSON yang berisi `id` dari ulasan spesifik yang ingin diubah dan `message` baru.

2.  **Membuat *Payload* Injeksi NoSQL**
    Kunci dari tantangan ini adalah memanipulasi nilai dari parameter `id`. Alih-alih mengirimkan ID ulasan dalam bentuk teks (string), kita akan mengirimkan sebuah objek JSON yang berisi operator dari database NoSQL (MongoDB).

    *Payload* yang digunakan adalah:

    ```json
    {
      "$ne": null
    }
    ```

    Dalam MongoDB, `$ne` berarti *not equal* (tidak sama dengan). Jadi, payload ini membuat kondisi "pilih semua dokumen yang `id`-nya **tidak sama dengan** `null`". Karena semua ulasan pasti memiliki `id`, kondisi ini akan cocok dengan **semua ulasan** yang ada di database.

3.  **Mengirim Permintaan yang Telah Dimodifikasi**
    Permintaan `PATCH` asli diubah. Nilai `id` yang tadinya berupa string diganti dengan *payload* injeksi yang sudah kita buat.

    Permintaan akhir yang dikirim terlihat seperti ini:

    ```json
    {
      "id": {"$ne": null},
      "message": "Pwned by NoSQL Injection"
    }
    ```

    Ketika server menerima permintaan ini, ia tidak lagi mencari satu ulasan spesifik. Sebaliknya, ia menjalankan kondisi yang kita suntikkan dan akhirnya memperbarui teks **semua ulasan** yang ada dengan pesan baru yang kita kirim. Dengan begitu, tantangan berhasil diselesaikan.
