# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [x] Commit: `Implement publish function in Program service and Program controller.`
    -   [x] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [x] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
# Reflection Publisher-1

## 1. Apakah masih perlu interface (trait) untuk Subscriber?

Pada konsep Observer pattern secara umum (seperti di Head First Design Patterns), Subscriber biasanya didefinisikan sebagai interface agar mendukung banyak jenis subscriber yang berbeda.

Namun, pada kasus BambangShop yang saya implementasikan di tutorial ini, penggunaan trait (interface di Rust) tidak diperlukan. Hal ini karena:

- Semua subscriber memiliki struktur yang sama (hanya url dan name).
- Tidak ada variasi perilaku antar subscriber.
- Proses notifikasi dilakukan dengan cara yang sama (HTTP POST ke URL).

Oleh karena itu, penggunaan satu struct `Subscriber` saja sudah cukup untuk memenuhi kebutuhan sistem saat ini. Penggunaan trait baru akan diperlukan jika di masa depan terdapat berbagai jenis subscriber dengan perilaku berbeda.

---

## 2. Apakah Vec cukup atau perlu DashMap?

Pada kasus ini, `url` pada Subscriber bersifat unik. Jika kita menggunakan `Vec`, maka:

- Kita harus melakukan pencarian manual (linear search) untuk mengecek duplikasi.
- Operasi insert, delete, dan lookup menjadi kurang efisien (O(n)).

Sedangkan dengan `DashMap`:

- Data disimpan dalam bentuk key-value (url sebagai key).
- Tidak memungkinkan duplikasi key.
- Operasi lebih efisien (mendekati O(1)).
- Lebih aman untuk akses bersamaan (thread-safe).

Karena itu, penggunaan `DashMap` lebih tepat dibandingkan `Vec` untuk kasus ini, terutama karena kita membutuhkan efisiensi dan keunikan data.

---

## 3. Apakah DashMap masih diperlukan atau cukup Singleton?

Dalam tutorial ini, kita menggunakan `lazy_static` untuk membuat `SUBSCRIBERS` sebagai singleton (hanya satu instance global).

Namun, Singleton hanya memastikan bahwa data hanya ada satu instance, **tidak menjamin thread safety**.

Karena aplikasi ini menggunakan multi-threading (misalnya saat mengirim notifikasi ke banyak subscriber), maka:

- Akses ke data bisa terjadi secara bersamaan.
- Dibutuhkan struktur data yang aman terhadap concurrency.

`DashMap` menyediakan thread-safe HashMap tanpa perlu manual locking seperti `Mutex`.

Jadi:
- Singleton → memastikan satu instance
- DashMap → memastikan thread-safe access

Kesimpulannya, kita tetap membutuhkan `DashMap` meskipun sudah menggunakan konsep Singleton, karena keduanya memiliki peran yang berbeda.
#### Reflection Publisher-2
## 1. Mengapa perlu memisahkan Service dan Repository dari Model?

Pada konsep MVC klasik, Model biasanya mencakup data sekaligus business logic. Namun, pada implementasi di tutorial ini, Model hanya digunakan sebagai representasi data, sedangkan logic dipisahkan ke dalam Service dan Repository.

Dalam tutorial yang saya kerjakan:
- Model (`Subscriber`, `Notification`, `Product`) hanya berisi struktur data.
- Repository (`SubscriberRepository`) bertugas untuk mengelola penyimpanan data menggunakan `DashMap`.
- Service (`NotificationService`) bertugas mengatur alur business logic seperti subscribe dan unsubscribe.

Pemisahan ini dilakukan karena mengikuti prinsip desain:
- **Separation of Concerns** → setiap layer memiliki tanggung jawab yang jelas.
- **Single Responsibility Principle (SRP)** → setiap komponen hanya memiliki satu tujuan utama.

Sebagai contoh dari implementasi saya:
- Saat melakukan subscribe, controller memanggil `NotificationService::subscribe`.
- Kemudian service akan memanggil `SubscriberRepository::add` untuk menyimpan data.
- Model hanya digunakan sebagai data yang dikirim dan diterima.

Keuntungan dari pemisahan ini:
- Kode lebih terstruktur dan mudah dibaca.
- Mudah dikembangkan jika nanti ingin menambah fitur baru (misalnya validasi tambahan).
- Mudah mengganti implementasi penyimpanan (misalnya dari `DashMap` ke database).
- Mempermudah debugging karena setiap layer memiliki peran yang jelas.

---

## 2. Apa yang terjadi jika hanya menggunakan Model?

Jika hanya menggunakan Model tanpa memisahkan Service dan Repository, maka seluruh logic akan berada di dalam Model. Hal ini akan menyebabkan beberapa masalah:

### a. Kompleksitas meningkat
Model akan menangani:
- Struktur data
- Penyimpanan data
- Business logic
- Bahkan kemungkinan komunikasi antar komponen

Sebagai contoh:
- `Subscriber` harus tahu cara menyimpan dirinya sendiri ke dalam struktur data global.
- `Product` harus langsung mengatur subscriber dan notifikasi.
- `Notification` bisa ikut terlibat dalam logic pengiriman.

Akibatnya, setiap Model menjadi sangat kompleks.

### b. Coupling tinggi
Setiap Model akan saling bergantung satu sama lain:
- Product bergantung ke Subscriber
- Subscriber bergantung ke Notification
- Notification bergantung ke Product

Hal ini membuat perubahan kecil bisa berdampak ke banyak bagian.

### c. Sulit di-maintain
- Kode menjadi panjang dan sulit dibaca.
- Sulit untuk menambahkan fitur baru tanpa merusak logic lama.
- Testing menjadi lebih sulit karena semua logic bercampur.

### d. Tidak sesuai dengan implementasi tutorial
Dalam tutorial ini, kita sudah menggunakan:
- Repository untuk akses data (`DashMap`)
- Service untuk business logic

Jika semua dipindahkan ke Model, maka struktur yang sudah dibuat menjadi tidak konsisten dan tidak mengikuti design pattern yang diharapkan.

---

## 3. Pengalaman menggunakan Postman

Dalam tutorial ini, saya menggunakan Postman untuk menguji endpoint yang telah dibuat

Postman sangat membantu karena:
- Saya tidak perlu membuat frontend untuk mencoba API.
- Bisa langsung mengirim request dan melihat response dari server.
- Mempermudah pengecekan apakah logic di Service dan Repository sudah berjalan dengan benar.

### Contoh penggunaan di tutorial:
Saat saya melakukan subscribe:
- Saya mengirim request ke endpoint `/notification/subscribe/APPLIANCES`
- Dengan body JSON:
  ```json
  {
    "url": "http://localhost:8001/receive",
    "name": "Rick Asli"
  }
#### Reflection Publisher-3

## 1. Variasi Observer Pattern yang digunakan

Pada tutorial BambangShop yang saya kerjakan, variasi Observer Pattern yang digunakan adalah **Push Model**, di mana publisher secara langsung mengirimkan data ke subscriber.

Hal ini terlihat dari implementasi pada `NotificationService::notify` dan `Subscriber::update`. Setiap kali terjadi event seperti create product, publish product, atau delete product, sistem akan langsung membuat payload `Notification` dan mengirimkannya ke seluruh subscriber menggunakan HTTP POST request.

Dalam implementasi yang saya lakukan:
- Service mengambil daftar subscriber dari `SubscriberRepository`
- Kemudian melakukan iterasi terhadap semua subscriber
- Untuk setiap subscriber, dipanggil method `update`
- Method tersebut akan mengirim request ke URL subscriber (contohnya `http://localhost:8001/receive`)

Dengan demikian, subscriber tidak mengambil data sendiri, melainkan menerima data yang dikirim langsung oleh publisher. Oleh karena itu, pendekatan yang digunakan jelas merupakan Push Model.

---

## 2. Jika menggunakan Pull Model

Jika pada tutorial ini digunakan Pull Model, maka alurnya akan berbeda karena subscriber harus mengambil data sendiri yang berasal dari publisher.

Dalam Pull Model:
- Publisher hanya menyediakan data (misal endpoint untuk mengambil notifikasi)
- Subscriber harus melakukan request secara berkala (polling) untuk mengetahui apakah ada update terbaru

Pendekatan ini memiliki beberapa kelebihan:
- Subscriber lebih fleksibel karena bisa menentukan kapan ingin mengambil data
- Publisher tidak perlu mengirim request ke banyak subscriber
- Tidak ada masalah jika subscriber sedang offline

Namun, untuk kasus pada tutorial ini, Pull Model memiliki beberapa kekurangan:
- Tidak real-time karena subscriber harus melakukan polling
- Bisa terjadi keterlambatan dalam menerima notifikasi
- Menambah kompleksitas di sisi subscriber
- Jika polling terlalu sering, justru dapat membebani server

Dibandingkan dengan itu, Push Model yang digunakan dalam tutorial ini lebih sesuai karena notifikasi langsung dikirim saat event terjadi, sehingga lebih sederhana dan lebih mendukung kebutuhan real-time.

---

## 3. Dampak jika tidak menggunakan multi-threading

Dalam tutorial ini, pengiriman notifikasi dilakukan menggunakan multi-threading dengan `thread::spawn`. Hal ini memungkinkan setiap subscriber diproses secara paralel.

Jika multi-threading tidak digunakan, maka proses pengiriman notifikasi akan berjalan secara berurutan (synchronous). Dampaknya adalah:

- Setiap request HTTP harus menunggu request sebelumnya selesai
- Jika jumlah subscriber banyak, waktu pengiriman notifikasi akan menjadi lama
- Response dari API (misalnya saat publish product) akan menjadi lambat
- Jika ada subscriber yang lambat atau tidak merespon, seluruh proses akan ikut terhambat

Sebagai ilustrasi:
- Tanpa thread → subscriber diproses satu per satu
- Dengan thread → semua subscriber diproses secara bersamaan

Dalam implementasi yang saya lakukan, penggunaan `thread::spawn` membuat proses notifikasi menjadi:
- Lebih cepat karena berjalan paralel
- Tidak blocking terhadap request utama
- Lebih scalable jika jumlah subscriber bertambah

---

Secara keseluruhan, penggunaan Push Model dan multi-threading pada tutorial ini membuat sistem notifikasi menjadi lebih efisien, real-time, dan sesuai dengan kebutuhan aplikasi yang mengharapkan pengiriman notifikasi secara langsung ke subscriber.
