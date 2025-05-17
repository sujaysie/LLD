# Spring Data JPA: Transactions and Validation Guide

This document focuses on **Spring Data JPA**'s transaction management and data validation mechanisms, crucial for ensuring data consistency and integrity in applications.

## 1. Transaction Management

### Core Concept
A **transaction** is a sequence of operations treated as a single unit of work, ensuring data consistency by either committing all operations or rolling back if any fail. Spring provides robust support for transaction management.

### @Transactional Annotation
The **`@Transactional`** annotation is the primary way to manage transactions in Spring:
- Applied to methods or classes.
- Spring handles transaction boundaries: starts a transaction before the method executes, then commits or rolls back after completion.

#### Example
```java
@Service
public class MyService {

    @Transactional
    public void performTransaction() {
        // Perform database operations here
        // Rolls back on exception, commits on success
    }
}
```

### Transaction Propagation
Defines how transactions behave when a transactional method is called within another transaction. Spring offers several propagation options:
- **REQUIRED** (default): Join an existing transaction, or create a new one if none exists.
- **REQUIRES_NEW**: Always create a new transaction, suspending the current one if it exists.
- **SUPPORTS**: Join an existing transaction, or execute non-transactionally if none exists.
- **NOT_SUPPORTED**: Execute non-transactionally, suspending any existing transaction.
- **MANDATORY**: Requires an existing transaction, throws an exception if none exists.
- **NEVER**: Execute non-transactionally, throws an exception if a transaction exists.
- **NESTED**: Create a nested transaction (savepoint) if a transaction exists, otherwise behaves like `REQUIRED`.

### Isolation Levels
Control visibility of changes between concurrent transactions to prevent issues like dirty reads, non-repeatable reads, and phantom reads. Spring supports:
- **DEFAULT**: Uses the database’s default isolation level.
- **READ_UNCOMMITTED**: Allows dirty reads (lowest level).
- **READ_COMMITTED**: Prevents dirty reads by reading only committed changes.
- **REPEATABLE_READ**: Prevents dirty and non-repeatable reads, ensuring consistent data within a transaction.
- **SERIALIZABLE**: Prevents dirty reads, non-repeatable reads, and phantom reads by serializing transactions (highest level).

#### Example
```java
@Service
public class MyService {

    @Transactional(propagation = Propagation.REQUIRED, isolation = Isolation.READ_COMMITTED)
    public void myTransactionalMethod() {
        // ...
    }
}
```

### Connecting to Single and Multiple Databases

#### Single Database
Spring Boot simplifies single-database setups with auto-configuration:
- Configure database properties (URL, username, password, driver) in `application.properties` or `application.yml`.
- Spring Boot auto-configures a `DataSource`.

#### Multiple Databases
For applications needing multiple databases (e.g., user data and product data):
1. **Define Multiple DataSources**:
   - Create separate `@Configuration` classes or methods for each `DataSource`.
   - Specify connection properties for each.
2. **Configure EntityManagerFactories**:
   - Each `DataSource` needs a corresponding `EntityManagerFactory` to create `EntityManager` instances.
3. **Configure TransactionManagers**:
   - Each `EntityManagerFactory` requires a `TransactionManager` for transaction handling.
4. **Use @Primary**:
   - Designate one `DataSource`, `EntityManagerFactory`, and `TransactionManager` as the default.
5. **Use @Qualifier**:
   - Specify which `DataSource`, `EntityManagerFactory`, or `TransactionManager` to use for specific operations.

#### Example
```java
@Configuration
public class DatabaseConfig {

    @Primary
    @Bean(name = "dataSource1")
    @ConfigurationProperties("spring.datasource.db1")
    public DataSource dataSource1() {
        return DataSourceBuilder.create().build();
    }

    @Bean(name = "dataSource2")
    @ConfigurationProperties("spring.datasource.db2")
    public DataSource dataSource2() {
        return DataSourceBuilder.create().build();
    }

    @Primary
    @Bean(name = "entityManagerFactory1")
    public LocalContainerEntityManagerFactoryBean entityManagerFactory1(
            @Qualifier("dataSource1") DataSource dataSource1) {
        // Configure EntityManagerFactory for dataSource1
        // ...
    }

    @Bean(name = "entityManagerFactory2")
    public LocalContainerEntityManagerFactoryBean entityManagerFactory2(
            @Qualifier("dataSource2") DataSource dataSource2) {
        // Configure EntityManagerFactory for dataSource2
        // ...
    }

    @Primary
    @Bean(name = "transactionManager1")
    public PlatformTransactionManager transactionManager1(
            @Qualifier("entityManagerFactory1") EntityManagerFactory entityManagerFactory1) {
        return new JpaTransactionManager(entityManagerFactory1);
    }

    @Bean(name = "transactionManager2")
    public PlatformTransactionManager transactionManager2(
            @Qualifier("entityManagerFactory2") EntityManagerFactory entityManagerFactory2) {
        return new JpaTransactionManager(entityManagerFactory2);
    }

    // Example of using a specific TransactionManager in a repository:
    @Repository
    @Transactional("transactionManager2") // Use transactionManager2 for this repository
    public interface MyRepository extends JpaRepository<MyEntity, Long> {
        // ...
    }
}
```

## 2. Data Validation

### Core Concept
**Data validation** ensures that persisted data meets application requirements, preventing invalid data from entering the database.

### JPA and Validation
JPA itself lacks built-in validation, but integrates with the **Bean Validation API (JSR 380)** to validate entities.

### Bean Validation API (JSR 380)
A Java standard for defining validation constraints using annotations:
- **@NotNull**: Field cannot be null.
- **@NotEmpty**: Field cannot be null or empty (strings, collections, maps).
- **@Size**: Enforces size limits (strings, collections, maps).
- **@Min, @Max**: Sets minimum/maximum values for numeric fields.
- **@Pattern**: Field must match a regular expression (strings).
- **@Email**: Validates email format (strings).
- **@Valid**: Triggers validation of related objects (e.g., in relationships).

### Spring and Validation
Spring seamlessly integrates with the Bean Validation API:
- Use **`@Valid`** on method parameters (e.g., in `@RestController`) to auto-validate objects, throwing `MethodArgumentNotValidException` on failure.
- Spring integrates validation with JPA via `LocalValidatorFactoryBean`.

#### Example
```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @NotNull
    @Size(min = 2, max = 50)
    private String username;

    @Email
    private String email;

    @Valid // Validate the address object as well
    @NotNull
    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "address_id")
    private Address address;
    // ...
}

@Entity
public class Address {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @NotEmpty
    private String street;

    @NotEmpty
    private String city;
    // ...
}

@Service
public class MyService {

    @Transactional
    public void createUser(@Valid User user) { // Spring validates the user object
        // ...
    }
}

@RestController
public class MyController {

    @PostMapping("/users")
    public ResponseEntity<User> createUser(@Valid @RequestBody User user) { // Validates user from request body
        // ...
    }
}
```

---

*Created on May 18, 2025 at 12:24 AM IST*  
This guide complements earlier Spring Data JPA topics by focusing on transaction management and data validation.
