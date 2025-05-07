# Tutorial Modul Pemrograman Lanjut 2024/2025

<details> <summary>1. Perbedaan Unary, Server Streaming, dan Bidirectional Streaming</summary>
Unary RPC mengirim satu permintaan dan menerima satu respons—cocok untuk operasi sederhana seperti login atau create record. Server streaming memungkinkan server mengirim banyak data untuk satu permintaan—ideal untuk pengambilan histori atau batch data. Sementara itu, bidirectional streaming memungkinkan klien dan server saling bertukar pesan secara asinkron dalam satu koneksi, menjadikannya pilihan terbaik untuk aplikasi real-time seperti live chat atau game multiplayer.

</details> <details> <summary>2. Pertimbangan Keamanan di Rust gRPC (Autentikasi, Otorisasi, Enkripsi)</summary>
Autentikasi dapat diimplementasikan dengan sertifikat TLS mutual atau token JWT; otorisasi bisa melalui role-based access control di middleware atau interceptor. Enkripsi data wajib dilakukan dengan TLS, karena gRPC secara default berjalan di atas HTTP/2, yang mendukung TLS. Kelemahannya, Rust saat ini belum punya ekosistem interceptor sekuat Go atau Java, sehingga sering perlu kode manual untuk validasi permintaan.

</details> <details> <summary>3. Tantangan Bidirectional Streaming di Rust gRPC (mis. Chat App)</summary>
Masalah yang sering muncul adalah race condition antara pengiriman dan penerimaan data secara paralel, serta manajemen stream yang harus tetap aktif selama komunikasi berlangsung. Selain itu, error handling dan backpressure (penundaan saat buffer penuh) juga menjadi tantangan karena setiap task async harus dipantau secara eksplisit agar tidak menggantung atau bocor memori.

</details> <details> <summary>4. Kelebihan dan Kekurangan ReceiverStream di Rust gRPC</summary>
Kelebihannya, ReceiverStream membuat integrasi dengan channel tokio menjadi sangat mudah dan idiomatik dalam ekosistem Rust async. Namun, kekurangannya adalah kontrol terhadap alur data terbatas—misalnya, sulit menangani backpressure kompleks atau melakukan buffering dan retry yang lebih pintar dibanding pendekatan manual dengan Stream trait.

</details> <details> <summary>5. Struktur Kode Modular & Reusable di Rust gRPC</summary>
Pisahkan setiap layanan (misalnya PaymentService, TransactionService) dalam modul sendiri, gunakan traits dan generic untuk logic yang bisa diabstraksi, dan gunakan builder pattern atau factory untuk inisialisasi service. Pastikan handler logic tidak langsung berada di dalam implementasi trait agar mudah dites dan digunakan ulang.

</details> <details> <summary>6. Pengembangan MyPaymentService untuk Kasus Nyata</summary>
Perlu menambahkan validasi input, integrasi ke sistem eksternal (gateway pembayaran), handling retry untuk transaksi gagal, audit log, dan penyimpanan status transaksi ke database. Selain itu, error harus dikategorikan dengan tepat agar klien bisa membedakan antara kesalahan pengguna dan kesalahan sistem.

</details> <details> <summary>7. Dampak gRPC terhadap Arsitektur Sistem Terdistribusi</summary>
gRPC memaksa sistem menjadi lebih strongly typed dan terstruktur, memudahkan integrasi antar layanan lintas bahasa karena protokol dan kontraknya jelas. Namun, interoperabilitas dengan sistem lawas berbasis REST atau JSON bisa jadi sulit tanpa gateway atau converter, dan debugging komunikasi binary membutuhkan tooling tambahan.

</details> <details> <summary>8. HTTP/2 vs HTTP/1.1 dan WebSocket dalam Konteks REST API</summary>
HTTP/2 mendukung multiplexing (beberapa permintaan dalam satu koneksi), lebih efisien daripada HTTP/1.1 yang berbasis pipelining. Dibanding WebSocket, HTTP/2 punya protokol yang lebih terstruktur dan aman secara default. Namun, adopsi server-side masih terbatas, dan debugging lebih rumit dibanding REST yang memakai teks biasa.

</details> <details> <summary>9. REST Request-Response vs gRPC Streaming dalam Komunikasi Real-Time</summary>
REST berbasis stateless request-response, membuatnya terbatas untuk aplikasi real-time. Sementara gRPC dengan bidirectional streaming memungkinkan pengiriman data dua arah secara langsung tanpa menunggu respons terlebih dulu, yang menjadikannya jauh lebih cocok untuk notifikasi instan, live feed, atau kolaborasi real-time.

</details> <details> <summary>10. Protobuf vs JSON: Konsekuensi Pendekatan Skema</summary>
gRPC menggunakan Protocol Buffers yang lebih cepat dan efisien dari sisi ukuran dan parsing, namun butuh kompilasi dan skema eksplisit. Sebaliknya, JSON bersifat fleksibel dan mudah dibaca manusia, tapi rentan kesalahan akibat ketidakkonsistenan struktur, serta lebih lambat karena parsing teks dan payload yang besar.

</details>