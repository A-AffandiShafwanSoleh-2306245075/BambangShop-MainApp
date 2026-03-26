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
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

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

#### Reflection Publisher-3
