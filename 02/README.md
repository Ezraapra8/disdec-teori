# Ringkasan Materi Minggu 2 : Proses dan Komunikasi Antar Proses
pada Sistem Terdistribusi

| Field | Keterangan |
| :--- | :--- |
| **Nama** | Ezra Adyatma Pranantio |
| **NIM** | 255410002 |

Mata Kuliah     : Sistem Terdistribusi dan Terdesntralisasi

Dosen Pengampu  : Dr. Bambang Purnomosidi D. P.

## Tujuan Pembelajaran
• Mampu menjelaskan konsep proses pada sistem
terdistribusi<br>
• Mampu menjelaskan berbagai jenis komunikasi
antar proses (IPC)<br>
• Mampu menganalisis isu sinkronisasi, reliabilitas,
dan keamanan pada sistem terdistribusi<br>
• Mampu mengaitkan konsep ke contoh nyata
seperti microservices, cloud, maupun distributed
messaging.

---

## 1. Apa Itu Proses?

<p align ="justify">Menjelaskan apa itu proses lewat tangkapan layar htop di Linux dan dua diagram yang merujuk ke laman Wikipedia Process (computing). Proses adalah program yang sedang dijalankan. Program sendiri hanya berkas berisi deretan instruksi (Instruction #1 sampai Instruction #N) yang diam di Disk dan belum melakukan apa-apa. Begitu dijalankan, sistem operasi memuatnya ke RAM mulai dari lokasi 1000... dan membuatnya menjadi Process, lalu CPU mengeksekusi instruksinya satu per satu. Karena itu satu program bisa melahirkan beberapa proses sekaligus, dan tiap proses punya ruang memori yang terpisah dari yang lain. Di dalam proses ada thread, yaitu unit eksekusi yang lebih ringan, dan satu proses boleh punya banyak thread. Htop memperlihatkan semua ini pada mesin yang sedang menyala. Bagian atasnya menunjukkan kondisi sistem: 12 inti CPU (indeks 0 sampai 11), memori fisik terpakai 4.86G dari 62.5G, swap 0K dari 3.00G, serta ringkasan Tasks: 74, 780 thr, 215 kthr dengan 1 proses berstatus running. Load average 0.76 1.01 1.04 menggambarkan seberapa sibuk CPU dalam beberapa menit terakhir, sedangkan uptime 01:28:31 berarti mesin sudah menyala sekitar satu setengah jam. 

<p align ="justify">Tabel di bawahnya mendaftar proses yang aktif, dengan kolom PID (nomor identitas proses), USER (pemilik proses), PRI dan NI (prioritas), VIRT, RES, dan SHR (pemakaian memori), S (status, misalnya S untuk sleeping dan R untuk running), CPU% dan MEM% (porsi pemakaian), TIME+ (total waktu CPU), dan Command (perintah yang dijalankan). Contoh barisnya antara lain /usr/lib/firefox/firefox, /usr/lib/Xorg, /usr/bin/dinit, /usr/bin/dbus-daemon, /usr/bin/NetworkManager, /usr/bin/pipewire, dan htop sendiri. Diagram kedua menjelaskan bagaimana banyak thread bisa berbagi satu CPU. Process #1, Process #2, sampai Process #M masing-masing menampung thread berprioritas high, med, atau low, dan RAM menyimpan tabel pointer ke thread-thread itu beserta prioritasnya. Scheduler, bagian dari sistem operasi yang bertugas mengatur giliran, mengumpulkan pointer tersebut (Collect pointers), memilih thread yang akan dieksekusi (Select for execution), lalu berpindah dari satu thread ke thread lain lewat Context Switch. Kalau ada thread berprioritas lebih tinggi yang butuh CPU, scheduler boleh menghentikan thread yang sedang jalan; mekanisme ini disebut Preemption. Dengan cara ini banyak proses terlihat berjalan bersamaan padahal CPU mengerjakannya bergantian, dan pembedaan antara program, proses, dan thread inilah yang menjadi dasar konkurensi pada sistem komputasi.

## 2. Manajemen Proses

<p align ="justify">Membahas tiga tugas sistem operasi dalam mengurus proses, yaitu Process creation, Process migration, dan Process monitoring, lengkap dengan diagram alur dan tabel pemantauan. Pada Process Creation, permintaan Create Process dari User / Application masuk ke Operating System (Process Management) yang berdiri di atas Hardware (CPU, Memory, Storage, Network). Sistem operasi lalu mengalokasikan sumber daya (memory, files, etc.), menyiapkan struktur kontrol eksekusi (PCB, registers, stack), dan menaruh New Process pada status Ready to Run. Pada Process Migration, proses yang sedang berjalan atau siap dipindahkan dari CPU 0 di Node A (tempat Process A, B, dan C berada) ke CPU 0 di Node B (yang sudah dihuni Process D dan E). Status proses, resource bindings, dan lokasi proses ikut diperbarui, tujuannya menyeimbangkan beban, menaikkan kinerja, atau menangani kegagalan. 

<p align ="justify">Process Monitor melacak status proses, pemakaian sumber daya, dan performa eksekusi supaya semuanya berjalan semestinya, dan bisa mengambil tindakan korektif kalau ada yang tidak beres. Metrik agregatnya di halaman ini: CPU Usage 45%, Memory Usage 62%, dan Process Count 5 proses. Menurut teori dasar sistem operasi, Process Control Block (PCB) harus selalu terjaga dan resource bindings harus diperbarui setiap kali proses pindah; tanpa itu, migrasi dan pemantauan antar-node tidak akan konsisten. Rincian tabel pemantauan proses pada Process Monitor di halaman ini disajikan sebagai berikut.

## 3. Sistem Terdistribusi dan Komunikasi

<p align ="justify">Mendefinisikan sistem terdistribusi sebagai kumpulan node independen yang menjalankan prosesnya masing-masing tetapi bekerja sama sebagai satu kesatuan logis. Diagram komunikasi antar node memperlihatkan empat simpul (Node A sampai D) yang terhubung lewat Network, masing-masing dengan lapisan Application / Service, Communication Layer (HTTP/gRPC/Message Queue), dan Local Data / Storage. Pertukarannya ada dua pola: Node A mengirim Request ke Node B yang membalas dengan Response, sedangkan Node C melakukan Publish dan Node D menerimanya lewat Subscribe. Protokol yang lazim dipakai adalah HTTP/HTTPS, gRPC, Message Queue (Kafka, RabbitMQ), dan DNS / Service Discovery, dengan fungsi pertukaran data, koordinasi, keandalan, dan skalabilitas. Contohnya multiplayer games, layanan Gojek/Grab, dan chat atau video call WhatsApp. Pertukaran pesan lewat jaringan menjadi cara utama mencapai resource sharing, dependability, distribution transparency, dan scalability.

## 4. Model Komunikasi Pesan

<p align ="justify">Menguraikan tiga model: socket (langsung), mailboxes dan message queues (tidak langsung), serta Publish/Subscribe. Contoh socket berupa kode Python dengan alamat 127.0.0.1 dan port 65432. Server membuat soket TCP/IPv4, memanggil bind dan listen, lalu accept; di dalam while True ia membaca maksimal 1024 byte dengan recv, berhenti bila datanya kosong, mencetak hasil dekode, dan mengirim balik datanya lewat sendall. Client membuka soket, connect ke (HOST, PORT), mengirim b"Hello, world!", menerima balasan, dan mencetaknya. Pada Pub/Sub (diagram Dashbird), Publisher mengirim msg ke Topic, lalu Topic meneruskannya ke semua Subscriber. Bedanya, socket mengikat dua proses secara langsung, sedangkan message queue dan Pub/Sub membuat pengirim dan penerima tidak perlu saling kenal maupun aktif bersamaan.

## 5. Proses pada Sistem Terdistribusi dan IPC

<p align ="justify">Menjelaskan bahwa proses di node berbeda tidak bisa berbagi memori, sehingga butuh IPC khusus. Ada empat jenis IPC: Shared Memory, Message Passing, Remote Procedure Call, dan Middleware, dan hanya Shared Memory yang tidak cocok untuk sistem terdistribusi. Diagram Shared Memory (artikel Haasita Pinnepu di Medium) menunjukkan satu mesin dengan Kernel yang mengelola area bersama, tempat Process A menulis dan Process B membaca. Antar-node juga tidak ada jam tunggal, jadi semua koordinasi harus lewat pesan eksplisit di jaringan.

## 6. Message Passing

<p align ="justify">Membahas Message Passing lewat dua diagram. Diagram dari University of Hull HPC Wiki menunjukkan empat proses paralel (Processes 0 sampai 3) yang berkomunikasi lewat lapisan MPI seperti OpenMPI di atas High Speed Network, misalnya Intel Omni-Path. Diagram kedua menunjukkan Process P memanggil send(Q, message) lewat communication link, lalu Process Q menerimanya dengan receive(P, message). Standarnya adalah MPI, yang menurut halaman ini sampai Oktober 2026 sudah versi 5.0 (dirilis 5 Juni 2025), memuat komunikasi point-to-point, komunikasi kolektif, dan ABI standar. Di Python, pustaka mpi4py membungkus MPI dengan antarmuka berorientasi objek sehingga objek serialisasi maupun larik seperti NumPy bisa langsung dikirim.

## 7. RPC (Remote Procedure Call)

<p align ="justify">Membahas RPC lewat dua diagram. Pada mekanisme dasar, Klien memanggil prosedur lewat Stub, Stub di sisi Server meneruskannya ke layanan tujuan, dan Hasil dikembalikan ke Klien. Diagram gRPC (grpc.io) memperlihatkan satu C++ Service yang melayani Ruby Client dan Android-Java Client lewat Proto Request dan Proto Response(s). Stub membuat pemanggilan jarak jauh terasa seperti pemanggilan lokal, tetapi di jaringan nyata komunikasi bisa gagal dan eksekusi bisa terulang, yang dikenal sebagai RPC semantics in the presence of failures.

## 8. Kesimpulan 

<p align ="justify">Dari materi satu sampai tujuh, hal pertama yang bisa dipegang adalah bahwa proses bukan sekadar program, melainkan program yang sudah dimuat ke RAM dan sedang dieksekusi CPU dengan ruang memorinya sendiri. Di dalamnya ada thread, dan Scheduler yang membagi waktu CPU lewat Context Switch atau Preemption sehingga banyak proses tampak jalan bersamaan. Siklus hidup proses diurus sistem operasi lewat pembuatan, migrasi, dan pemantauan, dengan PCB sebagai catatan yang harus selalu terjaga agar status proses tidak hilang saat berpindah. Pengamatan di htop membantu melihat semua itu pada mesin sungguhan, misalnya lewat kolom PID, status, CPU%, dan MEM%. Pemahaman dasar ini perlu dikuasai dulu karena seluruh pembahasan sistem terdistribusi berdiri di atasnya.

<p align ="justify">Begitu proses tersebar di banyak node, masalahnya berubah: tidak ada shared memory lintas mesin dan tidak ada jam tunggal, jadi semua koordinasi bergantung pada pertukaran pesan lewat jaringan. Materi menunjukkan beberapa caranya, mulai dari socket yang menghubungkan dua proses secara langsung, message queue dan Pub/Sub yang memisahkan pengirim dari penerima, sampai MPI untuk komputasi paralel dan RPC yang membuat pemanggilan jarak jauh terasa seperti pemanggilan lokal. Tiap pendekatan punya kelebihan sendiri, dan pilihannya tergantung kebutuhan: socket cocok untuk komunikasi langsung yang sederhana, Pub/Sub untuk sistem yang longgar keterikatannya, MPI untuk beban komputasi paralel, dan gRPC untuk layanan lintas bahasa. RPC tetap membawa risiko yang tidak ada pada pemanggilan lokal, yaitu komunikasi bisa gagal dan eksekusi bisa terulang, sehingga transparansi distribusi tidak pernah sepenuhnya gratis. Jadi, merancang sistem terdistribusi berarti memilih mekanisme komunikasi yang sesuai sambil menyiapkan diri menghadapi kegagalan di jaringan.
