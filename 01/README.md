# Ringkasan Materi Minggu 1 : Pengenalan Sistem Terdistribusi dan Terdesentralisasi

Mata Kuliah     : Sistem Terdistribusi dan Terdesntralisasi
Dosen Pengampu  : Dr. Bambang Purnomosidi D. P.

## Tujuan Pembelajaran
• Mampu menjelaskan konsep sistem terdistribusi<br>
• Mampu menjelaskan konsep sistem terdesentralisasi<br>
• Mampu menjelaskan perbedaan sistem terdistribusi dengan sistem terdesentralisasi<br>
• Mampu menyebutkan berbagai contoh penerapan nyata dari sistem terdistribusi dan terdesentralisasi<br>
• Mampu menyebutkan berbagai tantangan dan
tren terbaru di dunia sistem terdistribusi dan
terdesentralisasi

---

## 1. Arti Penting Sistem Terdistribusi dan Terdesentralisasi

<p align ="justify">Kenapa sistem terdistribusi dan terdesentralisasi dibutuhkan? Ada empat pendorongnya: data dan pengguna yang terus bertambah, layanan modern yang harus bisa diskalakan, sistem yang wajib tahan terhadap kegagalan, dan tuntutan agar layanan cepat serta menyala 24 jam sehari. Google, Netflix, dan Blockchain tidak mungkin berdiri tanpa semua itu.

<p align ="justify">Contoh paling gamblang datang dari klaster mesin pencari Inktomi, yang pernah menangani separuh trafik pencarian web. Disk di klaster itu rusak tiap minggu, basis datanya diperbarui tiap hari, dan sesekali listrik atau jaringan ikut bermasalah, tapi pengguna tetap mengharapkan hasil pencarian muncul. Google File System (GFS) lahir dari cara pandang yang sama: di klaster dengan ribuan perangkat keras komoditas, kerusakan komponen adalah kejadian rutin. Bayangkan semua data dan beban kerja ditaruh di satu mesin. Kapasitasnya cepat habis, dan begitu mesin itu mati, seluruh layanan ikut mati, itulah single point of failure.

<p align ="justify">Bitcoin (2008) punya persoalan lain lagi karena tidak ada otoritas pusat sama sekali. Para partisipan sendiri yang mencatat dan memvalidasi transaksi bersama-sama, tanpa bank atau lembaga perantara.

## 2. Catatan 

<p align ="justify">Skalabilitas adalah kemampuan sistem, jaringan, proses, atau organisasi untuk tumbuh dan tetap sanggup menangani beban kerja, pengguna, atau permintaan yang makin banyak, tanpa kehilangan kinerja maupun kualitasnya. Untuk sistem terdistribusi, artinya sistem harus tetap efektif walaupun jumlah sumber daya dan penggunanya melonjak tajam.

<p align ="justify">Sistem fault-tolerant punya sifat berbeda: ia terus bekerja normal meskipun sebagian komponen perangkat keras atau perangkat lunaknya gagal. Pada penyimpanan skala besar seperti GFS dan HDFS, hal ini dicapai lewat deteksi kegagalan otomatis, replikasi data lintas rak (cross-rack replication), pencatatan operasi dengan write-ahead log, dan pengalihan beban ke komponen yang masih sehat.


## 3. Pengertian Sistem Terdistribusi

<p align ="justify">Sistem terdistribusi adalah kumpulan komputer independen yang bagi penggunanya tampak seperti satu sistem tunggal yang koheren (a collection of independent computers that appears to its users as a single coherent system). Komponennya berdiri sendiri-sendiri dan berkomunikasi lewat jaringan, lalu ada empat jenis transparansi yang membuat semuanya terasa menyatu: transparansi lokasi, akses, replikasi, dan kegagalan. Masing-masing menyembunyikan satu hal. Lokasi menutupi di mana sumber daya berada, akses menutupi perbedaan protokol atau bentuk data, replikasi menutupi keberadaan banyak salinan, dan kegagalan menutupi proses pemulihan di belakang layar. Pengguna jadinya merasa hanya berurusan dengan satu entitas.

<p align ="justify">Materi memakai diagram arsitektur microservices dari blog teknis SolarWinds untuk menggambarkannya. Di sisi klien ada Mobile app, Browser, dan Web Storefront WebApp, yang semuanya mengirim permintaan lewat REST API ke API GATEWAY. Dari situ gateway membagi permintaan, lagi-lagi lewat REST API, ke tiga layanan yang terpisah: Account Service (dengan Account DB), Inventory Service (dengan Inventory DB), dan Shipping Service (dengan Shipping DB). Tiap layanan memegang basis datanya sendiri. Begitulah pola microservices: aplikasi dipecah jadi layanan-layanan kecil yang bisa dikembangkan dan diskalakan masing-masing, dan mereka berbicara satu sama lain lewat antarmuka ringan seperti REST API.

## 4. Arsitektur Sistem Dasar

<p align ="justify">Materi memetakan tiga arsitektur dasar, masing-masing dengan diagram: Client-Server, Peer-to-Peer (P2P), dan Hybrid.

<p align ="justify">Pada Client-Server, banyak Clients terhubung lewat Internet ke satu Server pusat. Perannya tidak setara. Klien meminta, server memproses dan menjawab, sesuai pembagian klasik antara peminta dan penyedia layanan.

<p align ="justify">P2P kebalikannya. Jaringan terdiri dari node yang setara tanpa server pusat, dan diagramnya mencantumkan BitTorrent, Bitcoin, Ethereum, Ripple, Doge, Uniswap, Cardano, Chainlink, Litecoin, dan Theta sebagai contoh ekosistem. Satu node bisa sekaligus memakai layanan dan menyediakannya untuk node lain.

<p align ="justify">Arsitektur Hybrid mencampur keduanya, dan legenda diagramnya punya enam komponen: Client/Server Connection, Client Request, Server Response, Peer-to-Peer File-sharing, Supernode Connection, dan Supernodes. Alurnya begini. Klien mengirim Client Request ke koordinator yang berkapasitas lebih besar (Supernodes atau server) untuk mencari sumber daya atau berkoordinasi, lalu menerima Server Response. Setelah ketemu, berkas dipertukarkan langsung antarpengguna lewat jalur P2P, sementara antar-koordinator saling menyelaraskan data lewat Supernode Connection. Keuntungannya ganda: pencarian cepat karena koordinator memegang indeks, sedangkan transfer data yang berat dibagi rata ke seluruh peer.

## 5. Karakteristik Sistem Terdistribusi

<p align ="justify">Karakteristiknya ada lima: transparansi lokasi, skalabilitas, fault tolerance, kinerja tinggi, dan heterogenitas.

<p align ="justify">Transparansi lokasi memisahkan nama logis sumber daya dari alamat fisik mesinnya. Server bisa dipindah tanpa aplikasi klien menyadarinya. Skalabilitas dan fault tolerance berasal dari akar yang sama, yaitu membagi beban kerja dan mereplikasi data ke berbagai mesin dan rak. Kalau kapasitas kurang, tinggal tambah peladen komoditas; kalau ada komponen yang rusak, salinannya mengambil alih.

<p align ="justify">Kinerja tinggi bergantung pada pemrosesan paralel dan data locality. Contohnya HDFS: kerangka seperti MapReduce menjadwalkan tugas di simpul yang sudah menyimpan blok datanya, jadi data tidak perlu bolak-balik lewat jaringan. Heterogenitas adalah soal keberagaman. Dalam satu infrastruktur bisa ada perangkat keras, sistem operasi, jaringan kabel dan nirkabel, serta bahasa pemrograman yang berlainan. Supaya semuanya tetap bisa saling bekerja, dipakailah lapisan middleware, protokol komunikasi standar yang terbuka, dan format pertukaran data yang tidak terikat pada satu bahasa.

## 6. Pengertian Sistem Terdesentralisasi

<p align ="justify">Dalam sistem tersentralisasi, kendali ada di satu titik. Sistem terdesentralisasi membagi kendali itu sehingga tidak ada single point of failure, dan keputusan diambil bersama lewat konsensus. Blockchain, DNS, dan BitTorrent adalah contohnya.

<p align ="justify">Konsepnya bisa dilihat pada diagram klasik dari memorandum RAND Corporation tahun 1964, On Distributed Communications (RM-3420-PR), yang membandingkan tiga topologi jaringan yang tersusun dari Station dan Link. Di CENTRALIZED (A), semua Station menggantung pada satu pusat kendali, jadi bila pusat itu padam, jaringan putus total. Di DECENTRALIZED (B), kendali tersebar ke beberapa simpul penghubung, sehingga rusaknya satu simpul tidak langsung melumpuhkan seluruh sistem. Di DISTRIBUTED (C), tautan menyebar merata antarsimpul, tanpa pusat dan tanpa hierarki; topologi ini dirancang sebagai arsitektur jaringan packet-switched yang tahan terhadap kerusakan jalur.

<p align ="justify">Satu konsekuensi penting: karena tak ada pengendali tunggal, sistem terdesentralisasi modern butuh protokol konsensus agar node-node yang independen bisa menyepakati setiap perubahan keadaan.

## 7. Sistem Terdistribusi vs Sistem Terdesentralisasi

<p align ="justify">Keduanya berbeda di hal yang dibagi. Sistem terdistribusi membagi beban kerja; sistem terdesentralisasi membagi kontrol. Materi mencontohkan GFS dan MinIO Object Storage (dicatat sudah di-fork ke Silo) untuk yang pertama, dan Blockchain untuk yang kedua.

<p align ="justify">GFS memotong berkas jadi chunk 64 MB dan menyalinnya ke banyak chunkserver. Tapi metadatanya tetap dipegang satu GFS master, keputusan yang membuat koordinasi global lebih sederhana, dan master tidak ikut campur di jalur transfer data. Penyimpanan objek yang kompatibel dengan S3 seperti MinIO juga membagi beban dengan pola mirip, begitu pula penerus komunitasnya, PGSTY Silo. Menurut dokumentasi repositori komunitas PGSTY tahun 2026, Silo adalah fork independen dari peladen MinIO sumber terbuka. Tujuannya menjaga kompatibilitas API S3 dan format penyimpanan setelah distribusi komunitas hulunya berhenti.

<p align ="justify">Bitcoin (2008) berjalan di arah lain. Yang dibagi di sana adalah wewenang validasi, ke banyak partisipan independen, supaya tidak ada satu pihak pun yang bisa menguasai buku besar transaksi. Perbandingan ini menunjukkan bahwa sistem bisa tersebar secara fisik tetapi kendalinya tetap di satu tangan. Sistem terdesentralisasi justru memakai jaringan terdistribusi sebagai alat untuk membagi kontrol.

## 8. Studi Kasus Sistem Terdistribusi

<p align ="justify">Di industri, contohnya GFS/HDFS untuk data besar, Apache Kafka dan Apache Pulsar untuk messaging dan streaming, serta platform hiburan dan media sosial seperti Netflix, Twitch, YouTube, Instagram, Threads, Facebook, dan TikTok.

<p align ="justify">HDFS memisahkan NameNode, pengelola namespace metadata, dari ribuan DataNodes penyimpan blok data. Di Yahoo!, susunan ini tercatat mengelola 25 petabita data di 25.000 peladen dan memungkinkan akses paralel dengan throughput tinggi. Kafka dirancang sebagai sistem perpesanan terdistribusi untuk pemrosesan log. Apache Pulsar, menurut dokumentasi resminya, memakai dua lapis: broker sebagai serving layer yang tidak menyimpan state, dan penyimpanan persisten di Apache BookKeeper tempat bookies menyimpan ledgers, ditambah metadata store seperti Oxia atau Apache ZooKeeper. Pemisahan ini berguna karena kapasitas komputasi broker dan kapasitas penyimpanan bookie bisa ditambah sendiri-sendiri, dan penulisan jurnal tidak terganggu latensi pembacaan.

<p align ="justify">Netflix, Twitch, dan platform serupa mengandalkan arsitektur terdistribusi agar trafik konten dan interaksi jutaan pengguna dari seluruh dunia bisa dilayani bersamaan.

## 9. Studi Kasus Sistem Terdesentralisasi

<p align ="justify">Ada tiga studi kasus: Blockchain (Bitcoin, Ethereum, Algorand, Sui, Near, Aptos, dan lainnya), Domain Name System (DNS), dan BitTorrent.

<p align ="justify">Bitcoin (2008) memperkenalkan transaksi elektronik peer-to-peer tanpa perantara pusat. Algorand tercatat dalam literatur sebagai salah satu varian protokol konsensus terdesentralisasi, berdampingan dengan Ethereum, Sui, Near, dan Aptos dari materi. DNS menyimpan pemetaan nama domain ke alamat IP di banyak root server dan pengelola zona yang tersusun berjenjang di berbagai wilayah, tanpa satu basis data monolitik sebagai tumpuan. BitTorrent menjalankan berbagi berkas P2P di mana tiap simpul pengguna saling bertukar potongan berkas langsung, tanpa server distribusi pusat.

<p align ="justify">Ketiganya menunjukkan bahwa desentralisasi kontrol bisa dipakai di bidang yang sangat berbeda: buku besar keuangan, sistem penamaan global, sampai distribusi konten berukuran besar.

## 10. CAP Theorem

<p align ="justify">Teorema CAP dikemukakan pada tahun 2000 dan menyebut tiga jaminan pada sistem data terdistribusi. Consistency berarti setiap pembacaan mengembalikan penulisan terbaru, atau galat. Availability berarti setiap permintaan ke node yang tidak gagal mendapat respons non-galat, meskipun data yang dikembalikan belum tentu yang terbaru. Partition Tolerance berarti sistem tetap beroperasi walaupun komunikasi antar-node terputus.

<p align ="justify">Begitu jaringan terpartisi, ketiganya tidak bisa dipenuhi bersamaan, jadi perancang harus memilih AP atau CP, meski ada auto-healing yang bekerja setelah partisi pulih. Konjektur ini dibuktikan secara formal pada 2002 di model jaringan asinkron, lewat argumen ketakterbedaan. Intinya begini: kalau pesan antar-komponen partisi hilang, pembacaan di komponen kedua tetap harus selesai demi availability, sehingga mustahil menjamin nilai yang dikembalikan adalah penulisan terakhir di komponen pertama. Pada 2012 muncul penjelasan lanjutan bahwa dalam jaringan normal tanpa partisi, konsistensi dan ketersediaan bisa berjalan beriringan. Jadi memilih AP atau CP hanyalah strategi untuk masa partisi berlangsung.

<p align ="justify">Di materi kuliah, MongoDB dicontohkan sebagai AP dan Apache Cassandra (tertulis “Casandra”) sebagai CP. Dokumentasi resmi kedua sistem mengatakan sebaliknya, dan itu perlu dicatat. MongoDB, menurut dokumentasinya, pada sebagian besar konfigurasi adalah sistem CP. Replikasinya berbasis turunan Raft, menuntut mayoritas replika lewat writeConcern: “majority”, dan menolak penulisan di sisi minoritas ketika partisi terjadi, meski konsistensinya bisa dilonggarkan ke arah AP per operasi. Cassandra, menurut dokumentasi DataStax/Apache Cassandra, berarsitektur eventual consistency (AP) dengan tunable consistency. Misalkan R adalah jumlah replika baca, W jumlah replika tulis, dan N total replika. Bila R + W ≤ N, Cassandra bekerja dalam eventual consistency; bila R + W > N (misalnya dengan level konsistensi QUORUM), atau memakai lightweight transactions, ia bisa dikonfigurasi menyerupai CP, yaitu strong/linearizable consistency.

## 11. Konsistensi Data

<p align ="justify">Materi membandingkan dua model. Strong Consistency membuat hasil pembacaan selalu sama dan dibutuhkan di perbankan, sedangkan Eventual Consistency, yang menjamin data pada akhirnya konsisten, sudah cukup untuk layanan media sosial.

<p align ="justify">Secara formal, strong consistency berkaitan dengan linearizability: riwayat eksekusi yang berjalan konkuren dianggap setara dengan satu urutan sekuensial tunggal, sehingga semua klien selalu melihat data terbaru. Perbankan memerlukan jaminan seketat itu karena saldo dan catatan transaksi harus akurat pada setiap pembacaan dan penulisan.

<p align ="justify">Eventual consistency dipelajari sebagai model konsistensi yang berpusat pada klien. Ia berpasangan dengan paradigma BASE (Basically Available, Soft-state, Eventual consistency) dan sudah dipakai di sistem produksi skala besar. Jaminannya: selama tidak ada pembaruan baru, semua replika akhirnya konvergen ke nilai yang sama. Di media sosial, replikasi yang telat sebentar antar-pusat data masih bisa ditoleransi, dan sebagai gantinya respons tetap cepat serta layanan selalu tersedia.

## Kesimpulan

<p align ="justify">Satu mesin atau satu pusat kendali pada akhirnya tidak sanggup menanggung data yang terus membengkak, pengguna yang berdatangan tanpa henti, dan layanan yang tak boleh mati sepanjang hari. Dari situlah sistem terdistribusi dan terdesentralisasi muncul. Orang sering mengira keduanya sama, padahal yang dibagi berbeda: sistem terdistribusi membagi beban kerja, sementara sistem terdesentralisasi membagi kontrol. GFS memperlihatkan perbedaannya dengan baik, karena datanya tersebar di banyak chunkserver tetapi metadatanya masih dipegang satu master. Bitcoin kebalikannya, sebab wewenang validasi dibagi ke banyak partisipan dan tak seorang pun bisa menguasai buku besar transaksi sendirian. Arsitekturnya pun bervariasi, yaitu Client-Server, Peer-to-Peer, dan Hybrid, dan kualitas rancangannya bisa dinilai dari transparansi lokasi, skalabilitas, fault tolerance, kinerja tinggi, serta kemampuan menampung perangkat yang beragam (heterogenitas). Contoh nyatanya mudah ditemukan: HDFS, Kafka, dan Pulsar mewakili sisi distribusi, sedangkan Blockchain, DNS, dan BitTorrent mewakili sisi desentralisasi.

<p align ="justify">Menyalin data ke banyak node ada ongkosnya, dan Teorema CAP menjelaskan ongkos itu. Ketika jaringan terpartisi, sistem tidak bisa menjamin Consistency dan Availability sekaligus, jadi perancang harus memilih AP atau CP; kalau tidak ada partisi, keduanya masih bisa jalan berdampingan. Hal serupa terjadi pada model data: perbankan memakai strong consistency karena saldo tidak boleh keliru, sedangkan media sosial cukup dengan eventual consistency demi respons yang cepat dan layanan yang selalu tersedia. Label di slide kuliah juga tidak bisa ditelan mentah-mentah. Menurut dokumentasi resminya, MongoDB di sebagian besar konfigurasi condong ke CP, sementara Cassandra condong ke AP dengan tunable consistency yang bisa digeser mendekati CP lewat pengaturan R + W > N. Memahami sistem terdistribusi berarti menerima bahwa setiap rancangan adalah kompromi, lalu memilih kompromi yang paling cocok dengan kebutuhan aplikasinya.










































































