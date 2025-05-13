In Java, the `Future<V>` interface represents the result of an asynchronous computation. It provides methods to check if the computation is complete, wait for its completion, and retrieve the result. `Future<V>` is part of the `java.util.concurrent` package and is often used with `ExecutorService` to manage asynchronous tasks.

### Key Methods of Future<V>
1. **`V get()`**:
   - Waits if necessary for the computation to complete, and then retrieves its result.
   - Throws `InterruptedException`, `ExecutionException`, and `CancellationException`.
   
2. **`V get(long timeout, TimeUnit unit)`**:
   - Waits for at most the given time for the computation to complete, and then retrieves its result.
   - Throws `TimeoutException` in addition to the exceptions thrown by `get()`.
   
3. **`boolean cancel(boolean mayInterruptIfRunning)`**:
   - Attempts to cancel the execution of the task.
   - `true` if the task was cancelled successfully, `false` otherwise.
   
4. **`boolean isCancelled()`**:
   - Returns `true` if the task was cancelled before it completed normally.
   
5. **`boolean isDone()`**:
   - Returns `true` if the task completed (either normally or by cancellation).

### Example Usage of Future<V>
Here is an example that demonstrates how to use `Future<V>` with an `ExecutorService` to run an asynchronous task:

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;

public class FutureExample {

    public static void main(String[] args) {
        ExecutorService executorService = Executors.newSingleThreadExecutor();

        // Callable task that returns a result after sleeping for 2 seconds
        Callable<String> callableTask = () -> {
            TimeUnit.SECONDS.sleep(2);
            return "Task's result";
        };

        // Submit the task to the executor service
        Future<String> future = executorService.submit(callableTask);

        // Perform other operations while the task is running

        try {
            // Wait for the task to complete and get the result
            String result = future.get(); // This will block until the task is complete
            System.out.println("Result: " + result);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }         
        // Handling timeouts
        Future<String> futureWithTimeout = executorService.submit(callableTask);
        try {
            String result = futureWithTimeout.get(1, TimeUnit.SECONDS); // This will throw TimeoutException
            System.out.println("Result: " + result);
        } catch (TimeoutException e) {
            System.out.println("Task timed out");
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        } finally {
            // Shut down the executor service
            executorService.shutdown();
        }
    }
}
```

### Explanation
1. **Callable Task**: 
   - A `Callable<String>` task is created that sleeps for 2 seconds and then returns a result.
   
2. **Submitting the Task**:
   - The task is submitted to an `ExecutorService` using `submit()`, which returns a `Future<String>` representing the pending result of the task.
   
3. **Getting the Result**:
   - The `get()` method is called on the `Future` object to wait for the task to complete and retrieve the result. This call blocks until the task is done.
   
4. **Timeout Handling**:
   - Another task is submitted, and `get(1, TimeUnit.SECONDS)` is called. This method will throw a `TimeoutException` if the task doesn't complete within the specified time.
   
5. **Shutting Down the Executor Service**:
   - After the tasks are done, the executor service is shut down to release resources.

### Key Points
- `Future<V>` provides a way to work with the results of asynchronous computations.
- It includes methods to check if a task is complete, cancel a task, and get the result with or without a timeout.
- Typically used with `ExecutorService` to manage and retrieve results of asynchronous tasks.

Using `Future<V>` allows for effective handling of concurrency by enabling tasks to run asynchronously while providing mechanisms to wait for, retrieve, or cancel their results as needed.