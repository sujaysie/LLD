# `Synchronized` keyword in java
In Java, the `synchronized` keyword is used to control access to a critical section of code, ensuring that only one thread can execute that section at a time. This is crucial in a multi-threaded environment to prevent concurrent access to shared resources, which could lead to data inconsistency or race conditions.

### Key Concepts of `synchronized`

1. **Synchronized Method**:
    - Locks the object (`this`) for which the method is called.
    - If a thread is executing a synchronized method on an object, no other thread can execute any synchronized method on the same object.
    - Example:
      ```java
      public class Counter {
          private int count = 0;
 
          public synchronized void increment() {
              count++;
          }
 
          public synchronized int getCount() {
              return count;
          }
      }
      ```

2. **Synchronized Block**:
    - Provides a finer level of control by locking only a portion of the method or a specific block of code.
    - You can synchronize on any object, allowing different blocks of code to synchronize on different objects.
    - Example:
      ```java
      public class Counter {
          private int count = 0;
          private final Object lock = new Object();
 
          public void increment() {
              synchronized (lock) {
                  count++;
              }
          }
 
          public int getCount() {
              synchronized (lock) {
                  return count;
              }
          }
      }
      ```

3. **Class Level Lock**:
    - Static methods can be synchronized, which locks the `Class` object associated with the class, rather than an instance of the class.
    - Example:
      ```java
      public class StaticCounter {
          private static int count = 0;
 
          public static synchronized void increment() {
              count++;
          }
 
          public static synchronized int getCount() {
              return count;
          }
      }
      ```

### Example

Here is a simple example demonstrating the use of synchronized methods and synchronized blocks:

```java
public class SynchronizedExample {
    private int count = 0;

    // Synchronized method
    public synchronized void increment() {
        count++;
    }

    // Synchronized block
    public void incrementWithBlock() {
        synchronized (this) {
            count++;
        }
    }

    public synchronized int getCount() {
        return count;
    }

    public static void main(String[] args) throws InterruptedException {
        SynchronizedExample example = new SynchronizedExample();

        // Create multiple threads to increment the counter
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                example.increment();
            }
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                example.incrementWithBlock();
            }
        });

        // Start the threads
        t1.start();
        t2.start();

        // Wait for threads to finish
        t1.join();
        t2.join();

        // Get the final count
        System.out.println("Final count: " + example.getCount());
    }
}
```

### Key Points

- **Mutual Exclusion**: Only one thread can execute a synchronized method or block at a time for a given object or class.
- **Visibility**: Changes made by one thread to the synchronized block are visible to other threads. This ensures that a thread cannot cache variables and must read the most recent values from memory.
- **Monitor**: Every object in Java has an intrinsic lock or monitor associated with it. When a thread enters a synchronized method/block, it acquires the lock. Other threads attempting to enter any synchronized method/block on the same object are blocked until the lock is released.

### Use Cases

- **Thread Safety**: When multiple threads need to access shared resources, synchronization ensures that only one thread can manipulate the resource at a time.
- **Atomic Operations**: Ensures that a series of operations are completed without interruption, maintaining data consistency.

### Drawbacks

- **Performance Overhead**: Synchronization can lead to contention and overhead due to thread waiting and context switching.
- **Deadlock**: Improper use of synchronization can lead to deadlocks where two or more threads are waiting indefinitely for each other to release locks.

Using the `synchronized` keyword appropriately is crucial for maintaining data consistency and thread safety in concurrent programming. However, it should be used judiciously to avoid performance bottlenecks and potential deadlocks.

# `Lock` interface in java

In Java, the `java.util.concurrent.locks.Lock` interface provides a more flexible and powerful alternative to the built-in synchronization provided by the `synchronized` keyword. The `Lock` interface is part of the `java.util.concurrent.locks` package and offers more sophisticated locking mechanisms, such as try-locking, timed-locking, and interruptible locking.

### Key Methods of the Lock Interface

1. **`void lock()`**:
   - Acquires the lock, blocking the thread until the lock is available.

2. **`void lockInterruptibly()`**:
   - Acquires the lock unless the thread is interrupted while waiting.

3. **`boolean tryLock()`**:
   - Attempts to acquire the lock without waiting. Returns `true` if the lock was acquired, and `false` otherwise.

4. **`boolean tryLock(long time, TimeUnit unit)`**:
   - Attempts to acquire the lock, waiting up to the specified time if necessary. Returns `true` if the lock was acquired, and `false` otherwise.

5. **`void unlock()`**:
   - Releases the lock.

6. **`Condition newCondition()`**:
   - Returns a new `Condition` instance bound to this `Lock` instance. Conditions provide a means for a thread to suspend execution until a condition is met.

### Example of Using Lock

Here’s an example demonstrating the basic usage of the `Lock` interface using `ReentrantLock`, a common implementation of the `Lock` interface:

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class LockExample {
    private final Lock lock = new ReentrantLock();
    private int count = 0;

    public void increment() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }

    public int getCount() {
        lock.lock();
        try {
            return count;
        } finally {
            lock.unlock();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        LockExample example = new LockExample();

        // Create multiple threads to increment the counter
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                example.increment();
            }
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                example.increment();
            }
        });

        // Start the threads
        t1.start();
        t2.start();

        // Wait for threads to finish
        t1.join();
        t2.join();

        // Get the final count
        System.out.println("Final count: " + example.getCount());
    }
}
```

### Example of Using tryLock

Using `tryLock` allows the program to attempt to acquire the lock without blocking indefinitely:

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class TryLockExample {
    private final Lock lock = new ReentrantLock();
    private int count = 0;

    public void increment() {
        if (lock.tryLock()) {
            try {
                count++;
            } finally {
                lock.unlock();
            }
        } else {
            System.out.println("Could not acquire lock, performing alternative actions");
        }
    }

    public int getCount() {
        lock.lock();
        try {
            return count;
        } finally {
            lock.unlock();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        TryLockExample example = new TryLockExample();

        // Create multiple threads to increment the counter
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                example.increment();
            }
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                example.increment();
            }
        });

        // Start the threads
        t1.start();
        t2.start();

        // Wait for threads to finish
        t1.join();
        t2.join();

        // Get the final count
        System.out.println("Final count: " + example.getCount());
    }
}
```

### Key Points

- **ReentrantLock**: A common implementation of the `Lock` interface, which provides the same basic behavior and semantics as the implicit monitor lock accessed using `synchronized` methods and statements, with extended capabilities.
- **Fairness**: `ReentrantLock` can be created with a fairness policy, ensuring that the longest-waiting thread is given access to the lock.
  ```java
  Lock fairLock = new ReentrantLock(true); // Fair lock
  ```
- **Conditions**: Using the `newCondition()` method, you can create condition variables that provide a more flexible waiting mechanism than the `wait()` and `notify()` methods used with synchronized blocks.

Using `Lock` and its implementations, such as `ReentrantLock`, provides more control and flexibility over thread synchronization compared to the `synchronized` keyword, allowing for non-blocking attempts to acquire locks, interruptible lock acquisition, and timed lock acquisition.