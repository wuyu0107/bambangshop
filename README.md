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
    -   [✔] Commit: `Create Subscriber model struct.`
    -   [✔] Commit: `Create Notification model struct.`
    -   [✔] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [✔] Commit: `Implement add function in Subscriber repository.`
    -   [✔] Commit: `Implement list_all function in Subscriber repository.`
    -   [✔] Commit: `Implement delete function in Subscriber repository.`
    -   [✔] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [✔] Commit: `Create Notification service struct skeleton.`
    -   [✔] Commit: `Implement subscribe function in Notification service.`
    -   [✔] Commit: `Implement subscribe function in Notification controller.`
    -   [✔] Commit: `Implement unsubscribe function in Notification service.`
    -   [✔] Commit: `Implement unsubscribe function in Notification controller.`
    -   [✔] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [✔] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [✔] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [✔] Commit: `Implement publish function in Program service and Program controller.`
    -   [✔] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [✔] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

> #### In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

The observer design pattern is a behavioral design pattern that defines a one-to-many dependency between objects. The use of an interface allows for decoupling between the subject and its observers. When the subject changes state, all of its dependents (observers) will be notified and updated automatically, without needing to know the specifics of each observer type. In thsis BambangShop case, a single Model struct is enough since there is only one kind of subscriber in view.

> #### id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

```Vec``` is simple and sufficient if the list of subscriber is small and we don't need fast loopups or deletion. However, using ```Vec``` doesn't guarantee the uniqueness unless it is checked manually, and searching/removing a subscriber by ID or URL is O(n), which runs slower as it grows. 

On the other hand, ```DashMap``` supports fast insertion, removal, and lookup by key and ensures uniqueness through the key. Since BambangShop will handle many products in a shop, using ```DashMap``` will provide faster performace than ```Vec```. It also supports safe multithreaded access, which would be more appropriate for multithreaded applications like BambangShop. 

> #### When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

We know that Rust requires safe shared mutable access in multi-threaded context and DashMap is used because it provides interior mutability along with thread safety for concurrent access. The ```Singleton Pattern``` ensures only one instance of a global object, which is often implemented using ```lazy_static!``` in Rust. However, singleton does not ensure thread-safe access by default. 

Since both ensurance of global state and thread-safe concurrent access is required in Bambangshop, we can conclude that DashMap is better to be used than a Singleton pattern as DashMap handles both.  

#### Reflection Publisher-2

> #### In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

In a traditional MVC, the ```Model``` managed both data and business logic. However, as applications grow more complex, combining these responsibilites make it difficult to maintain code. By separating the ```Service``` and ```Repository``` from the model, we can apply the principle of separation of concerns. The Service layer only contains the business logic, along with validations, processing rules, and workflows, which handles actions across different models. The Repository layer only handles data access, abstracting how data is retrieved or stored. 

> #### What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

If we only use the Model to handle both business logic and data storage, each model will have more and more responsibilities that extend beyond its own data. For example, if the ```Notification``` model need access ```Subscriber``` and ```Program``` data, validate rules, and trigger updates, all within its own scope. This may lead to tight coupling where models over overly dependent on one another making the system fragile. Having one changes in one area can affect other areas. As a result, the complexity of the code increases, testing becomes more difficult, and potential for bugs grows. 

> #### Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

Yes, I have used Postman multiple times within the course. It is used to give help in testing and debugging APIs. It allows for simulation of HTTP requests to the server quickly, view responses, and troubleshoot issues without the need to build the full frontend. Some features I think that may be useful in future projects are automated testing, API documentation generation, and collaborative sharing.

#### Reflection Publisher-3

> #### Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?

In this tutorial, we are using the ```Push model``` of the Observer Pattern. The publisher (model that gets updated), actively notifies its subscribers by pushing relevant data to them. In this tutorial, the NotificationService is the one that notifies and sends updates to all its subscribers without needing any requests. The publisher controls when and what data will be sent.

> #### What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

```Pull model``` of Observer Pattern is where subscribers are only notified of a changed and are responsible for pulling the data from the publisher. Some advantages can be publisher doesn't need to know what exact data the subscribers need, it simply signals something as changed. Subscribers can choose which data to request and when to get the data. This is useful if subscribers have different needs or needs partial updates.

However, the subscriber would need to hold reference to the publisher and know how to query the necessary data, which increases the complexity on the subscriber part. There also may be inconsistency or delay if the publisher's state changes quickly, or multiple subscribers request data at different times. 

> #### Explain what will happen to the program if we decide to not use multi-threading in the notification process.

If we do not use multi-threading in the notification process, the program will handle all subscriber notification sequentially in the order of requested. If there are multiple subscribers, each one will be notified after another, we may cause a delay in the notification service. The publisher also cannot continue processing new updates until notifications are done, reducing the system's responsiveness. Multi-threading is important to maintain a responsive and efficient application.