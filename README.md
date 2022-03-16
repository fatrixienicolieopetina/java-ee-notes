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


- **JPA Entity** 
    - the most unit component is a plain old java object (POJO) 
    - every entity must have a unique identifier
    - @_MappedSuperClass_ annotation can be used to to use superclasses containing common fields of entities
    - _@AttributeOverride_ annotation is used to override entities of the superclass
    - _@Basic_ annotation is used to denote that the field being mapped is a basic type like long, String, etc. This is optional to put since it is default.
    - _@Column_ is used to customize database mappings.
    - _@Transient_ annotation can be used for fields in the entity class that should not be mapped to the database
    - Access Types = process by which the persistence provider accesses states in the entity. Field access type happens when the provider accesses class fields directly through reflection. Property access type happens when the java bean property methods are used to access states, i.e. use of getter and setter methods. To use property access, the getter method must be annotated with _@Id_. Mixed access type uses both field and property access in the same entity class using _@Access_ annotation. The field should be mapped @Transient for the columns using property access to avoid duplicate mapping.
    - Enum types are assigned ordinal values so by default, they are mapped to the db as integers. This can cause a lot of problems. _@Enumerated_ annotation can be used to save enums as strings to prevent problems.
    - _@Lob_ (large object) can be used to map large objects, e.g. images. Use _@Basic_ annotation with fetch type to specify whether the object will be eagerly or lazily fetched.
    - Lazy and Eager Fetching. Basic fields by default are eagerly fetched. Making a field to be lazily fetched is up to the persistence provider so there are times that it will be ignored.
    - JPA 2.2 introduces native support for LocalDate, LocalTime, LocalDateTime, OffsetTime, OffsetDateTime
    - _@Embeddable_ annotation can be used to mean that a class does not have an identity of its own in the databled (not mapped or there is no corresponding database table). When added to an entity class, _@Embedded_ annotation is used. The fields of the embedded class would be part of the fields of the entity using that class. 


- **JPA Entity ID Generation** = using _@Id_ makes a field the primary column of a table
    - AUTO generation strategy = let persistence provide generate the id values.
    - IDENTITY generation strategy = this is database dependent. The persistence provider defers the generation of ID to the database since databases usually supports primary key generation
    - SEQUENCE generation strategy = use annotation _@SequenceGenerator_ name value as a parameter to the generator attribute of the _@GeneratorValue_ annotation of the id field. The sequence used should already exist in the database.  
    - TABLE generation strategy = annotated with _@TableGenerator_. A table is generator to store the IDs.


- **JPA Entity Relationship Mapping**
    - Directionality = refers to how relationship is viewed from one entity to the other (bidirectional or unidirectional)
    - Cardinality = how many entities are there on each end of the relationship (one-to-many, many-to-one, one-to-one, many-to-many). Note that cardinality is named from the source to target entity, e.g. one-to-many (from source to target respectively)
    - Ordinality (Optionality) = should the other part of the relationship exist or not during persistence? Value is usually boolean, 0 or 1. Just ask if an entity can exist without another entity.
    - Single Valued Relationships = only one entity type at the target of the relationship.Owner of the relationship is the table with the foreign key column
    - _@JoinColumn_ is used for foreign key columns
    - _@OnetoOne_ would add a unique constraint to a table
    - Collection Valued Relationships = target of the relationship has many entities, e.g. _@OneToMany_, _@ManyToMany_
    - When using _@OneToMany_ annotation, an _@ManyToOne_ should be created on the owning entity to avoid unwanted joins
    - With _@ManyToMany_ relationship, joins are used by the annotation _@JoinTable_
    - Collection values like @ManyToMany fields are lazily loaded
    - _@ElementCollection_ annotation is used to store collections of non-entity types to the db. This will create a new table with of foreign key of the embedding entity. _@CollectionTable_ annotation is used to customize the collection table created
    - _@OrderedBy_ annotation can be used to order a list of entities

<hr />

### Enterprise Java Beans

   - **Enterprise Java Beans** gives a set of API, mostly annotation-driven that helps to make development of business easier. Goal is to help encapsulate the business level or the logic of the application in a well-defined way, i.e. it EJBs house the business the logic

     - **Features of EJB**  
            1. **Declarative Metadata** = simply declare to use the metadata to declare the intention<br/>
            2. **Configuration by Exception** = convention over configuration. The developer does not need to configure anything id the sensible defaults suit the needs. <br/>
            3. **Dependency Management** = Dependencies are automatically managed directly and transparently managed in business EJB components. EJB platform, in collaborain with the CDI API takes care of all the dependencies of the business components.<br/>
            4. **Lifecycle Management** = EJB lifecycle is managed by the container, and there are clearly defined lifecycle with hooks at strategic points that app developers can easily customize if needed.<br/>
            5. **Scalability** = containers are designed for scalability from the ground up.<br/>
            6. **Transactionality** = Can declaratively manage transaction from business components.<br/>
            7. **Security** = all is needed is to declaratively mark security boundaries to be enforced and the container will take care of it
            8. **Portability** <br/>
        
     - **Architecture of EJB** 
             1. **EJB Component Model** = comprises of the various session beans that is used, i.e. stateless, stateful, singleton and message-driven beans. <br/>
             2. **Stateless Session Bean (Stateless EJB)** = a component that sets out to complete a given task within the lifetime of a single method. Use _@Stateless_ annotation. It does not keep state. Every single method is isolated to do a single task. Analogous to request scoped beans in CDI. The beans are put in a pool, where the container will pick from if there is a request to inject the stateless bean. A transaction is created by default <br/>
             3. **Stateful Session Bean (Stateful EJB)** = uses _@Stateful_ annotation. It is a component that keeps state. Stateful EJBS usually implement serializable because the container might need to serialize them. Since they contain states, the container cannot discard the beans. The bean keeps the state of the collaboration between the client and the server across invocations.<br/>
             4. **Singleton Beans** = one instance available across the application so this can be used to manage shared data. The instance last for the entire lifetime of the whole application. Uses _@Singleton_ annotation. _@Startup_ annotation means that the singleton is eagerly created. By default, only when the first instance is requested is the singleton created.<br/>
       
     - **EJB Component Model (Lifecycle)** = for stateless and singleton _@PostConstruct_ and _@PreDestroy_ are the only lifecycle. Stateful EJB has two additional constructs, _@PrePassivate_ which is called before the bean is put to sleep or hibernated and _@PostActivate_ which is called when the bean is activated from passivation.
     - **Message Driven Beans** = beans used for messaging similar to publish-subscribe kind of system.

     - **EJB Container** = black box, a piece of code that manages EJBs (creation, management and destruction). Analogous to the CDI container.

<hr />

### Transactions
    
   - Transaction is just an abstraction that is used to group together a series of operations which must all be completed successfully or none or should be allowed to stand.
   - Four Properties of Transaction
        - Atomicity = all minute operations should either all be complete successfully or none should be completed at all.
        - Consistency = after atomicity is ensured, data should be consistent with a laid down guidline and expectations. The data that result from the completion of the atomic operation must be consistent.
        - Isolation = means that the change made within the transaction is only visible through the operations until there is a commit on the transaction and then that change can be seen by other atomic units. Dont expect a change happening within the transaction to be visible to any unit outside of the transaction.
        - Durability = means that change is made within a transaction and beyond the completion of the transaction. The change must endure beyond the execution or the completion of the transaction.
        - A transaction that meets these properties are said to be an ACID compliant transaction.
        - Transaction Management (Bean vs Container). Container-managed transaction happens when the application developer delegate transaction management to the application server. Bean-managed transactions happen when the developer wants to manage the transaction on their own.
        - CMT Transaction Management Attributes = There are times when fine-grained control is needed over transactional demarcations and boundaries. _@TransactionAttribute_ annotation is used with parameter TransactionAttributeType.

<hr />

### Persistence Unit and Persistence Context
   - **Persistence Unit** = main configuration of entity classes. It is an xml configuration file that is used to group entity classes into discrete units. jta-data-source in the xml file means the datasource, which is used by the application to connect to the database. It contains the runtime configurations used by the runtime provider to know how to manage the entities in a particular persistence unit.
   - **Persistence Context** = Not visible, but it is a set of managed entities (means that an entity is an a state where the entity manager can carry out operations). The persistence context is derive from the persistence unit. It is the entity manager that interacts with the persistence context.
   - How to access the entity manager = _@PersistenceContext_ annotation can be used.
   - Entity Manager Persist Operation = _entityManager.persist_ is for new entities that are not in the persistence context. The data is persisted to the database when the transaction changes has been commited.
   - Entity Manager Find Operation = find an entity by primary key
   - Entity Manager Remove Operation 
   - Cascade Operations = Cascade means to trickle from one level to the other, from one state to the other. There are times when we want to cascade certain operations from entity to the other. Cascade ALL, REFRESH, REMOVE, MERGE, DETACH, PERSIST are all cascade operation choices. An operation can be cascaded so that when a particular operation happens from the original entity, it is also cascaded to the target entity.
   - Cascade Remove = remove all target entity if the source entity is removed
   - Entity Detachment = A detached entity means an entity outside of the persistence context. This poses problems if there are objects in the detached entity that are lazily loaded.
   - Entity Merging = Takes a detached entity instance and merge it back into the persistence context. 
   - Elements of a persistence unit : name = should be unique across the application since it is a unique way of identification.
   - Elements of a persistence unit : transaction type = used to define the kind of transaction that the entity manager will use. One option is JTA or Java Transaction API which is the value by default which means the container manages the transactions. Another option is RESOURCE_LOCAL which means that the developer handles the transactions.
   - Elements of a persistence unit : persistence provider = used to specify the kind of assistance provider to use, i.e. the concrete implementation of JPA to be used. There are default implementations for each application server, e.g. payara server ships with eclipse link and jboss ships with hibernate.
   - Elements of persistence unit : datasource = it is a configuration the helps the application server connect to a given database. Can be thought of as the pipe between the server and the database. This usually includes the database name, username, password and other details. java:app means that a particular data source is scope to the application, java:amodule means scoped to the module and java:global means available across. Example value of a jta-data-source _java:app/Payroll/MyDS_ . @DataSourceDefinition annotation is used for the details used in connecting to the datasource, like user, password, url. If the annotation is not used, it is added to the web.xml file. If the transaction type is not JTA, <non-jta-data-source> is used in the persistence unit.
    - Elements of a persistence unit : entity classes = the value of the <exclude-unlisted-classes> attribute means if there is a need to scan all classes as possible JPA classes. a _false_ value all classes should be scanned. 
    - Elements of a persistence unit : schema and script generation = means that during deployment time, what should happen to the database (generate.database.action)? Options can be none, create, drop, drop-qnd-create. There is also a property if scripts are generated from the schema on deploy (generare.database.scripts). An additional property target location is added if the value contains "create and dop". 

<hr />

### Java Persistence Query Language (JPQL)
    
  - JPQL is one of two major APIs for querying used in Java EE. It is a string based standard API for querying entities. It is very similar to SQL except that SQL are querying on tables while JPQL are querying on entities. It is very portable since it can port to almost all database vendors. It is string-based query langauge so type-safety is lost.
   - Queries can be broken down into four broad categories, SELECT, AGGREGATE, UPDATE and DELETE. Select and aggregate can be called report queries since select is used to retrieve while aggregates are used to summarize. 
   - JPQL queries can be broken down into named and dynamic queries. Named queries are more or less static, once they are defined, they cannot be changed at runtime. One advantage of named queries is they can be optimized by the provider.
   - Alias is required in JPQL
   - SELECT expressions with combined PATH expressions returns a collection of arrays of type of objects. Constructor expression can be used to create a new Object from the projected data returned from the database.
   - Querying over Maps using joins uses the values by default. Example, _select e.fullname, KEY(p), VALUE(p) from Employee e join e.phoneNumber_ given that phonenumber is a Map object.
   - Fetch Join = eagerly fetches originally lazily loaded attributes of an entity. Example _select e from Employee e join fetch e.employeeAllowances_ where employeeAllowances is a lazily fetched set of allowances.
   - Positional parameters(1?) can be used to add dynamic values to WHERE clauses that acts as filter. Named Parameters is also used to add conditions e.g. (:namedParameter).
   - To use getSingleResult, you must know for a fact that the query will necessarily return one result only. If there is no result, it will throw a no result exception. If there are many results it throws a not unique exception. 
   - The JPA Criteria API is an alternative to JPQL. It is typesafe since it enjoys compile time checks compared to the string-based JPQL. However it is quite verbose.
    

<hr />

### JPA Validations
   - **Bean Validation API** = declarative way of specifying constraints on fields of a given class or properties of a class. For instance, just before an entity is persisted, the runtime will validate the class. If the validation fails a constraint validation exception will be wrapped in an EJB exception and be thrown.
   - There are validations available on Java EE and also in Java SE
   - Examples are _@NotEmpty_, _@Past_, @NotNull,  _@PastOrPresent_, _@DecimalMax_, _@DecimalMin_, _@Future_, _@Pattern_, _@Size_, 
   - **Entity Lifecycle Callbacks** =  @Prepersist gets invoked when the entity is just about to be put into the database. @PostPersist is invoked after a persist happens, but does not mean that the transaction has been committed, thus should not rely on the invocation of this lifecycle that a transaction has completed successfully. @PreUpdate and @PostUpdate will be invoked just before/after an entity is updated. @PostLoad is invoked when the entity has been loaded from the database and populated with data. 
    
<hr />
    
### Entity Listeners
- Entity Listener is an API construct that can be used to isolate lifecycle callbacks from the entity. This can be done if the lifecycle callback method is not necessarily unique to the entity. 
 - To associate the entitylistener to an entity, @EntityListeners annotation is used and entity listener classes is passed as an argument
    
<hr />
 
### Native Queries
- JPA supports native queries where native SQL queries can be issued and get the results back. The results will be casted to Java objects. There is anamed native query and there is also dynamic native SQL. In using named native query, an annotation @NamedNativeQuery can be used. Everything that can be done in SQL can be used in a native query. Dynamic native query can be done by using the EntityManager#createNativeQuery method. Instead of naming the query, the SQL string is passed directly. Dynamic native query returns a query interface and not a type query so the return type is not checked. 
    
 <hr />
    
 ### Equals and hashcode
   
   - **General Contract - Equals Method** = (1) It is reflexive : For any reference value x, x.equals(x) must return true. (2) It is symmetric : For any reference values x and y, x.equals(y) must return true if and if y.equals(x) return true. (3) It is transitive : For any reference values x, y and z, if x.equals(y) returns true and y.equals(z) returns true, then x.equals(z) must return true. (4) It is consistent: For any reference values x and y, multiple invocations of x.equals (y) consistently returns true or consistently returns false, provided no information used in equals comparisons on the object is modified. 
   - **General Contract - Hashcode method** = (1) Whenever it is invoked on the same object more than once during an execution of an application, the hashcode method must consistently return the same integer provided no information used in equals comparison on the object is modified. (2) This integer need not remain consistent from one execution of an application to another execution of the same applicaton. (3) When two objects are equal according to the equals(Object) method, then calling the hashcode method on each of the two objects must produce the same integer result. (4) It is not required that if two objects are unequal according to the equals method, then calling the hashcode method on each of the two Objects must produce distinct integer results. Programmers should be aware that producing distinct integer results for unequal objects may improvde the performance of hashtables.
    
 
<hr />
  
### JAX-RS Java API for Restful Web Services
    
  - REST Architecture Constraints : (1) Client and server is independent of each other . (2) Stateless = Every single request that comes to the server is a self-contained unique request. The server does not make any assumption from the previous request. (3) Cacheable = The system should support caching at different levels to improve performance and scalability (4) Uniform Interface means that the client should have a general uniform interface for accessing resources on the server and also for interacting with the resources on the server. (5) Layered System = The server can be implemented in different layers in a way such that a client need not to worry about a layer system, e.g. a server that supports load balancing. 
  - A resource method is simply a Java method that is hosted at a specific resource path. A resource is essentially anything in the server the client is interested at.
  - HTTP GET method = request for a resource or resources. A GET request is idempotent, meaning that making the same request to a method over and over again should not and must not change the state of resource or data on the server. 
  - HTTP POST method = A POST request is used to create new resources on the server.  It causes a change on the dataset in the server. The POST normally has a body where the payload or whatever we want to create on the server is posted or attached. 
  - HTTP PUT method = This is used semantically to update resources on the server.
  - HTTP DELETE method = It is used to delete resources on the server.
  - Content Types = (1) XML (2) CSV (3) EXCEL (4) TEXT (5) JSON Javascript Object Notation 
  - JAX-RS has a concept of message body writer. They are API constructs used to convert Java types to relevant type the client expects. @Produces annotation can be used to specify the type to which the Java object response is converted to, e.g. @Produces("application/json"). Class-level @Produces can be overriden to specific methods if necessary.
  - The @Consumes annotation tells the JAX-RS runtime the type of content a given resource method consumes. The JAX-RS runtime will then convert the JSON content passed as a payload to a Java object of type similar to the method parameter. 
   - Path parameters or uri template = use @PathParam annotation. Restricting the types of path parameters can be done by using regular expressions, e.g @Path('employee/{id}: ^[0-9]+$'). @DefaultValue annotation can be used in the method parameter to set the default value of a certain path param.
   - The @Context annotation is sued to inject a certain runtime interfaces and classes that have contextual information about the resources. UriInfo interface provides access to the application and request URI information. The HTTPServletRequest interface provides the client request information for a servlet. 
   - JAX-RS Exception Mapper = API construct used to map exceptions to HTTP responses. _@Provider_ is used to register an exception mapper to the JAX-RS runtime programatically. @Valid can be used in the method parameter to invoke constraint checking before prepersisting. Sometimes it is good to use this annotation to not cascade the constraint checking in the service layer. @FormParam, a multivalued map and @BeanParam can be used to consume form field. @BeanParam is used to inject custom JAX-RS "parameter aggregator" value object into a resource class field, property or resource method parameter. 
   - JAX-RS Caching with CacheControl and Etags = The _privateFlag_ attribute in the CacheControl class is used to control whether or not the intermediaries like CDN and proxies will cache the resource. _noCache_ disables caching, noStore means the client should not store any data whether in hard disk or memory, etc.
   - Content Negotiation = Used to tell the server to prioritize more specific content types by levels when a resource is requested. When there are a lot of options in the @Produces annotation, the server will check the Accept headers during runtime and will choose the content type with highest priority. Priority can be assigned by using the httpheaders or adding `qs` values to the media types.
