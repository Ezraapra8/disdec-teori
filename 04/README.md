# Ringkasan Materi Minggu 4 : Konsistensi dan Replikasi pada Sistem Terdistribusi

| Field | Keterangan |
| :--- | :--- |
| **Nama** | Ezra Adyatma Pranantio |
| **NIM** | 255410002 |

Mata Kuliah     : Sistem Terdistribusi dan Terdesntralisasi

Dosen Pengampu  : Dr. Bambang Purnomosidi D. P.

## Tujuan Pembelajaran
• Memahami dan mampu menjelaskan konsep konsistensi dalam sistem terdistribusi.

• Mampu menjelaskan berbagai model konsistensi.

• Mampu menjelaskan konsep replikasi data.

• Mengetahui dan mampu menjelaskan berbagai protokol dan strategi replikasi.

• Mampu melakukan analisis studi kasus replikasi pada sistem nyata yang banyak digunakan di industri.

---

## 1. Apa itu Konsistensi pada Sistem Terdistribusi?

<p align= "justify">Konsistensi adalah jaminan bahwa semua node memegang data yang sama, atau setidaknya tampak sama pada saat tertentu, walaupun data tersebar di banyak lokasi dan bisa diubah serentak oleh pengguna atau proses yang berbeda. Masalah ini muncul ketika sebuah aplikasi menyimpan beberapa salinan data di server yang letak fisiknya berjauhan. Begitu satu salinan berubah, sistem harus memastikan salinan lain akhirnya bernilai sama. Kata “akhirnya” berarti kesamaan tidak selalu terjadi seketika, jadi seberapa cepat kesamaan itu tercapai bergantung pada model konsistensi yang dipilih. Tanenbaum dan Van Steen (2017) mencatat bahwa replikasi melahirkan masalah konsistensi karena setiap perubahan pada satu salinan wajib dirambatkan ke salinan lain. Karena itu, konsistensi bisa dibaca sebagai kontrak antara sistem dan klien tentang apa yang boleh klien amati.

## 2. Mengapa Konsistensi Penting pada Sistem Terdistribusi?

<p align= "justify">Tanpa jaminan konsistensi, pengguna bisa melihat versi data yang berbeda tergantung server mana yang ia baca. Materi memakai contoh transfer dari rekening A ke rekening B. Server 1 sudah mengurangi saldo A, tetapi Server 2 belum menambah saldo B, sehingga nasabah gagal bertransaksi dari rekening B karena saldonya tampak kurang. Kasus ini memperlihatkan bahwa ketidakkonsistenan merusak kebenaran fungsi aplikasi, bukan sekadar tampilan layar. Pada aplikasi keuangan, jeda sinkronisasi yang singkat sekalipun sudah cukup untuk membuat sistem berperilaku salah.

## 3. Potensi Ketidaksamaan Data dan Pengantar CAP

<p align= "justify">Data yang disimpan di banyak node membuka peluang terjadinya perbedaan isi antara satu node dan node lain. Tujuannya sendiri sederhana: pengguna harus melihat data yang benar dan terbaru. Hanya saja, tujuan itu ada ongkosnya, yaitu trade-off antara Consistency, Availability, dan Partition Tolerance yang dirumuskan dalam Teorema CAP. Gilbert dan Lynch (2002) membuktikan secara formal dugaan Brewer yang melandasi teorema tersebut. Halaman ini jadi jembatan menuju pembahasan CAP. Kemungkinan data berbeda antarnode itulah yang membuat pemilihan model konsistensi menjadi keputusan desain yang berbobot.

## 4. Teorema CAP (Diagram)

<p align= "justify">Diagram berbentuk segitiga: Consistency (C) di puncak, Availability (A) di sudut kiri bawah, Partition Tolerance (P) di sudut kanan bawah. Bentuk ini melambangkan bahwa ketiga properti tidak bisa terpenuhi penuh pada saat yang sama. Setiap sisi segitiga dibaca sebagai pasangan dua properti, yakni CA, CP, atau AP, sedangkan properti ketiga harus dikorbankan. Gilbert dan Lynch (2002) menyatakan bahwa layanan web tak mungkin menjamin konsistensi, ketersediaan, dan toleransi partisi sekaligus. Diagram ini hanya alat bantu visual untuk memetakan pilihan desain, bukan prosedur dengan alur kerja.

## 5. Partisi Jaringan dan Definisi C, A, P

<p align= "justify">Materi menyebut partisi, yaitu putusnya koneksi jaringan, sebagai kejadian wajar yang hampir pasti muncul sebagus apa pun jaringannya. Akibatnya, kondisi ideal ketika C, A, dan P terpenuhi bersamaan tidak akan tercapai di dunia nyata. Consistency berarti semua node memegang data yang sama. Availability berarti data siap diakses ketika pengguna memerlukannya. Partition Tolerance berarti sistem tetap jalan walau satu atau beberapa komponen jaringan gagal atau terputus. Karena partisi tak terhindarkan, pilihan desain yang realistis berada di antara konsistensi dan ketersediaan saat partisi terjadi, sesuai formulasi Gilbert dan Lynch (2002).

## 6. Contoh Kasus CAP pada e-Commerce

<p align= "justify">Contohnya sebuah aplikasi e-commerce dengan Server A untuk wilayah barat Indonesia dan Server B untuk wilayah timur. Jaringan antarwilayah lalu terputus, dan sistem dihadapkan pada dua jalan. Kalau sistem memilih Availability (AP), pengguna di kedua wilayah tetap bisa belanja, tetapi data stok sempat tidak sinkron. Kalau memilih Consistency (CP), sistem menolak transaksi sampai jaringan pulih agar stok tetap benar, dan ketersediaan menjadi korbannya. Alurnya: partisi terdeteksi, lalu sistem melayani permintaan (AP) atau menolaknya (CP). Pilihan akhirnya tergantung pada mana yang lebih mahal bagi bisnis, data stok yang keliru atau layanan yang ditolak.

## 7. Model Konsistensi (Definisi)

<p align= "justify">Model konsistensi adalah aturan atau jaminan tentang bagaimana dan kapan perubahan data terlihat oleh node atau klien lain. Tiap model menentukan seberapa cepat seluruh salinan data menjadi sama setelah ada pembaruan. Tanenbaum dan Van Steen (2017) memandangnya sebagai kontrak antara proses dan penyimpanan data: selama proses mematuhi aturan tertentu, penyimpanan menjamin hasil baca yang bisa diprediksi. Kontrak itu memberi pengembang pegangan untuk menalar perilaku sistem. Model yang lebih kuat memberi jaminan lebih besar, tetapi koordinasinya lebih mahal.

## 8. Spektrum Model Konsistensi (Diagram)

<p align= "justify">Diagram menyusun enam model secara vertikal dari kuat ke lemah: Linearizable, Sequential, Causal, Read-your-writes, Monotonic read, dan Eventual. Makin ke bawah, jaminannya makin longgar. Di bagian atas, urutan operasi dijaga ketat dan biaya koordinasinya besar; di bagian bawah, ketersediaan dan kecepatan respons lebih tinggi. Diagram ini menjadi peta untuk halaman-halaman berikutnya, yang membahas tujuh model karena strong consistency diperlakukan lebih dulu sebagai payung bagi kelompok atas.

## 9. Strong Consistency

<p align= "justify">Pada strong consistency, begitu data diperbarui, semua node langsung melihat versi terbaru, sehingga setiap pembacaan setelah penulisan mengembalikan nilai yang sama. Implementasinya biasanya memakai protokol seperti Two-Phase Commit atau Paxos. Materi mencontohkan sistem perbankan: saat saldo berubah, semua server harus segera tahu nilai barunya. Pembaruan baru dianggap selesai setelah para node berkoordinasi, dan koordinasi inilah yang menambah latensi. Model ini cocok untuk data yang kesalahannya mahal.

## 10. Eventual Consistency

<p align= "justify">Eventual consistency tidak menjanjikan data langsung sama di semua node. Yang dijanjikan: bila tidak ada pembaruan baru dalam jangka waktu tertentu, semua salinan akhirnya sama. Model ini cocok untuk sistem yang mengutamakan availability dan kecepatan, dan dikenal lewat istilah BASE (Basically Available, Soft-state, Eventual consistency). Amazon DynamoDB dan Apache Cassandra jadi contohnya, sebab pembaruan di sana bisa terlambat menyebar tetapi akan konsisten setelah beberapa waktu. DeCandia et al. (2007) menjelaskan bahwa Dynamo sengaja memilih eventual consistency supaya operasi tulis selalu bisa diterima. Konsekuensinya ditanggung aplikasi, yang harus siap membaca data usang dan menyelesaikan konflik versi.

## 11. BASE

<p align= "justify">Basically Available berarti data tetap bisa dibaca dan ditulis meski terjadi kegagalan sebagian atau pemisahan jaringan. Soft State berarti replika tak harus konsisten setiap saat; keadaan sistem bisa berubah sendiri walau tidak ada masukan baru, karena sinkronisasi masih berjalan di belakang layar. Eventual Consistency berarti semua node akhirnya konsisten bila tidak ada pembaruan lagi setelah sekian waktu. BASE sering dihadapkan dengan ACID yang menuntut konsistensi segera. Jadi BASE adalah filosofi desain yang menukar kuatnya jaminan dengan ketersediaan dan skalabilitas.

## 12. Causal Consistency

<p align= "justify">Pada causal consistency, urutan harus dijaga kalau satu operasi bergantung pada operasi lain. Operasi yang tak berhubungan boleh dilihat dalam urutan berbeda oleh node lain. Di media sosial, misalnya, komentar atau reaksi tidak boleh tampil sebelum pos yang ditanggapinya. Hubungan sebab-akibat semacam ini lazim dilacak dengan vector clock, seperti yang sudah dibahas pada materi sinkronisasi. Karena hanya operasi yang saling berkaitan yang diurutkan, ongkosnya lebih murah daripada sequential consistency, namun logika aplikasi tetap terlindungi.

## 13. Read-Your-Writes Consistency

<p align= "justify">Setelah pengguna menulis data, pembacaan berikutnya oleh pengguna yang sama pasti menampilkan tulisannya. Pengguna lain belum tentu melihat hasil yang sama pada saat itu. Pada aplikasi pesan, pesan yang baru dikirim langsung muncul di layar pengirim walaupun penerima belum melihatnya. Jaminan ini berlaku per klien, jadi cukup dengan mengarahkan pembacaan pengguna ke replika yang sudah memuat tulisannya. Tanpa jaminan tersebut, pengguna akan merasa pembaruannya hilang.

## 14. Monotonic Read Consistency

<p align= "justify">Model ini menjamin pengguna yang membaca data berkali-kali tidak akan menjumpai versi yang lebih lama daripada yang pernah ia lihat. Timeline media sosial yang tidak “mundur” ke versi lama setelah di-refresh adalah contohnya. Tanpa jaminan ini, bacaan berturut-turut yang dilayani replika berbeda bisa membuat data tampak kembali ke masa lalu. Semua klien tidak harus melihat data yang sama; satu klien hanya perlu melihat kemajuan yang tak pernah berbalik. Ongkosnya rendah, tetapi konsistensi sesi yang didapat sangat terasa bagi pengguna.

## 15. Sequential Consistency

<p align= "justify">Pada sequential consistency, semua operasi tampak dieksekusi dalam satu urutan yang sama bagi semua node, tanpa keharusan mengikuti waktu nyata (real-time). Urutan penulisan berperan besar, tetapi sinkronisasinya boleh tertunda. Di media sosial, semua pengguna melihat urutan pos yang sama walau ada sedikit jeda. Tanenbaum dan Van Steen (2017) mensyaratkan satu urutan global yang selaras dengan urutan program tiap proses. Perbedaan dengan linearizability ada pada hubungan ke waktu nyata: sequential consistency tidak terikat padanya.

## 16. Linearizability

<p align= "justify">Linearizability adalah varian terkuat dari strong consistency. Tiap operasi tampak berlangsung atomik dan seketika, dengan urutan yang sama bagi semua node. Begitu penulisan selesai, setiap pembacaan sesudahnya, oleh klien mana pun, mencerminkan penulisan itu atau yang lebih baru; sistem terdistribusi pun tampak seperti satu node atomik dengan urutan global menurut waktu nyata. Etcd, ZooKeeper, dan Spanner memakai model ini. Contoh penerapannya pengecekan keunikan ID, yakni memastikan dua pengguna tak bisa mendaftarkan username yang sama pada saat bersamaan. Harganya mahal, sebab setiap operasi memerlukan koordinasi.

## 17. Trade-off Latensi dengan Konsistensi

<p align= "justify">Makin tinggi tingkat konsistensi yang dijamin, makin besar latensi yang harus ditanggung pengguna. Materi meringkasnya begini: konsistensi naik, latensi naik; konsistensi turun, latensi turun. Penyebab utamanya komunikasi antarnode yang harus beres sebelum respons keluar. Coulouris et al. (2012) mengaitkannya dengan fakta bahwa menjaga replika tetap sinkron butuh pertukaran pesan, dan pertukaran itu memakan waktu. Karena itu tidak ada model konsistensi yang unggul untuk semua kasus.

## 18. Konsistensi Tinggi vs Konsistensi Lemah

<p align= "justify">Pada konsistensi tinggi (strong consistency), sistem memastikan semua node memegang data yang sama sebelum menjawab. Sistem menunggu sinkronisasi antarserver selesai; komunikasi lintas jaringan itu membuat latensinya tinggi. Pada konsistensi lemah (eventual consistency), sistem menjawab saat itu juga tanpa menunggu semua node sinkron. Data bisa sementara berbeda di beberapa tempat, jadi latensinya rendah tetapi ada risiko data belum yang terbaru. Alur logikanya: di konsistensi tinggi, respons menunggu replikasi; di konsistensi lemah, respons mendahului replikasi.

## 19. Contoh Dua Pusat Data (Jakarta dan Yogyakarta)

<p align= "justify">Skenarionya: pengguna mengubah data di Jakarta, sistem ingin perubahan itu juga sampai di Yogyakarta, dan sesudahnya datang permintaan baca. Dengan strong consistency, sistem menunggu sampai Jakarta dan Yogyakarta identik baru menjawab; datanya konsisten, tetapi responsnya lambat. Dengan eventual consistency, sistem menjawab langsung dari Jakarta atau Yogyakarta lalu menyusulkan pembaruan ke Yogyakarta belakangan, sehingga responsnya cepat tetapi datanya bisa berbeda untuk sementara. Faktor penentunya latensi antarpusat data, dan contoh ini membuat trade-off halaman sebelumnya jadi konkret. Pilihan yang tepat tergantung seberapa tahan aplikasi terhadap data usang.

## 20. Pengertian Replikasi Data

<p align= "justify">Replikasi data adalah proses membuat dan merawat salinan data yang sama di beberapa node atau server dalam sistem terdistribusi. Tujuannya supaya sistem tetap tersedia, andal, dan cepat walaupun salah satu node gagal. Contohnya basis data utama di Jakarta yang ditambah salinan di Yogyakarta dan Denpasar demi high availability. Menjaga semua salinan tetap sama adalah inti dari replikasi. Coulouris et al. (2012) menyebut kinerja, ketersediaan, dan toleransi kesalahan sebagai motivasi utamanya. Masalah konsistensi lahir dari keberadaan banyak salinan itu sendiri.

## 21. Diagram Arsitektur Replikasi (Source dan Replica)

<p align= "justify">Diagram menggambarkan satu pengguna, satu basis data Source, dan sejumlah replika (Replica #1 sampai #N). Operasi writes mengarah ke Source, sedangkan reads mengarah ke replika. Dari Source mengalir Replication ke semua replika, membawa perubahan data. Alurnya: klien menulis ke sumber, sumber menyebarkan perubahan ke replika, klien membaca dari replika. Beban tulis dan beban baca jadi terpisah, sehingga arsitektur ini cocok untuk beban kerja yang didominasi pembacaan. Gambar bersumber dari artikel Medium tentang replikasi MySQL, seperti tertera di slide.

## 22. Tujuan Replikasi Data

<p align= "justify">Replikasi punya empat tujuan. Satu, ketersediaan: ketika satu server gagal, server lain masih menyimpan salinan data. Dua, kinerja: pengguna bisa membaca dari server terdekat sehingga lebih cepat. Tiga, reliabilitas: risiko kehilangan data turun karena cadangan tersebar di banyak tempat. Empat, skalabilitas: beban baca terbagi ke beberapa server. Tanenbaum dan Van Steen (2017) juga menempatkan keandalan dan kinerja sebagai alasan utama, dengan catatan bahwa bayarannya adalah usaha menjaga konsistensi antarsalinan.

## 23. Jenis Replikasi

<p align= "justify">Materi membedakan empat jenis. Master–Slave (Primary–Secondary): satu master menulis, slave hanya membaca; konsistensinya kuat tetapi master menjadi bottleneck, dan PostgreSQL streaming replication contohnya. Multi-Master (Peer-to-Peer): semua node bisa menulis dan membaca, fleksibel tetapi rawan konflik data, dengan contoh PostgreSQL logical replication, Apache Cassandra, MongoDB, dan Apache Ignite. Synchronous replication menulis pembaruan ke semua node sebelum dianggap selesai; konsisten tetapi lambat, seperti Google Spanner. Asynchronous replication mengirim pembaruan setelah transaksi di master selesai; cepat, tetapi data bisa tertunda, seperti mode asinkron MySQL dan DynamoDB.

## 24. Strategi Sinkronisasi Replika

<p align= "justify">Strategi sinkronisasi dikelompokkan menurut empat sudut pandang. Menurut waktu ada synchronous, asynchronous, dan semi-synchronous. Menurut arah ada master-slave, master-master (peran dan kemampuan setara), serta master-master asymmetric (peran dan kemampuan berbeda). Menurut cara sinkronisasi ada pull-based, push-based, dan hybrid. Terakhir, penanganan konflik memakai LWW (Last Write Wins), Vector Clock, CRDT, atau Operational Transformation. Pengelompokan ini membantu perancang memilih kombinasi yang cocok dengan kebutuhan konsistensi dan kinerja.

## 25. Waktu Sinkronisasi

<p align= "justify">Pada mode asinkron, sumber melakukan commit tanpa menunggu replika; performanya tercepat, tetapi risiko kehilangan data paling tinggi. Mode semi-sinkron membuat sumber menunggu satu replika menerima data, dan hasilnya keseimbangan yang baik antara konsistensi dan performa; menurut materi, MySQL memakai mode ini. Mode sinkron membuat sumber menunggu semua replika selesai melakukan commit, sehingga konsistensinya tertinggi dengan performa terendah. Urutannya sama di ketiga mode: commit lokal dulu, lalu sumber menunggu nol, satu, atau seluruh replika sebelum mengabari klien. Selisih antarmode itu menggambarkan spektrum antara kecepatan dan keawetan data.

## 26. Penanganan Konflik

<p align= "justify">Konflik muncul ketika beberapa pembaruan konkuren menyentuh data yang sama. LWW memilih pembaruan ber-timestamp terbaru dan membuang sisanya; sederhana, tetapi bisa membuang pembaruan yang sah. Vector clock mengurutkan kejadian untuk mendeteksi hubungan sebab-akibat dan konkurensi, seperti dibahas pada kuliah sebelumnya, dan Dynamo memakainya (DeCandia et al., 2007). CRDT menaruh struktur data di tiap node yang dirancang supaya penggabungan selalu konvergen tanpa koordinator pusat, dan Yjs untuk penyunting kolaboratif seperti Google Docs adalah contohnya. OT mentransformasikan operasi berdasarkan informasi lokal supaya hasil akhir identik.

## 27. Cara Sinkronisasi
<p align= "justify">Pada pull-based, replika memeriksa sumber secara berkala; ada pembaruan, replika memperbarui diri, tidak ada, replika diam saja. Pada push-based, sumber yang aktif mengirim pembaruan ke replika setiap kali ada perubahan, dengan CDC (Change Data Capture) terpasang di sumber dan pengiriman lewat WebSockets atau SSE (Server-Sent Events). Pendekatan hybrid mencampur keduanya. Pull sederhana tetapi punya jeda sepanjang interval pemeriksaan; push lebih cepat, hanya saja butuh mekanisme pengiriman yang andal.

## 28. Contoh CRDT

<p align= "justify">Dua pihak, Agnes Monica dan Bambang, menghitung jumlah akses ke sebuah situs, masing-masing dengan peta lokal berisi hitungan per orang. Agnes mencatat 5 pengunjung ({“Agnes Monica”: 5, “Bambang”: 0}), Bambang mencatat 3 ({“Agnes Monica”: 0, “Bambang”: 3}). Saat sinkronisasi, aturan penggabungannya mengambil nilai maksimum per entri: max(5, 0) = 5 dan max(0, 3) = 3, jadi kedua pihak berakhir dengan {“Agnes Monica”: 5, “Bambang”: 3} dan total 5 + 3 = 8. Operasi max bersifat komutatif, asosiatif, dan idempoten, sehingga urutan penggabungan tak memengaruhi hasil. Satu catatan: keadaan awal di slide memakai kunci “Alice” dan “Bob”, kemungkinan sisa penamaan lama yang tak cocok dengan nama pelakunya.

## 29. Contoh OT

<p align= "justify">Dokumen awal berisi “abc”. Secara bersamaan, Agnes menyisipkan “x” di indeks 0 sehingga salinannya menjadi “xabc”, dan Bambang menghapus “c” di indeks 2 sehingga salinannya menjadi “ab”. Tanpa transformasi, “hapus indeks 2” pada “xabc” malah menghapus “b” dan menghasilkan “xac”, sementara “sisipkan x di indeks 0” pada “ab” menghasilkan “xab”; kedua salinan pun berbeda. Dengan OT, operasi hapus dari Bambang diubah di sisi Agnes menjadi “hapus indeks 3”, karena ada sisipan di depan, dan hasilnya “xab”. Di sisi Bambang, sisipan dari Agnes diterapkan di indeks 0 sesudah penghapusan, dan hasilnya juga “xab”, jadi kedua dokumen konvergen.

## 30. Protokol Replikasi

<p align= "justify">Materi mendaftar lima protokol replikasi. Primary-based (Master-Slave) menunjuk satu node untuk menulis dan membaca, sedangkan node lain hanya membaca. Quorum-based mereplikasi data ke kuorum, yaitu sejumlah node yang disepakati. Tiga sisanya adalah State Machine Replication, Gossip Protocol, dan Chain Replication. Tanenbaum dan Van Steen (2017) serta Coulouris et al. (2012) membahas protokol berbasis primer dan berbasis kuorum sebagai pendekatan dasar. Kelimanya diurai satu per satu pada halaman berikutnya.

## 31. Quorum-based

<p align= "justify">Pada quorum-based replication, operasi tulis dan baca dianggap berhasil hanya jika sejumlah node tertentu mengonfirmasinya. Syaratnya W + R > N, dengan W jumlah minimal node untuk tulis, R jumlah minimal untuk baca, dan N jumlah seluruh node. Contohnya N = 3 (Node A, B, C), W = 2, dan R = 2, sehingga 2 + 2 > 3 terpenuhi. Syarat itu menjamin himpunan node penulisan dan himpunan node pembacaan selalu beririsan di setidaknya satu node. Dynamo juga memakai konfigurasi ini, dengan N, R, dan W yang bisa disetel (DeCandia et al., 2007).

## 32. Skenario Operasi Tulis pada Kuorum
<p align= "justify">Klien mengirim perintah tulis x = 100 ke klaster, dan permintaan itu diteruskan paralel ke Node A, B, dan C. Node A dan Node B berhasil menyimpan lalu membalas ACK, sedangkan Node C lambat atau kena timeout. Dua ACK sudah terkumpul dan W = 2 terpenuhi, maka sistem menyatakan penulisan berhasil, walaupun Node C belum selesai memproses. Sistem tetap bisa menerima tulisan meski satu node bermasalah. Begitulah toleransi kegagalan bekerja pada konfigurasi kuorum.

## 33. Skenario Operasi Baca pada Kuorum
<p align= "justify">Klien yang ingin membaca x meminta data dari minimal dua node, karena R = 2. Anggap klien membaca dari Node A dan Node C. Node A mengembalikan versi terbaru (x = 100), sedangkan Node C mungkin masih menyimpan versi lama atau belum menerima data. Penulisan sebelumnya melibatkan Node A, jadi data terbaru pasti ikut terbaca lewat irisan kedua himpunan. Ketika hasilnya berbeda, sistem memilih versi terbaru, misalnya berdasarkan nomor versi atau timestamp. Alur ini memperlihatkan mengapa W + R > N menjamin setiap pembacaan memuat setidaknya satu salinan terbaru.

## 34. State Machine Replication
<p align= "justify">Pendekatan ini menjaga high availability lewat beberapa replika yang mengeksekusi perintah sama dalam urutan sama. Server utama menerima permintaan klien, mencatatnya ke log, dan memberinya urutan, lalu replika menerapkan masukan terurut itu. Pada contoh, Replika A, B, dan C sama-sama berawal dari x = 10. Klien mengirim SET x = 20 ke leader (Replika A), yang memberi nomor urut, mencatatnya ke log, dan meneruskan perintah ke B dan C. Setelah A menerima konfirmasi dari B dan C, perintah dianggap “dikomit”; ketiga replika mengeksekusi SET x = 20 sehingga statusnya berubah dari 10 menjadi 20, dan leader mengirim hasil akhir ke klien. Coulouris et al. (2012) membahas pendekatan ini sebagai replikasi yang menerapkan urutan operasi sama pada setiap replika.

## 35. Gossip Protocol
<p align= "justify">Protokol gossip dipakai banyak basis data terdistribusi, termasuk Apache Cassandra, untuk memeriksa keberadaan node dan mereplikasi data. Node A mendeteksi perubahan, misalnya entri data yang diperbarui atau tetangga yang down. Tiap detik, Node A memilih 1–3 peer acak (katakanlah Node B) dan membagikan statusnya, termasuk pembaruan baru. Node B memperbarui statusnya sendiri lalu memilih Node C secara acak untuk meneruskan kabar yang sama. Setelah beberapa putaran, seluruh node di klaster cepat mengetahui status baru; penyebarannya probabilistik, tak bergantung pada koordinator pusat, dan mudah diskalakan. Dynamo memakai protokol serupa untuk keanggotaan node (DeCandia et al., 2007).

## 36. Chain Replication (Diagram)
<p align= "justify">Diagram menggambarkan klien dan rantai node berurutan: head, A, B, C, tail. Klien mengirim write ke head, yang meneruskannya berurutan sepanjang rantai sampai tail. Setelah penulisan tiba di tail, konfirmasi (ack) kembali ke klien, dan tail pula yang melayani read. Karena tail hanya memegang data yang sudah melewati seluruh rantai, pembacaan darinya menjamin konsistensi kuat. Van Renesse dan Schneider (2004) merancang protokol ini untuk throughput tinggi dan ketersediaan tanpa melepas konsistensi kuat. Gambar bersumber dari artikel Medium tentang replikasi rantai pada Azure Storage, sebagaimana tertera di slide.

## Kesimpulan :
<p align= "justify">Masalah konsistensi pada sistem terdistribusi lahir dari kebiasaan menyimpan banyak salinan data di server yang letaknya berjauhan. Setiap perubahan pada satu salinan harus sampai ke salinan lain, dan selama itu belum terjadi, pengguna bisa membaca versi yang berbeda; contoh transfer dari rekening A ke B memperlihatkan bahwa akibatnya merusak fungsi aplikasi, bukan sekadar tampilan. Teorema CAP menjelaskan mengapa masalah ini tidak punya jawaban yang sempurna: Consistency, Availability, dan Partition Tolerance tidak bisa terpenuhi penuh bersamaan. Karena partisi jaringan hampir pasti terjadi, perancang sistem sebenarnya memilih antara konsistensi atau ketersediaan, seperti pada contoh e-commerce yang harus menolak transaksi demi stok yang benar, atau tetap melayani pembeli dengan risiko stok sempat tidak sinkron.

<p align= "justify">Pilihan itu diterjemahkan lewat model konsistensi, yang tersusun dari linearizable sampai eventual. Makin kuat jaminannya, makin banyak komunikasi antarnode yang harus beres sebelum sistem menjawab, jadi latensinya ikut naik. Strong consistency cocok untuk data yang kesalahannya mahal seperti saldo bank, sedangkan eventual consistency yang dipakai DynamoDB dan Cassandra mengutamakan kecepatan dan menyerahkan urusan data usang kepada aplikasi. Tidak ada model yang unggul di semua kasus; keputusannya bergantung pada seberapa tahan aplikasi terhadap data yang belum terbaru.

<p align= "justify">Replikasi menjadi cara untuk mendapatkan ketersediaan, kinerja, keandalan, dan skalabilitas, tetapi bayarannya adalah usaha menjaga semua salinan tetap sama. Mode sinkron, semi-sinkron, dan asinkron menentukan seberapa jauh sistem menukar keawetan data dengan kecepatan, sementara konflik dari pembaruan yang berjalan bersamaan bisa diselesaikan dengan LWW, vector clock, CRDT, atau OT. Contoh CRDT memperlihatkan hitungan Agnes (5) dan Bambang (3) bergabung menjadi 8 lewat operasi max, dan pada OT dokumen “abc” berakhir sama-sama “xab” di kedua sisi. Dari sisi protokol, quorum-based dengan syarat W + R > N menjamin setiap pembacaan menyentuh setidaknya satu salinan terbaru, dan state machine replication, gossip, serta chain replication menawarkan cara lain untuk menjaga urutan dan penyebaran data.













































































