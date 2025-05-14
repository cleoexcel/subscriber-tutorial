a. what is amqp?

AMQP merupakan singkatan dari Advanced Message Queuing Protocol, yaitu sebuah protokol standar terbuka yang digunakan di tingkat aplikasi untuk memungkinkan komunikasi antar aplikasi secara andal melalui sistem perantara yang disebut messaging middleware atau message broker. Dalam konteks ini, aplikasi pengirim akan mengirimkan data ke message broker, dan aplikasi penerima akan mengambil data dari broker tersebut, sehingga keduanya tidak perlu terhubung secara langsung. Salah satu contoh message broker yang populer adalah RabbitMQ. AMQP mendukung berbagai fitur penting seperti pengiriman data berbasis pesan (message-oriented), sistem antrian (queuing), pengaturan jalur pesan (routing) baik secara langsung maupun menggunakan sistem publish-subscribe, serta memastikan pengiriman data dilakukan secara aman dan handal. Dengan kata lain, AMQP membantu sistem aplikasi menjadi lebih fleksibel, skalabel, dan terorganisir dalam mengelola pertukaran data.

b. what it means? guest:guest@localhost:5672 , what is the first quest, and what is
Alamat amqp://guest:guest@localhost:5672 adalah sebuah URI (Uniform Resource Identifier) yang digunakan oleh aplikasi untuk terhubung ke message broker AMQP, seperti RabbitMQ. Bagian amqp:// menunjukkan bahwa protokol yang digunakan adalah AMQP. Lalu guest:guest adalah informasi autentikasi—guest pertama adalah username, dan guest kedua adalah password. Biasanya nilai ini digunakan sebagai kredensial default saat RabbitMQ pertama kali dijalankan. Selanjutnya, localhost menunjukkan bahwa message broker tersebut berjalan pada komputer lokal, yaitu perangkat yang sama dengan aplikasi klien. Terakhir, 5672 adalah port default yang digunakan oleh AMQP untuk menerima koneksi. Jadi, URI tersebut secara lengkap berarti aplikasi akan mengakses message broker RabbitMQ yang berjalan di komputer lokal pada port 5672, dengan menggunakan username dan password default guest.
- `guest` (pertama) adalah nama pengguna untuk autentikasi.
- `guest` (kedua, setelah tanda titik dua `:`) adalah kata sandi untuk autentikasi.
- `localhost:5672` merujuk pada alamat dan port di mana message broker AMQP (seperti RabbitMQ) mendengarkan koneksi.
- `localhost` berarti message broker berjalan pada mesin yang sama dengan klien yang mengakses.
- `5672` adalah nomor port default yang digunakan oleh RabbitMQ untuk menerima koneksi AMQP.

# Simulation slow subscriber
![Alt text](images/image6.jpg)
Jumlah total pesan yang mengantri dapat dipengaruhi oleh beberapa faktor, salah satunya adalah ketika publisher mengirim pesan dengan cepat ke RabbitMQ, sehingga setiap proses pengiriman menambah panjang antrian. Sementara itu, subscriber mengonsumsi pesan dengan kecepatan yang lebih lambat karena adanya penundaan sengaja selama 1 detik per pemrosesan pesan. Penundaan ini dalam pemrosesan memungkinkan pesan untuk menumpuk dalam antrian. Jumlah queue tergantung pada berapa banyak jenis antrian yang dibuat dalam aplikasi dan bagaimana aplikasi kamu dideploy atau diuji. Pada mesin saya, hanya ada 2 karena hanya ada satu jenis pesan/event yang diproses dan satu subscriber aktif.

# Reflection and Running at least three subscribers
![Alt text](images/image7.jpg)
![Alt text](images/image8.jpg)
![Alt text](images/image9.jpg)
![Alt text](images/image10.jpg)
![Alt text](images/image11.jpg)
Ketika beberapa subscriber memproses pesan secara bersamaan, peningkatan dalam panjang antrian pesan akan berkurang lebih cepat karena adanya pemrosesan paralel dan peningkatan throughput. Dengan setiap subscriber menangani pesan secara independen, beban kerja tersebar lebih merata di seluruh sistem, mengurangi kemungkinan antrean menjadi panjang dan memungkinkan lebih banyak pesan diproses secara bersamaan.
Pada image tersebut dapat dilihat kalau pada tiap subscriber mendapatkan data yang berbeda-beda saat publisher mengirimkan banyak data ke message queue. Ini karena tiap subscriber berfungsi seperti aplikasi nya masing-masing sehingga terpisah saat mengambil data dari message queue. Ketika data sudah terambil dari message queue, maka message akan hilang dan aplikasi lain tidak bisa menggunakannya. Selain itu, menurut saya, cara untuk meningkatkan performa dari aplikasi subscriber adalah dengan membuatnya menjadi multithreading sehingga bisa mengolah banyak event dari publisher sekaligus.