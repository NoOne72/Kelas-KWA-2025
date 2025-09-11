# Juice-Shop Write-up: Manipulate Basket

## Ringkasan Tantangan

**Judul:** Manipulate Basket  
**Kategori:** Broken Access Control
**Tingkat Kesulitan:** ⭐⭐⭐ (3/6)

Tantangan "Manipulate Basket" ini adalah tentang menambahkan sebuah item ke dalam keranjang belanja pengguna lain dengan mengeksploitasi kerentanan *Insecure Direct Object References* (IDOR).

## Alat yang Digunakan

-   **Peramban Web (Web Browser)**: Digunakan untuk berinteraksi dengan aplikasi web dan memeriksa permintaan jaringan.
-   **Burp Suite**: Digunakan untuk mencegat (*intercept*), memodifikasi, dan mengirim ulang permintaan HTTP.

## Metodologi dan Solusi

### 1. Menjelajahi Fungsionalitas Keranjang

Langkah pertama adalah memahami bagaimana fungsi "tambah ke keranjang" bekerja.

-   **Pengujian Awal**: Tambahkan sebuah produk ke keranjang belanja Anda sendiri sambil mengamati permintaan HTTP yang dikirim menggunakan Burp Suite.
-   **Identifikasi Parameter**: Dari permintaan tersebut, kita dapat melihat bahwa setiap operasi keranjang (seperti menambahkan item) dikirim bersama dengan sebuah `BasketId` yang terikat pada sesi pengguna.

### 2. Mengidentifikasi Kerentanan

-   **Manipulasi Permintaan**: Cegat permintaan `POST` saat menambahkan item ke keranjang menggunakan Burp Suite. Perhatikan bahwa `BasketId` milik kita disertakan dalam *body* permintaan. Ini menunjukkan potensi vektor serangan IDOR, di mana kita bisa mencoba mengganti ID tersebut dengan ID milik pengguna lain.

### 3. Upaya Eksploitasi IDOR (Gagal)

-   **Mengubah Basket ID**: Percobaan pertama adalah mengubah nilai `BasketId` dalam permintaan yang dicegat ke ID keranjang lain (misalnya, `1` untuk keranjang milik admin atau pengguna pertama).
-   Namun, upaya ini gagal. Aplikasi memiliki pemeriksaan kontrol akses yang mencegah kita menambahkan item ke keranjang yang bukan milik kita. Server mengembalikan pesan kesalahan.


*Gambar 1: Pesan error saat mencoba mengubah `BasketId` secara langsung.*

### 4. Melewati Pemeriksaan Keamanan (HTTP Parameter Pollution)

Karena cara langsung gagal, kita perlu mencoba teknik yang lebih canggih.

-   **Injeksi Parameter Ganda**: Teknik ini, juga dikenal sebagai *HTTP Parameter Pollution*, melibatkan pengiriman parameter yang sama lebih dari satu kali. Kita mencoba mengirimkan **dua** parameter `BasketId` dalam satu permintaan.
-   Percobaan pertama dengan parameter ganda mungkin menyebabkan error *foreign key*, yang menandakan bahwa server mencoba memprosesnya tetapi bingung. Ini adalah pertanda baik!


*Gambar 2: Error yang muncul setelah mencoba injeksi parameter ganda.*

-   Setelah beberapa kali mencoba, ditemukan celahnya: server ternyata hanya menggunakan `BasketId` **pertama** untuk pemeriksaan keamanan (memvalidasi apakah ID itu milik kita), tetapi menggunakan `BasketId` **kedua** untuk menjalankan operasi penambahan item ke database.
-   Jadi, *payload* yang berhasil adalah dengan mengirimkan `BasketId` milik kita di urutan pertama, dan `BasketId` target di urutan kedua.


*Gambar 3: Permintaan yang berhasil dengan `BasketId` milik kita dan `BasketId` target.*

### 5. Mengonfirmasi Eksploitasi

-   **Verifikasi**: Kirim permintaan yang telah dimanipulasi tersebut. Jika server merespons dengan status sukses, berarti serangan kemungkinan berhasil. Untuk memastikannya, kita perlu cara untuk memeriksa isi keranjang pengguna lain (jika memungkinkan) atau mengandalkan notifikasi penyelesaian tantangan dari Juice Shop.

## Penjelasan Solusi

Tantangan ini diselesaikan dengan mengidentifikasi dan mengeksploitasi kelemahan dalam cara aplikasi menangani parameter permintaan HTTP. Aplikasi gagal mengantisipasi adanya parameter duplikat. Dengan memasukkan `BasketId` ganda, kita berhasil **melewati pemeriksaan keamanan awal** yang hanya membaca parameter pertama, sementara **aksi sebenarnya dilakukan menggunakan parameter kedua**, yang memungkinkan kita memanipulasi keranjang pengguna lain.

## Rekomendasi Perbaikan (Remediasi)

Untuk mencegah kerentanan semacam ini di aplikasi nyata:

-   **Pastikan Penanganan Parameter yang Benar**: Server harus dirancang untuk menangani parameter yang tidak terduga, duplikat, atau tidak berurutan secara aman. Idealnya, server harus menolak permintaan dengan parameter duplikat atau secara konsisten hanya menggunakan parameter yang pertama.
-   **Terapkan Kontrol Akses yang Kuat**: Pastikan semua operasi sensitif (seperti mengubah isi keranjang) selalu memverifikasi di sisi server bahwa pengguna yang diautentikasi memiliki izin untuk melakukan tindakan pada sumber daya yang diminta (keranjang target). Jangan pernah memercayai ID yang dikirim dari sisi klien tanpa validasi kepemilikan.
