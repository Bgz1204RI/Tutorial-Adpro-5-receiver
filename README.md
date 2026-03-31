# BambangShop Receiver App
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
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.


## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create SubscriberRequest model struct.`
    -   [x] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [x] Commit: `Implement add function in Notification repository.`
    -   [x] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Commit: `Implement receive_notification function in Notification service.`
    -   [x] Commit: `Implement receive function in Notification controller.`
    -   [x] Commit: `Implement list_messages function in Notification service.`
    -   [x] Commit: `Implement list function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

### 1. Why do we use RwLock and not Mutex?

In this tutorial, `RwLock` is used to synchronize access to the shared `Vec<Notification>`. The main reason is that `RwLock` allows multiple readers to access the data at the same time while still ensuring that only one writer can modify it. This is useful because in many cases the program may only need to read the notifications rather than modify them.

If we used `Mutex` instead, every read operation would also lock the entire data structure, meaning only one thread could access it at a time. This would reduce concurrency and make the system less efficient. With `RwLock`, multiple threads can read the notifications simultaneously, which improves performance when reads are more frequent than writes.

### 2. Why does Rust not allow mutating static variables like Java?

Rust does not allow mutable access to `static` variables in the same way Java does because Rust focuses heavily on memory safety and preventing data races. If multiple threads were able to modify a static variable directly, it could easily lead to unpredictable behavior or crashes.

Instead, Rust requires safe synchronization mechanisms such as `RwLock`, `Mutex`, or other concurrency primitives. The `lazy_static` crate is used to initialize complex static variables safely at runtime. This design forces developers to handle shared mutable state explicitly, which reduces the risk of bugs related to concurrency and improves overall program safety.

#### Reflection Subscriber-2

### 1. Exploring things outside the tutorial steps

Yes, I did look a little beyond the tutorial steps, especially at files like `src/lib.rs` and the shared configuration parts of the project. In my opinion, doing that helped me understand the project better because the tutorial itself mostly focuses on the main flow, while files outside the steps explain how the application is configured and how different parts are connected. For example, by looking at the shared config and helper code, I could better understand where values like the app instance name, root URL, and HTTP client were coming from.

I think this was useful because it made the project feel less like just copying code and more like understanding how the application actually works. Even if I did not study every single file in detail, checking those extra files gave me more context about how the receiver app communicates with the publisher app and how the project is organized overall.

### 2. How Observer pattern helps plug in more subscribers, and what if there is more than one Main app

After trying the notification system with multiple Receiver instances, I think the Observer pattern makes it much easier to plug in more subscribers. Each receiver can subscribe independently to a product type, and the publisher does not need to know the internal details of each receiver. It only needs to store subscriber information and send notifications when an event happens. Because of that, adding more receivers is relatively easy and does not require changing the main notification logic.

However, if we spawn more than one instance of the Main app, it becomes less simple compared to adding more Receiver instances. The reason is that each Main app instance may have its own in-memory data, including subscribers and products, unless there is a shared database or shared storage. So while Observer pattern makes it easy to scale subscribers, running multiple publisher instances consistently would require additional coordination. In other words, adding more subscribers is easy, but scaling the publisher side would need a more careful design.

### 3. Trying Postman tests or better documentation

I have used Postman mainly to test the endpoints in this tutorial, and I think it is already very helpful even without advanced features. It makes it easier to send requests, check responses, and confirm whether each endpoint works as expected. For this project, that is very useful because I can test subscribe, unsubscribe, receive, and list behavior without needing to build any frontend.

I have not deeply explored making my own automated Postman tests yet, but I can see why they would be useful. Tests and better collection documentation would make it easier to repeat checks, especially in group projects where several people need to use the same API. I think this would be useful not only for tutorial work, but also for future software engineering projects, because it helps keep backend testing more organized and easier to share with teammates.