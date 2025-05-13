# CompletableFuture in Java

`CompletableFuture` is a Java class introduced in Java 8 under the `java.util.concurrent` package. It provides a way to write **asynchronous**, **non-blocking**, and **composable** code using futures.

---

## 🔹 Key Concepts

### 1. Asynchronous Execution
Run a task in a background thread without blocking the main thread.

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    return "Hello";
});

future.thenAccept(System.out::println); // Prints: Hello
```

---

### 2. Chaining with `thenApply`, `thenAccept`, and `thenRun`

| Method         | Description                                 |
|----------------|---------------------------------------------|
| `thenApply`    | Transform result                            |
| `thenAccept`   | Consume result (no return)                  |
| `thenRun`      | Run a task after completion (no input/output) |

```java
CompletableFuture<String> greeting = CompletableFuture.supplyAsync(() -> "Hello")
    .thenApply(str -> str + " World")
    .thenApply(String::toUpperCase);

System.out.println(greeting.get()); // Output: HELLO WORLD
```

---

### 3. Combining Futures
Run tasks in parallel and combine results.

```java
CompletableFuture<Integer> f1 = CompletableFuture.supplyAsync(() -> 10);
CompletableFuture<Integer> f2 = CompletableFuture.supplyAsync(() -> 20);

CompletableFuture<Integer> combined = f1.thenCombine(f2, Integer::sum);
System.out.println(combined.get()); // Output: 30
```

---

### 4. Exception Handling
Handle exceptions gracefully in async pipelines.

```java
CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {
    return 1 / 0; // Throws exception
}).exceptionally(ex -> {
    System.out.println("Handled: " + ex);
    return -1;
});

System.out.println(future.get()); // Output: -1
```

Use `.handle()` to work with both result and exception:

```java
CompletableFuture<String> handled = CompletableFuture.supplyAsync(() -> "data")
    .handle((res, ex) -> ex == null ? res : "default");
```

---

### 5. Waiting for Multiple Tasks

- `CompletableFuture.allOf(...)` waits for all futures.
- `CompletableFuture.anyOf(...)` returns the result of the first completed future.

```java
CompletableFuture<Void> all = CompletableFuture.allOf(f1, f2);
all.join(); // Waits for all to complete
```

---

## 🔹 Real World Example: Combine DB and API Call

```java
CompletableFuture<User> userFuture = CompletableFuture.supplyAsync(() -> fetchUserFromDB());
CompletableFuture<Profile> profileFuture = CompletableFuture.supplyAsync(() -> fetchProfileFromAPI());

CompletableFuture<UserProfile> result = userFuture.thenCombine(profileFuture, (user, profile) -> {
    return new UserProfile(user, profile);
});

result.thenAccept(System.out::println);
```

---

## 🔹 Best Practices

- Use `supplyAsync()` for returning values, `runAsync()` for `void`.
- Always handle exceptions.
- Use `join()` when you’re sure it won’t throw checked exceptions.
- Avoid blocking `.get()` in production async logic.

---

## 🔹 Summary Table

| Concept              | Description                          |
|----------------------|--------------------------------------|
| Async execution      | `supplyAsync`, `runAsync`            |
| Chaining             | `thenApply`, `thenAccept`, `thenRun` |
| Composition          | `thenCombine`, `allOf`, `anyOf`      |
| Error Handling       | `exceptionally`, `handle`            |

---

## 🔚 Want More?
Want to see `CompletableFuture` in a Spring Boot REST API or with custom `ExecutorService`? Let me know!

