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

<hr />

### Context and Dependency Injection API
- **Dependency Injection** = specific form of inversion control (software strategy where individual components have their dependencies supplied to them). This externalizes dependency in the application to create loosely coupled components.

- **CDI Features**
    - Dependency Injection (Typesafe Dependency Injection) = declare dependency on types, compiler catches error on runtime
    - Lifecycle Contexts
    - Interceptors = intercept requests to method
    -  Events = way to develop highly decoupled applications. Events can be fired while observers listen for the fired events.
    -  Service Provider Interface (SPI) = set of hooks and API and interfaces that can be used as extensions, e.g. Apache libraries.

- **CDI Bean Discovery** = mechanism where DI runtime analyzes and discovers beans for it to manage default bean discovery mode, i.e beans that are annotated, e.g. _@Annotated_. There are three types of bean discovery mode, ALL (includes those beans that are not annotated), ANNOTATED and NONE. This can be set in the _beans.xml_. 

- **CDI Container** = factory in which Java classes comes in and goes out with functionalities and features . It is an application that manages beans.

- **Beans and Contextual Instance** 
     - Bean = template which a developer creates
     - Contextual Instance = instance of a bean created by the CDI container and is managed by it

- **CDI Injection Points** = where to inject dependencies. 
    - Field = injections that takes place on the field of a bean
    - Constructor = request the CDI container for contextual instances as parameters to constructors.
    - Method = request a contextual instance as a parameter to a method.

- **CDI Lifecycle Callback** = point or hook of a bean.
    - _@PostConstruct_ = method should be void. It is a point at which a bean, together with all injected dependencies are initiated and is ready to use and put into service.
    - _@PreDestroy_ = invoked before the bean is destroyed and is released for garbage collection.

- **Managed Bean** = any bean eligible for CDI management.

- **CDI Qualifiers** = a way to avoid ambiguity when injecting contextual instances and to distinguish bean types.

- **CDI Stereotypes** = collection of annotations grouped together as one. _@Named_ is one of the default qualifiers of CDI API. It allows access of a particular bean to be accessed from a web page.

- **CDI Scopes and Contexts** = Scope associates a specific contextual instance to a context (lifetime of a bean). Context refers to a valid environment with a well-defined lifecycle and time.
    -  Dependent Scope = default without annotation, meaning it is dependent on the scope of the contextual instance it is injected. It follows the context of the bean it is injected.
    -  Request Scope (_@RequestScoped_) = associated with an HTTP request. Every request will create a new contextual instance.
    -  Session Scoped (_@SessionScoped_) = associated/bound to an HTTP session. Note that beans are lazily created. Thus it can be hibernated or be passive if not used since sessions are usually long lasting.
    -  Application Scope (_@ApplicationScoped_) = singleton, lifetime of the application. It creates a single contextual instance that lasts with the application.
    -  Conversation Scope (_@ConversationScoped_) = similar to session scoped except that it is managed by the developer. The CDI creates the bean but the developer decides when to end it (??)

- **CDI Producers** = way to turn objects or classes that you don't own into CDI injectable bean/contextual instance. Producer methods are very useful when the concrete type to be injected varies at runtime. Field producers on one hand make a field a CDI managed bean. Qualifying beans can also be used to prevent ambiguity in using producers. Disposers (annotated with _@Disposes_) do custom cleanup on a producer class. It is called before the bean is destroyed.

- **CDI Interceptors** = used to intercept a method call to do some preprocessing.

- **CDI Event Mechanism** = link disparate parts of an application. Payload is the informatin passed to the observers of an event. This mechanism helps write a reactive application.

- **The Event Interface API** = Observers can be prioritized during method invocations by using the _@Priority_ qualifier
    - Simple Events = notify all observers of the event
    - Qualifying Events = use qualifiers to only invoke certain observers
    - Conditional Observers - use notifyObserver and during as parameters to the _@Observes_ annotation.
    - Async Events 


<hr />

### Java Persistence API 
= used to map Java objects to relational database tables. The Java Persistence API meets the ORM Manifesto tenets below.


- **ORM Manifesto** (Object Relational Mapping Manifesto)
    - **_Objects not Tables_** = devs write objects not tables. Thus think in terms of objects, not database tables.
    - **_Convenience, Not Ignorance_** = an ORM should be convenient. Developers should at least have minimum knowledge on relational databases. ORM is not a way to mask ignorance but for convenience.
    - **_Unobtrusive not transparent_**. an ORM should make it so that developers are able to control what is in the database, and have complete control on what is being persisted.
    - **_Legacy Data, New Objects_** = Expect ORM to allow creation of new objects from legacy data, i.e reverse engineer legacy database to Java objects.
    - **_Just Enough, not too much_** = expect ORM to give us all the tools to solve generally encountered problems due to impedance mismatch (term used to refer to problems that occur due to difference between the database model and programming language). An ORM should not be excessively heavy weight.
    - **_Local but mobile_** = The data is local but there should be an ability of a the persistent state of the application to travel to different parts of the application.
    - **_Standard API, pluggable implementation_** = rely on a standard API but can be swapped implementations if needed.

