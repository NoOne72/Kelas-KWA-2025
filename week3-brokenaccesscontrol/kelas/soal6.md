## Write-up Juice-Shop: CSRF

### Ringkasan Tantangan

**Judul:** CSRF (*Cross-Site Request Forgery*)  
**Kategori:** Broken Access Control
**Tingkat Kesulitan:** ⭐⭐⭐ (3/6)

Tantangan "CSRF" ini melibatkan pelaksanaan serangan ***Cross-Site Request Forgery*** **(CSRF)** untuk mengubah nama pengguna di platform OWASP Juice Shop tanpa persetujuan dari pengguna tersebut. 😱

-----

### Alat yang Digunakan

  - **Peramban Web (Web Browser)**: Khususnya versi lama yang tidak memiliki perlindungan CSRF modern (contohnya, Firefox versi 81.0).
  - **Burp Suite**: Untuk mencegat (*intercept*) dan menganalisis permintaan HTTP.
  - **Editor HTML**: Untuk membuat dan menjalankan halaman eksploitasi CSRF (seperti [http://htmledit.squarefree.com/](http://htmledit.squarefree.com/)).

-----

### Metodologi dan Solusi

#### Mengidentifikasi Endpoint yang Rentan

1.  **Penemuan Endpoint**: Gunakan **Burp Suite** untuk mencegat dan memeriksa permintaan HTTP yang dibuat saat mengubah nama pengguna melalui halaman profil aplikasi. Identifikasi permintaan `POST` yang berfungsi untuk mengubah nama pengguna, lalu perhatikan URL dan parameter form-nya.

    *Gambar 1: Permintaan HTTP yang dicegat Burp Suite, menunjukkan parameter untuk mengubah nama pengguna.*

-----

#### Membangun Halaman Serangan CSRF

2.  **Membuat Payload CSRF**: Buat sebuah halaman HTML yang berisi formulir yang otomatis terkirim (*auto-submitting*) ke *endpoint* yang rentan. Gunakan *input field* tersembunyi (*hidden*) untuk mengatur nilai nama pengguna baru yang diinginkan. Tambahkan JavaScript untuk mengirimkan formulir secara otomatis saat halaman dimuat.

    ```html
    <html>
      <body>
        <form action="http://127.0.0.1:3000/profile" method="POST">
          <input type="hidden" name="username" value="HelloFromCSRF" />
        </form>
        <script>document.forms[0].submit();</script>
      </body>
    </html>
    ```

-----

#### Menjalankan Serangan CSRF

3.  **Eksekusi Serangan**: Unggah file HTML serangan CSRF ke sebuah *host* (untuk tantangan ini, bisa menggunakan editor HTML online seperti [http://htmledit.squarefree.com/](http://htmledit.squarefree.com/)). Ketika korban (yang sedang login di Juice Shop) mengunjungi halaman berbahaya tersebut, skrip akan memicu pengiriman formulir menggunakan sesi terautentikasi korban, sehingga nama pengguna berubah tanpa persetujuan eksplisit dari pengguna.

-----

#### Melewati Perlindungan Peramban Modern

4.  **Mengatasi Perlindungan CSRF Peramban**: Karena peramban modern memiliki peningkatan keamanan untuk memblokir serangan CSRF (seperti kebijakan *SameSite cookie*), tantangan ini mungkin memerlukan penggunaan **versi peramban yang lebih lama** atau konfigurasi keamanan yang lebih longgar untuk berhasil.

-----

### Rekomendasi Perbaikan (Remediasi)

Untuk melindungi dari kerentanan CSRF:

  - **Gunakan Token Anti-CSRF**: Pastikan setiap pengiriman formulir yang sensitif menyertakan token unik dan rahasia yang divalidasi di sisi server. Token ini memastikan bahwa permintaan berasal dari aplikasi itu sendiri, bukan dari situs lain.
  - **Terapkan Atribut Same-Site pada Cookie**: Konfigurasikan *cookie* agar hanya dikirim dalam permintaan yang berasal dari situs yang sama (*Same-Site*). Ini adalah pertahanan yang sangat efektif yang sudah banyak diterapkan di peramban modern.
  - **Implementasikan Kebijakan CORS**: Konfigurasikan kebijakan *Cross-Origin Resource Sharing* (CORS) dengan benar untuk membatasi permintaan hanya ke domain yang tepercaya.
<img width="2508" height="1521" alt="image" src="https://github.com/user-attachments/assets/ccfbc9f2-6004-41fe-8e9e-aab8985383b8" />
<img width="831" height="1377" alt="image" src="https://github.com/user-attachments/assets/090b84b7-0624-47d3-abc2-6a71d675e01f" />

