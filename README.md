# java-ee-notes

### Introduction

- **Java EE** = collection of abstract specifications that together form a complete solution for solving commonly faced challenges in software development.

- **Application Server** = concrete implementation of Java EE abstract specifications.
Application Server Examples: Payara Server (Glassfish), IBM OpenLiberty, JBoss Wildfly

- **JSR (Java Specification Request)** = formal request to the Java community process for addition and enhancements to technologies. It is a body that standardizes APIs on the Java technology platform and is used to group APIS into silos, e.g. JAX-RS (Java API for RESTful Web Services).
= For every JSR, there is a **reference implementation**.

- **Reference Implementation** = Concrete realization of an abstract JSR. For instance, the reference implementation for JAX-RS is called Jersey. 
= Java EE itself is a JSR. Thus, an application server is a collection of the various reference implementations for the Java EE JSR. For example, Java EE is JSR 366 and one of its reference implementations is Glassfish 5.

- **Jakarta EE** = Java EE going forward. Oracle moved the Java platform to be hosted by the Eclipse Foundation (https://jakarta.ee). Thus the new cloud native Java EE is hosted at Eclipse Foundation.

- **Java EE and Spring Framework** = both influenced each other 

<hr />

### Java EE Basics
- **Entity Lifecycle Callback** = A lifecycle callback is a method that will be invoked at a specific lifecycle point of an entity. An example of a lifecycle in JPA API are @PrePersist, it is a point where a method is executed just before it is persisted into the database. 
Note: Java EE is annotation driven.

- **Bean Validation API (for db entities)** = e.g. @NotEmpty, @Size, @FutureOrPresent, @NotNull

- **Three Key APIs in Java EE** 
1. Java Persistence API (JPA)
2. CDI (Context and Dependency Injection)
3. JAX-RS (Java API for RESTful Web Services)

-  **JPA** = responsible for storing and retrieving info from relational databases, which can be extended to handle NoSQL databases. It is the data layer of an application.

- **JAX-RS** = Java API for RESTful Web Services. Exposes resources over HTTP protocol as web services.

- **CDI** = Context and Dependency Injection API. Standardized way to create highly decoupled applications. It managed various interactions of different components to allow loose decoupling.
