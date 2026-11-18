# Ringkasan Materi Minggu 3 : Sinkronisasi dan Koordinasi pada Sistem Terdistribusi

| Field | Keterangan |
| :--- | :--- |
| **Nama** | Ezra Adyatma Pranantio |
| **NIM** | 255410002 |

Mata Kuliah     : Sistem Terdistribusi dan Terdesntralisasi

Dosen Pengampu  : Dr. Bambang Purnomosidi D. P.

## Tujuan Pembelajaran
• Memahami dan mampu menjelaskan mengapa
diperlukan sinkronisasi untuk koordinasi pada sistem
terdistribusi.

• Memahami dan mampu menjelaskan berbagai
tantangan sinkronisasi untuk koordinasi pada sistem
terdistribusi.

• Memahami dan mampu menjelaskan sinkronisasi
waktu fisik dan bisa menyebutkan serta menjelaskan
berbagai algoritma dan protokolnya.

• Memahami dan mampu menjelaskan sinkronisasi
waktu logis dan bisa menyebutkan dan menjelaskan
beberapa algoritma terkait.

---

## 1. Apa itu Sinkronisasi?

<p align= "justify">Sinkronisasi adalah koordinasi proses, data, dan sumber daya di banyak node supaya operasi tetap konsisten, andal, dan bebas konflik data saat proses berjalan bersamaan. Ilustrasinya ada di e-commerce: Produk X punya stok 10 unit, lalu User A dan User B sama-sama membeli 1 unit di waktu yang sama. Proses A membaca stok 10, dan proses B ikut membaca 10 sebelum A sempat memperbarui basis data. Hasilnya keduanya menulis stok 9, padahal seharusnya sisa 8. Anomali ini disebut lost update, yaitu satu pembaruan hilang karena tidak ada kontrol konkurensi dan isolasi transaksi, dan teori race condition membahasnya.

## 2. Mengapa Sinkronisasi Penting

<p align= "justify">Ada sembilan alasan utama: menjaga integritas data dari data race, menyelaraskan state antar-node, mengoordinasikan tugas, mengelola sumber daya bersama, mendukung pemulihan lewat state yang terjaga, mencegah operasi redundan, mendukung load balancing, mencegah deadlock, dan memungkinkan sistem berskala besar. Contoh nyatanya adalah game online yang menyebarkan posisi, kesehatan, dan inventaris pemain ke semua klien, serta aplikasi web yang menyamakan keranjang belanja antar perangkat dan sesi. Dalam teori desain sistem terdistribusi, semua alasan ini bertumpu pada keandalan, model konsistensi (berpusat pada data maupun klien), dan skalabilitas. Pemulihan setelah kegagalan juga hanya valid kalau replika atau koordinator pengganti memegang catatan state yang konsisten.

## 3. Data Race

<p align= "justify">Data race terjadi di level memori. Thread 1 membaca x = 10 dan menghitung 11, lalu Thread 2 membaca x = 10 sebelum Thread 1 sempat menulis dan menghitung 11 juga. Keduanya menulis x = 11, padahal hasil yang benar 12, jadi satu increment hilang. Menurut teori race condition, data race muncul ketika dua thread atau lebih mengakses lokasi memori yang sama secara bersamaan tanpa urutan happened-before dari primitif sinkronisasi, dan minimal satu akses berupa write. Penyebabnya, operasi read-modify-write tidak berjalan atomik dan tidak dilindungi kunci eksklusif, sehingga perilaku program menjadi undefined.

## 4. Race Condition

<p align= "justify">Race condition terjadi di level logika aplikasi. Dalam contoh bank, saldo $100 dan dua thread masing-masing menarik $80. Keduanya mengecek kondisi ($100 >= $80) dan lolos, lalu Thread A mengurangi saldo menjadi $20, dan Thread B mengurangi lagi hingga saldo akhir -$60. Akibatnya terjadi penarikan ganda dan saldo negatif. Dalam klasifikasi teori race condition, kasus ini masuk high-level race condition: akses ke variabel mungkin aman di memori, tetapi celah waktu antara pengecekan dan aksi (check-then-act) merusak logika bisnis. Solusinya menggabungkan pengecekan dan pembaruan saldo dalam satu transaksi atomik, atau memakai penguncian transaksi di basis data.

## 5. Perbandingan Data Race dan Race Condition

<p align= "justify">Program yang bebas data race belum tentu bebas race condition, karena keduanya bekerja di level berbeda.
<br><br>

| Aspek | Data Race | Race Condition |
|---|---|---|
| Fokus | Akses memori secara bersamaan | Urutan dan waktu eksekusi instruksi |
| Level | Rendah (CPU, akses memori fisik) | Tinggi (alur logika dan business logic) |
| Syarat terjadi | Minimal dua thread mengakses memori yang sama, minimal satu write, tanpa mutex/lock | Kebenaran program bergantung pada urutan eksekusi yang tak bisa diprediksi |
| Dampak | Undefined behavior, korupsi memori, data tidak konsisten | Keluaran salah dan bug logika bisnis (saldo minus, eksekusi ganda) |
| Solusi | Mutex/lock atau tipe data atomic | Desain ulang alur kontrol: atomic check-and-act atau penguncian transaksi database |

## 6. Multithreading Python dan Non-Determinisme

<p align= "justify">Program multithreads-example.py (30 baris) mengimpor modul threading dan time, lalu mendefinisikan fungsi task(name, delay). Fungsi ini mencetak pesan mulai, menjalankan time.sleep(delay) untuk mensimulasikan operasi I/O-bound, lalu mencetak pesan selesai. Di bagian utama, program membuat lima thread (t1 sampai t5) untuk tugas A sampai E dengan delay 5 detik masing-masing, menjalankannya dengan .start(), dan memanggil .join() supaya thread utama menunggu semuanya selesai sebelum mencetak “All tasks completed.”. Dua kali eksekusi menunjukkan tugas selalu mulai berurutan A sampai E, tapi urutan selesainya berbeda: A, B, D, E, C pada percobaan pertama dan A, B, E, C, D pada percobaan kedua. Ini bukti bahwa penjadwalan sistem operasi membuat waktu selesai thread tidak bisa diprediksi tanpa mekanisme koordinasi tambahan.

## 7. Deadlock pada Sistem Terdistribusi

<p align= "justify">Resource Allocation Graph dalam materi menghubungkan tiga proses dengan tiga sumber daya. R1 dipegang Process 1 yang menunggu R2, R2 dipegang Process 2 yang menunggu R3, dan R3 dipegang Process 3 yang menunggu R1. Karena tidak ada proses yang mau melepas sumber daya sebelum mendapat yang ditunggu, ketiganya berhenti permanen. Dalam teori deteksi deadlock terdistribusi, siklus tertutup pada wait-for graph (WFG) adalah syarat perlu dan cukup terjadinya deadlock, baik pada model permintaan tunggal maupun model AND. Deteksinya butuh algoritma lintas node, yaitu edge-chasing (mengirim pesan probe sepanjang tepi WFG) atau diffusing computation, supaya siklus ketemu dalam waktu terbatas tanpa melaporkan false deadlock akibat latensi pesan.

## 8. Tantangan Sinkronisasi

<p align= "justify">Ada enam tantangan. Latensi dan partisi jaringan muncul karena node hanya berkomunikasi lewat pesan yang tertunda dan bisa terputus, sehingga klaster terpecah jadi partisi terisolasi. Skalabilitas menuntut mekanisme sinkronisasi tidak jadi bottleneck saat node bertambah, sedangkan fault tolerance menuntut sistem tetap benar walau sebagian proses atau tautan gagal. Concurrency control dan konsistensi data menjaga operasi paralel agar tidak melanggar model konsistensi yang disepakati. Terakhir, sinkronisasi waktu jadi masalah karena osilator lokal tiap komputer punya laju yang melenceng, sehingga tidak ada waktu global yang tunggal.

## 9. Sinkronisasi Waktu Fisik dan Standar Waktu

<p align= "justify">Sinkronisasi waktu fisik menyelaraskan real-time clock dengan standar waktu bersama agar log, penjadwalan event, dan konsistensi sistem tetap terjaga. Caranya lewat protokol jaringan atau penguncian langsung ke atomic clock maupun sinyal GPS yang tersinkron dengannya. Ada tiga standar: GMT yang tidak lagi jadi standar teknis, UTC yang ditetapkan ITU dan berlaku sejak 1963 berbasis TAI, serta LTC yang masih dikembangkan. Jam atom mengunci osilator pada frekuensi transisi sesium-133 atau rubidium-87 untuk menstabilkan TAI, sementara UTC menambah leap second supaya tetap sejalan dengan rotasi Bumi, meski rencana penghapusan leap second sudah mengemuka di pertemuan metrologi 2026.

<p align= "justify">Untuk LTC, OSTP Gedung Putih pada April 2024 meminta NASA merampungkan strategi implementasinya pada akhir 2026, karena jam di Bulan berdetak rata-rata 58,7 mikrodetik per hari lebih cepat akibat relativitas umum. IAU lalu mengesahkan Resolusi II (skala Lunar Coordinate Time) dan Resolusi III (kesepakatan standar waktu bulan terkoordinasi) di Majelis Umum ke-32 pada Agustus 2024. CGPM ke-28 pada Oktober 2026 menyusul dengan Resolusi 6 tentang definisi skala waktu referensi bulan dan ketertelusurannya terhadap UTC.

## 10.Etimologi UTC

<p align= "justify">Singkatan resminya UTC, bukan CUT (dari Coordinated Universal Time) dan bukan TUC (dari Temps Universel Coordonné). Pada 1967, CCIR serta Komisi 4 dan 31 IAU di Majelis Umum ke-13 menyetujui nama Inggris dan Prancis beserta satu akronim bersama. Pada 1970, kelompok penasihat teknis di bawah ITU merumuskan sistemnya dan memilih UTC sebagai kompromi yang tidak memihak bahasa mana pun, sekaligus cocok dengan pola penamaan UT0, UT1, UT2, dan UT1R. Referensi tunggal seperti ini menghilangkan ambiguitas stempel waktu lintas wilayah dan infrastruktur telekomunikasi.

## 11. Ekosistem Sumber Jam Atom dan Antarmuka TTY Linux

<p align= "justify">Rantai sinkronisasinya dua arah: Atomic Clock, jam atom di GPS, GPS Dongle, Linux Server, lalu Client. Akurasi mengalir dari master atomic clock di darat ke jam atom satelit, sinyal waktu GPS, dongle, server NTP/PTP, dan berakhir di sistem operasi klien. Materi menyebut 30-an lebih sumber jam atom di orbit dan darat, termasuk 31 satelit GPS di orbit MEO.

## 12 Kesimpulan 

<p align= "justify">Sinkronisasi menjaga proses, data, dan sumber daya di banyak node tetap terkoordinasi, dan kasus stok e-commerce menunjukkan akibatnya kalau koordinasi itu hilang: dua pembelian hanya mengurangi stok satu unit. Masalah semacam ini muncul dalam dua bentuk. Data race terjadi di level memori ketika thread mengakses lokasi yang sama tanpa pengurutan dan minimal satu akses menulis, sedangkan race condition terjadi di level logika bisnis, seperti penarikan saldo yang berujung -$60 karena ada jeda antara pengecekan dan aksi. Karena levelnya beda, program yang sudah bebas data race masih bisa mengandung race condition, dan solusinya pun lain: mutex atau tipe atomic untuk yang pertama, transaksi atomik atau penguncian database untuk yang kedua. Percobaan multithreading Python memperlihatkan sisi lainnya, sebab kelima tugas selalu mulai berurutan tetapi selesai dalam urutan acak di tiap eksekusi akibat penjadwalan sistem operasi. Deadlock melengkapi gambaran ini. Siklus tertutup pada wait-for graph membuat proses saling menunggu tanpa ujung, dan di sistem terdistribusi deteksinya butuh algoritma seperti edge-chasing agar tidak salah melaporkan deadlock akibat latensi pesan.

<p align= "justify">Di luar urusan konkurensi, sinkronisasi di sistem terdistribusi menghadapi tantangan dari latensi dan partisi jaringan, skalabilitas, fault tolerance, kontrol konkurensi, konsistensi data, dan waktu. Soal waktu, osilator tiap komputer punya laju yang melenceng sehingga tidak ada jam global yang tunggal. Komputer menyelaraskan jam fisiknya ke standar bersama, yaitu UTC yang berbasis TAI, dikunci pada jam atom, dan memakai leap second untuk mengejar rotasi Bumi. Standar ini masih berkembang: para ahli menyiapkan LTC karena jam di Bulan berdetak sekitar 58,7 mikrodetik per hari lebih cepat, dan IAU serta CGPM sudah mengeluarkan resolusi untuk itu. Akronim UTC sendiri lahir sebagai kompromi Inggris-Prancis, dan referensi tunggal seperti ini membantu menghindari ambiguitas stempel waktu lintas wilayah. Sinkronisasi tidak bisa mengandalkan satu mekanisme saja, karena sistem terdistribusi butuh kontrol akses yang benar di level memori dan logika, ditambah referensi waktu yang sama di semua node.




































