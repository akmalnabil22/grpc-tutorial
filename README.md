1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?  
    - Unary: Client mengirim sebuah request, Server mengirimkan sebuah response. Contoh skenario: CRUD yang tidak membutuhkan komunikasi berkelanjutan antara client dan server(autentikasi, mengambil user berdasarkan id)  
    - Server Streaming: Client mengirim sebuah request, Server mengirim aliran response. Contoh skenario: Server mengirim data dalam jumlah besar, seperti log dan hasil pencarian.
    - Bi-directional Streaming: Client dan Server mengirim aliran pesan secara bersamaan. Contoh skenario: Client dan Server sama-sama mengirim dan menerima pesan, seperti aplikasi chat real-time


2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?  
    - kita bisa menggunakan TLS(Transport layer security) untuk mengautentikasi pengguna di client dan server untuk menjaga keamanan data yang dikirim
    - Kita juga bisa menambahkan middleware untuk mengecek otorisasi pengguna  

3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?  
    - Bi-directional streaming perlu mengirim dan menerima pesan secara bersamaan. Cukup sulit untuk diimplementasikan dengan model ownership rust dan keamanan async yang ketat  
    - Jika koneksi tiba-tiba terputus, task async bisa saja terus berjalan
    - Jika pesan yang dikirim terlalu banyak, buffer bisa penuh yang akan menyebabkan lag  

4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?  
    - Keuntungan:  
        1. Mudah dihubungkan dengan channel async  
        2. Safe ownership
        3. Secara otomatis mengatur aliran data
        4. Bekerja dengan library tokio  
    - Kekurangan:  
        1. Ukuran buffer tetap  
        2. Kontrol terbatas  

5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?  
    Untuk membangun layanan gRPC di Rust yang modular, reusable, dan maintainable, struktur kode sebaiknya mengikuti pendekatan arsitektur berlapis. Kode dapat dipisahkan menjadi beberapa modul seperti handler untuk logika terkait gRPC, `service` untuk logika bisnis inti, `model` untuk representasi data, dan `repo` untuk akses data seperti database. Penggunaan `trait` untuk mendefinisikan interface disarankan agar logika bisnis dapat diuji secara terpisah dan fleksibel. Modul proto sebaiknya dipisahkan dan hanya digunakan untuk menyimpan kode hasil kompilasi `.proto`, sedangkan konversi data antar model dan tipe protobuf ditangani melalui implementasi `From.` Dengan memisahkan logika transport (gRPC) dari logika aplikasi inti, serta memanfaatkan channel komunikasi dan utilitas async seperti tokio, proyek menjadi lebih mudah untuk dikembangkan, diuji, dan diperluas seiring waktu.  

6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?  
    Kita bisa menambahkan multi-threading agar pemrosesan request bisa berjalan lebih cepat saat terdapat banyak request yang masuk secara bersamaan.  

7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?  
    file `.proto` memberikan definisi service dan message yang tidak terikat dengan suatu bahasa pemrograman. Artinya, gRPC bisa dijalankan pada bahasa pemrograman apa saja sehingga bisa diimplementasikan di platform mana saja.  

8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?  
    - Keuntungan:  
        1. Bisa mengirim banyak request dan response sekaligus
        2. Mengurangi headers yang tidak penting dan meningkatkan bandwidth
    - Kekurangan: 
        1. Browser yang support HTTP/2 masih terbatas
        2. Proses debugging lebih kompleks

9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?  
    - REST APIs memberikan sebuah respon hanya ketika ada sebuah request. Setiap request stateless dan tidak memiliki memory dengan interaksi sebelumnya.  
    - Bidirectional streaming memungkinkan Clien dan Server mengirimkan message secara independen. Bisa mempertahankan state di beberapa pesan.

10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?  
    Dengan protocol buffer, ada kepastian terkait bentuk data yang dikirimkan antara client dan server, berbeda dengan JSON yang harus menyamakan bentuk data di client dan server. Protobuf juga memiliki bentuk yang lebih kecil sehingga dapat diolah lebih cepat dibanding data JSON.