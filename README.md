1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

- Perbedaan utamanya adalah pada unary streaming adalah ketika client hanya ingin mengirimkan data kecil dan satu kali ke server sehingga tidak diperlukannya pengiriman data banyak sekaligus.
Unary cocok digunakan misal pada sistem autentikasi/autorisasi, pengiriman form.
- Server streaming memiliki perbedaan yaitu saat server mengirimkan informasi banyak sekaligus melalui 1 grpc stream.
Hal ini berguna ketika server perlu mengirimkan data dalam jumlah yang sangat banyak berkali-kali sehingga tidak perlu dibuatnya koneksi baru karena overheadnya lebih mahal. Yang dilakukan adalah server akan mengirimkan data berulang kali melalui 1 koneksi yang sama sehingga data yang terkirimkan adalah potongan data-data yang membentuk data yang lebih besar. Misal pada  harga saham, berita atau dataset yang besar dikirimkan dalam potongan-potongan.
- Bidirectional mirip dengan server streaming tetapi sekarang client juga melakukan hal yang sama dengan server dalam proses pengiriman datanya. Ini cocok untuk ketika server dan client sering mengirimkan data berulang kali misal pada colaborative editing atau real time game.
2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

    Dalam autorisasi/autentikasi untuk tiap potongan data yang dikirimkan oleh grpc diperlukannya validasi autorisasi/autentikasi untuk menjamin keamanannya dimana ini tidak sama seperti REST karena pada REST hanya diperlukannya 1 kali validasi untuk tiap request. Untuk enkripsi data, tiap potongan data yang dikirimkan baik oleh server maupun client perlu di enkripsi secara terpisah untuk menjamin privasi dari data yang dikirim.

    Intinya, tidak seperti REST, GRPC memaksa autoriasi/autentikasi/enkripsi berkali-kali untuk 1 stream request sedangkan untuk REST, 1 request hanya perlu validasi 1 kali karena setelah response akan ditutup hubungannya.

3.  What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

    Ketika kita ingin membuat sistem dengan bidirectional streaming dan juga grpc akan sulit untuk memastikan tidak terjadinya race condition dan perlu dipastikan sinkronisasi data. Koneksi yang hidup lama juga akan mempersulit load balancing karena bisa saja terbentuk banyak koneksi yang hidup lama terhadap dimana tidak bisa diputuskan koneksi tersebut akibat grpc sehingga akan membebankan server tersebut.

4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?

    Keuntungan :
    - Asynchronous yang mempercepat pengolahan data
    - Integrasi yang mudah dengan modul-modul tokio lain
    Kerugian : 
    - Kompleks akibat pemrograman asinkronous
    - Untuk autentikasi dan autorisasi perlu dilakukan untuk tiap data

5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

    Rust gRPC memberikan cara untuk meningkatkan maintainability dan ekstensibility karena perubahan dan penambahan terhadap fitur dapat dilakukan dengan lebih mudah karena dengan proto akan dibuat sebuah interface, kurang lebih, yang dapat diimplementasikan oleh suatu class di Rust yang mempermudah ekstensibilitas kode. 

6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?
    Bisa dibuat menjadi server streaming dan tidak lagi unary agar pengiriman data-data yang kompleks dapat dilakukan dengan lebih cepat dan mengurangi overhead pembuatan koneksi antara client dan server.

7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

    Ketika kita menggunakan gRPC, kita jadi tidak perlu memikirkan bagaimana suatu modul dapat diakses dengan http method karena grpc akan mengkonek pemanggilan method yang kita inginkan secara otomatis karena sudah ada pemahaman yang sama melalui file proto dimana client jadi bisa seakan-akan memanggil function dari server sehingga mempermudah konektivitas dan operasi antar teknologi, platform dan sistem terdistribusinya. 

8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

    Keuntungan dari HTTP/2 adalah HTTP/2 memberikan opsi dimana banyak request dan response dapat dilakukan dalam sebuah koneksi tanpa koneksi perlu dimatikan.
    Kerugian dari HTTP/2 dibanding HTTP/1.1 adalah karena HTTP/2 bisa mempertahankan 1 koneksi untuk banyak request dan response, maka akan diperlukannya biaya overhead yang lebih tinggi baik untuk performance maupun memory, dimana bisa saja untuk pengiriman data yang kecil akan memakan biaya lebih besar dari HTTP/2 kebanding HTTP/1.1 walaupun untuk data yang banyak HTTP/2 lebih efisien.

9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

    Dari cara kerja REST APIs dan juga gRPC sudah terlihat sebuah perbedaan dimana REST APIs akan memerlukan waktu lebih lama kebanding gRPC untuk real-time communication karena untuk REST APIs, tiap pengiriman request diperlukan pembuatan koneksi baru dari client ke server dan untuk gRPC hanya memerlukan 1 koneksi sampai semua request selesai. Hal ini membuat gRPC lebih optimal untuk real-time communication and responsiveness kebanding REST APIs karena kecepatannya yang lebih baik sehinga lebih "real time".

10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

    Dengan pendekatan protocol buffer, client menjadi tidak perlu memikirkan apakah suatu JSON memang berisikan informasi yang sesuai karena dengan gRPC dan protocol buffernya akan dibuat suatu interface yang akan membuat client dan server code dan akan saat runtime men-serialize dan men-deserialize message yang masuk dan keluar secara otomatis.

    Dengan demikian tiap data yang masuk dan keluar bisa dibilang terjamin bisa digunakan dan tidak salah tipe, dimana jika dibandingkan dengan JSON di REST APIs, sangat mungkin untuk mengirimkan data yang berisikan informasi yang tidak bisa dipakai oleh salah satu pihak karena tidak ada contraint mengenai apa yang perlu dikirim.