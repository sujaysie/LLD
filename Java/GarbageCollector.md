Great! Let’s dive into the **types of Garbage Collection (GC)** in Java. Java's **Garbage Collector** automatically reclaims memory, cleaning up **unused objects** to ensure efficient memory management.

### 🔹 Types of Garbage Collection in Java

Java provides several garbage collection strategies, each optimized for different application needs. These strategies are based on **how memory is divided** and how objects are collected.

---

### 🧹 **Generational Garbage Collection (GC)**

Java's memory is divided into different **generations** to optimize garbage collection:

1. **Young Generation** (Minor GC)
2. **Old Generation** (Major GC or Full GC)
3. **Permanent Generation / Metaspace** (class metadata)

---

### 🗂️ **1. Young Generation GC (Minor GC)**

- **Purpose**: Handles newly created objects.
- **Memory Areas**:
    - **Eden Space**: Where new objects are allocated.
    - **Survivor Spaces**: Objects that survive the first few GC cycles move here.
- **Trigger**: When **Eden** space gets filled up.
- **Result**: **Minor GC** (Only cleans the Young Generation).

| Step | What Happens |
|------|--------------|
| 1. New objects are allocated in Eden | | 
| 2. When Eden is full, Minor GC occurs | |
| 3. Objects that survive are moved to Survivor Space | |
| 4. Surviving objects are eventually promoted to Old Generation | |

**Example**: If objects are short-lived (e.g., in web applications), **Young GC** is frequent, improving performance.

---

### 🗂️ **2. Old Generation GC (Major GC or Full GC)**

- **Purpose**: Handles long-lived objects that have survived several Minor GCs.
- **Memory Areas**:
    - **Old Generation**: Objects promoted from Young Generation.
- **Trigger**: When **Old Generation** runs out of space.
- **Result**: **Major GC** (Cleans both Young and Old Generations).

**Note**: **Full GC** is more expensive because it involves both Young and Old Generations.

---

### 🗂️ **3. Metaspace (previously Permanent Generation)**

- **Purpose**: Stores class metadata, method definitions, and static variables.
- **Old PermGen in Java 7 and before** → **Metaspace in Java 8+** (dynamic and not part of the heap).
- **Trigger**: When **Metaspace** is full (can lead to `OutOfMemoryError` if not managed).

---

### ⚙️ **GC Algorithms**

Java supports several **GC algorithms**, depending on the needs of your application. Here are the common types:

---

#### **1. Serial GC**

- **Description**: A simple garbage collector that uses a single thread to collect garbage.
- **Best for**: Small applications, or when running on single-threaded environments (like some embedded systems).
- **Trigger**: Both Minor and Full GC are done by the same thread.

**Command to enable**:
```bash
-XX:+UseSerialGC
```

---

#### **2. Parallel GC (Throughput Collector)**

- **Description**: Uses multiple threads to perform garbage collection.
- **Best for**: Multi-threaded applications where throughput (execution time) is crucial.
- **Trigger**: Minor GCs are done in parallel. Full GC can also be parallelized.

**Command to enable**:
```bash
-XX:+UseParallelGC
```

---

#### **3. CMS GC (Concurrent Mark-Sweep)**

- **Description**: Minimizes pause times by performing most of the work concurrently with the application threads.
- **Best for**: Applications that need low pause times.
- **Trigger**: Divides the GC cycle into phases: Initial Mark, Concurrent Mark, Concurrent Sweep, and Final Remark.

**Note**: With CMS, Full GC can still happen when memory is full or during pauses.

**Command to enable**:
```bash
-XX:+UseConcMarkSweepGC
```

---

#### **4. G1 GC (Garbage-First)**

- **Description**: Aiming for high throughput with low latency. Breaks memory into regions and tries to focus on the most garbage-filled regions.
- **Best for**: Applications with large heaps and low-latency requirements.
- **Trigger**: G1 divides memory into regions and aims for predictable pause times, balancing both Young and Old GCs.

**Command to enable**:
```bash
-XX:+UseG1GC
```

---

#### **5. ZGC (Z Garbage Collector)**

- **Description**: A **low-latency** garbage collector designed to handle extremely large heaps with minimal pause times.
- **Best for**: Low-latency applications, large heaps (multi-terabyte).
- **Trigger**: Uses a **region-based approach** with concurrent marking and sweeping.

**Command to enable**:
```bash
-XX:+UseZGC
```

---

### 🧹 **GC Phases (Generic)**

| Phase         | Description                                         |
|---------------|-----------------------------------------------------|
| **Mark**      | Identifies which objects are still in use.         |
| **Sweep**     | Reclaims memory of unreachable objects.            |
| **Compact**   | Optionally moves objects to reduce fragmentation.  |

---

## ✅ TL;DR

| GC Type              | Description                                     | Best For                        |
|----------------------|-------------------------------------------------|---------------------------------|
| **Serial GC**         | Simple, single-threaded GC                      | Small apps or single-threaded   |
| **Parallel GC**       | Multi-threaded, throughput-focused              | Multi-threaded, high throughput |
| **CMS GC**            | Concurrent with low pause times                 | Low-latency apps                |
| **G1 GC**             | Predictable pause times with region-based GC    | Large heaps, low latency        |
| **ZGC**               | Low-latency, concurrent GC for huge heaps       | Ultra-low-latency, large heaps  |

---

Let me know if you need more details on tuning or using a specific GC type!