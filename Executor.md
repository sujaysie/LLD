In Java, the `java.util.concurrent` package provides the `Executor` framework, which includes a set of classes and interfaces designed for managing and controlling the execution of asynchronous tasks. The core interface in this framework is the `Executor` interface, which represents an object that executes submitted `Runnable` tasks.

### Core Components of the Executor Framework

1. **Executor Interface**: 
   - The simplest interface in the framework with a single method `void execute(Runnable command)`.
   - Example:
     ```java
     Executor executor = new Executor() {
         @Override
         public void execute(Runnable command) {
             new Thread(command).start();
         }
     };
     executor.execute(() -> System.out.println("Task executed"));
     ```

2. **ExecutorService Interface**:
   - Extends the `Executor` interface and adds more functionality like lifecycle management and task submission.
   - Commonly used methods include `submit()`, `shutdown()`, `shutdownNow()`, `invokeAll()`, and `invokeAny()`.
   - Example:
     ```java
     ExecutorService executorService = Executors.newFixedThreadPool(3);

     executorService.submit(() -> System.out.println("Task 1 executed"));
     executorService.submit(() -> System.out.println("Task 2 executed"));

     executorService.shutdown();
     ```

3. **ScheduledExecutorService Interface**:
   - Extends `ExecutorService` and supports task scheduling.
   - Methods include `schedule()`, `scheduleAtFixedRate()`, and `scheduleWithFixedDelay()`.
   - Example:
     ```java
     ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);

     scheduler.schedule(() -> System.out.println("Scheduled task executed"), 5, TimeUnit.SECONDS);

     scheduler.scheduleAtFixedRate(() -> System.out.println("Repeated task executed"), 1, 3, TimeUnit.SECONDS);

     // This will stop the scheduler after 10 seconds to end the example.
     scheduler.schedule(() -> scheduler.shutdown(), 10, TimeUnit.SECONDS);
     ```

4. **Executors Utility Class**:
   - Provides factory and utility methods for creating executor services.
   - Common methods include `newFixedThreadPool()`, `newCachedThreadPool()`, `newSingleThreadExecutor()`, `newScheduledThreadPool()`, etc.
   - Example:
     ```java
     ExecutorService fixedThreadPool = Executors.newFixedThreadPool(3);
     ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
     ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();
     ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(2);
     ```

### Example: Using Executors
Here is a more comprehensive example demonstrating various executor services:

```java
import java.util.concurrent.*;

public class ExecutorExample {

    public static void main(String[] args) throws InterruptedException, ExecutionException {
        // Using a Fixed Thread Pool ExecutorService
        ExecutorService fixedThreadPool = Executors.newFixedThreadPool(2);

        Runnable task1 = () -> {
            try {
                TimeUnit.SECONDS.sleep(2);
                System.out.println("Task 1 completed");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        };

        Runnable task2 = () -> System.out.println("Task 2 completed");

        fixedThreadPool.execute(task1);
        fixedThreadPool.execute(task2);

        // Using a Scheduled ExecutorService
        ScheduledExecutorService scheduledExecutor = Executors.newScheduledThreadPool(1);

        Runnable scheduledTask = () -> System.out.println("Scheduled task executed");

        scheduledExecutor.schedule(scheduledTask, 3, TimeUnit.SECONDS);

        // Submitting Callable tasks
        Callable<String> callableTask = () -> {
            TimeUnit.SECONDS.sleep(1);
            return "Callable Task Result";
        };

        Future<String> future = fixedThreadPool.submit(callableTask);

        System.out.println("Callable task result: " + future.get());

        // Shutting down the executor services
        fixedThreadPool.shutdown();
        scheduledExecutor.shutdown();

        try {
            if (!fixedThreadPool.awaitTermination(5, TimeUnit.SECONDS)) {
                fixedThreadPool.shutdownNow();
            }
            if (!scheduledExecutor.awaitTermination(5, TimeUnit.SECONDS)) {
                scheduledExecutor.shutdownNow();
            }
        } catch (InterruptedException e) {
            fixedThreadPool.shutdownNow();
            scheduledExecutor.shutdownNow();
        }
    }
}
```

### Key Points:
- **Executor**: Basic interface with the `execute` method for running tasks.
- **ExecutorService**: Extends `Executor` and adds methods for managing task lifecycle.
- **ScheduledExecutorService**: Extends `ExecutorService` for scheduling tasks.
- **Executors**: Utility class for creating various types of executor services.

The executor framework in Java provides a robust and flexible way to handle task execution, allowing for better resource management and easier concurrency control.

Awesome! Here’s a **runnable Java code** that compares `volatile`, `AtomicInteger`, and `synchronized` counters using **4 threads**, each doing 250,000 increments (1 million total).

---

## 🚀 Java Code to Compare Counter Strategies

```java
import java.util.concurrent.*;
import java.util.concurrent.atomic.AtomicInteger;

public class CounterComparison {

    static final int NUM_THREADS = 4;
    static final int INCREMENTS_PER_THREAD = 250_000;

    public static void main(String[] args) throws InterruptedException {
        runTest(new VolatileCounter(), "Volatile Counter");
        runTest(new AtomicCounter(), "Atomic Counter");
        runTest(new SynchronizedCounter(), "Synchronized Counter");
    }

    static void runTest(Counter counter, String name) throws InterruptedException {
        ExecutorService executor = Executors.newFixedThreadPool(NUM_THREADS);

        long start = System.nanoTime();

        for (int i = 0; i < NUM_THREADS; i++) {
            executor.submit(() -> {
                for (int j = 0; j < INCREMENTS_PER_THREAD; j++) {
                    counter.increment();
                }
            });
        }

        executor.shutdown();
        executor.awaitTermination(1, TimeUnit.MINUTES);

        long end = System.nanoTime();
        System.out.printf("%-25s | Final Count: %d | Time: %d ms%n",
                name, counter.getCount(), (end - start) / 1_000_000);
    }

    interface Counter {
        void increment();
        int getCount();
    }

    // ⚠ Not thread-safe
    static class VolatileCounter implements Counter {
        volatile int count = 0;

        public void increment() {
            count++;
        }

        public int getCount() {
            return count;
        }
    }

    // ✅ Thread-safe
    static class AtomicCounter implements Counter {
        AtomicInteger count = new AtomicInteger(0);

        public void increment() {
            count.incrementAndGet();
        }

        public int getCount() {
            return count.get();
        }
    }

    // ✅ Thread-safe (but slower)
    static class SynchronizedCounter implements Counter {
        int count = 0;

        public synchronized void increment() {
            count++;
        }

        public int getCount() {
            return count;
        }
    }
}
```

---

## 🧾 Expected Output (Example)

```
Volatile Counter          | Final Count: 837492  | Time: 15 ms
Atomic Counter            | Final Count: 1000000 | Time: 19 ms
Synchronized Counter      | Final Count: 1000000 | Time: 25 ms
```

> ☠ `VolatileCounter` gives wrong result — not atomic  
> ✅ `AtomicCounter` and `SynchronizedCounter` give correct result

---

Want a JMH version of this benchmark or would you like to add G1 GC/heap profiling around this?