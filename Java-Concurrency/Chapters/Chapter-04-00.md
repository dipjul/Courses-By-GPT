# Locks and Concurrent Collections
This chapter delves into advanced synchronization mechanisms in Java, focusing on ReentrantLock, ReadWriteLock, and an overview of concurrent collections. 
These features are essential for achieving improved concurrency and thread safety in Java applications.

## ReentrantLock Overview:
The ReentrantLock class in Java provides a more flexible and sophisticated approach to synchronization compared to the traditional synchronized keyword. 
It is part of the `java.util.concurrent.locks` package and allows developers to have more control over the locking and unlocking of resources in a multithreaded environment.

### Features of ReentrantLock:

* **Reentrancy**: The term "Reentrant" in ReentrantLock signifies that a thread, which already holds a lock, can acquire it again without blocking. This feature is not available with the traditional synchronized keyword.

* **Fairnes**s: ReentrantLock supports both fair and unfair lock acquisition. In a fair lock, the longest-waiting thread gets the lock first. This can help in preventing thread starvation.

* **Condition Support**: ReentrantLock allows the creation of multiple Condition objects, which can be used to implement advanced synchronization patterns, such as producer-consumer scenarios.

* **Interruptible Locking**: Threads waiting to acquire a lock with ReentrantLock can be interrupted while waiting, allowing for more responsive handling of thread interruptions.

### Advantages over synchronized
1. **Reentrancy**:
  - Explanation: The ability of a thread to acquire the same lock multiple times.
  - Advantage: It simplifies the programming model in situations where a method may need to call another method that also requires the same lock.
2. **Fairness Options**:
  - Explanation: ReentrantLock allows developers to choose between fair and unfair lock acquisition policies.
  - Advantage: Fair locks can help in scenarios where ensuring fairness is critical to prevent some threads from being starved.
3. **Condition Objects**:
  - Explanation: The support for multiple Condition objects allows more flexible coordination between threads.
  - Advantage: It enables the implementation of complex synchronization patterns beyond what is achievable with traditional locks.
4. **Explicit Locking and Unlocking**:
  - Explanation: Unlike synchronized, where locking and unlocking are implicit, ReentrantLock provides explicit methods for acquiring and releasing locks (lock() and unlock()).
  - Advantage: Explicit control allows for more fine-grained synchronization, enabling developers to design more efficient and responsive locking strategies.

### Explicit Locking and Unlocking
* **Explicit Locking**:
  - Usage: The lock() method of ReentrantLock is used to acquire a lock explicitly.
  - Advantage: Explicit locking allows developers to control exactly where a lock is acquired, providing more granularity.

* **Explicit Unlocking**:
  - Usage: The unlock() method of ReentrantLock is used to release a lock explicitly.
  - Advantage: Explicit unlocking ensures that the lock is released precisely when needed, preventing accidental or premature releases.

* **Considerations**:
  - It is crucial to use try-finally blocks to ensure that the lock is always released, even if an exception occurs within the locked region.

* **Example**:
  ```java
  import java.util.concurrent.locks.Lock;
  import java.util.concurrent.locks.ReentrantLock;
  
  class SharedResource {
      private int count = 0;
      private Lock lock = new ReentrantLock(); // ReentrantLock used for synchronization
  
      public void increment() {
          lock.lock(); // Explicit lock acquisition
          try {
              count++;
              System.out.println(Thread.currentThread().getName() + " - Count: " + count);
          } finally {
              lock.unlock(); // Explicit lock release in a finally block
          }
      }
  }

  ```
> In summary, ReentrantLock offers advanced features and explicit control over locking mechanisms,
> providing a powerful alternative to the traditional synchronized keyword in situations where fine-grained synchronization and additional features are required.
> Developers should carefully consider the advantages and choose the synchronization mechanism that best suits their specific concurrency requirements.

## ReadWriteLock for Improved Concurrency
ReadWriteLock is an interface in Java, introduced to address certain scenarios where improved concurrency can be achieved by distinguishing between read and write operations on a shared resource. 
Unlike a simple lock, such as the intrinsic lock provided by synchronized, ReadWriteLock allows multiple threads to read simultaneously while ensuring exclusive access for write operations.

### Use Case:
In situations where a resource is read more frequently than it is modified, ReadWriteLock can significantly improve overall system throughput by allowing multiple threads to read concurrently.

### Read Lock and Write Lock
#### Read Lock:
* **Acquisition**: Multiple threads can simultaneously acquire the read lock.
* **Concurrency**: Read locks are shared, meaning multiple threads can read the data concurrently without blocking each other.
* **Usage**: Ideal for scenarios where the shared resource is mostly read.

#### Write Lock:
* **Acquisition**: Only one thread can acquire the write lock at a time.
* **Concurrency**: Exclusive access is enforced during a write lock, preventing other threads from reading or writing concurrently.
* **Usage**: Appropriate for scenarios where a thread intends to modify the shared resource.

#### Benefits of ReadWriteLock
* **Improved Concurrency**:
  - Explanation: Read operations can be performed concurrently by multiple threads without locking each other.
  - Benefit: This allows for increased throughput in scenarios where reads are frequent and writes are relatively infrequent.

* **Reduced Blocking**:
  - Explanation: With a traditional lock, write operations would block all other threads, including those attempting to read.
  - Benefit: ReadWriteLock minimizes contention by allowing parallel reads, reducing the likelihood of threads being blocked unnecessarily.

* **Optimal Resource Utilization**:
  - Explanation: ReadWriteLock is particularly beneficial when the shared resource is read-heavy.
  - Benefit: It ensures that multiple threads can efficiently access and retrieve data without introducing unnecessary contention.

* **Avoiding Writer Starvation**:
  - Explanation: In scenarios where write operations are infrequent, read-heavy workloads can proceed without starving write operations.
  - Benefit: This helps maintain a balance between read and write access, preventing writer threads from being consistently delayed.

### Considerations:
While ReadWriteLock can improve concurrency in certain scenarios, it might not be suitable for all use cases. It is essential to evaluate the specific requirements of the application.

### Example:
  ```java
  import java.util.concurrent.locks.ReadWriteLock;
  import java.util.concurrent.locks.ReentrantReadWriteLock;
  
  class SharedResource {
      private int data = 0;
      private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();
  
      // Read operation, multiple threads can read simultaneously
      public int readData() {
          readWriteLock.readLock().lock();
          try {
              System.out.println(Thread.currentThread().getName() + " is reading data: " + data);
              return data;
          } finally {
              readWriteLock.readLock().unlock();
          }
      }
  
      // Write operation, exclusive access required
      public void writeData(int newData) {
          readWriteLock.writeLock().lock();
          try {
              System.out.println(Thread.currentThread().getName() + " is writing data: " + newData);
              data = newData;
          } finally {
              readWriteLock.writeLock().unlock();
          }
      }
  }
  
  public class ReadWriteLockExample {
      public static void main(String[] args) {
          SharedResource sharedResource = new SharedResource();
  
          // Creating multiple threads for reading
          for (int i = 0; i < 3; i++) {
              Thread readerThread = new Thread(() -> {
                  sharedResource.readData();
              }, "Reader-" + i);
              readerThread.start();
          }
  
          // Creating a thread for writing
          Thread writerThread = new Thread(() -> {
              sharedResource.writeData(42);
          }, "Writer");
          writerThread.start();
      }
  }

  ```
In this example, multiple reader threads can concurrently read the data from the shared resource using the read lock (`readWriteLock.readLock().lock()`). 
However, only one writer thread at a time can modify the data, ensuring exclusive access with the write lock (`readWriteLock.writeLock().lock()`). 
This helps improve concurrency by allowing concurrent reads while preventing write conflicts.

The decision to use ReadWriteLock depends on the nature of the shared resource and the expected frequency of read and write operations.

> In summary, ReadWriteLock provides a mechanism for achieving improved concurrency in scenarios where the shared resource is predominantly read.
> It allows multiple threads to read concurrently, preventing unnecessary blocking and enhancing the overall efficiency of the system.
> However, careful consideration of the application's characteristics is necessary to determine whether ReadWriteLock is the most suitable synchronization mechanism.

## Overview of Concurrent Collections
* **Introduction**: Exploring collections designed for thread-safe access in concurrent programming.
* **ConcurrentHashMap**: Understanding a thread-safe map implementation for concurrent access.
* **CopyOnWriteArrayList**: Exploring a thread-safe list implementation that creates a new copy on every modification.
* **Example**:
  ```java
  import java.util.Map;
  import java.util.concurrent.ConcurrentHashMap;
  
  public class ConcurrentCollectionsExample {
      public static void main(String[] args) {
          // Creating a thread-safe map
          Map<String, Integer> concurrentMap = new ConcurrentHashMap<>();
  
          // Performing thread-safe operations
          concurrentMap.put("Key", 1);
          int value = concurrentMap.get("Key");
          System.out.println("Value: " + value);
      }
  }

  ```

## Quiz:
* How does the use of ReentrantLock offer more flexibility than the synchronized keyword?
* What advantages does explicit locking and unlocking provide when using ReentrantLock?
* In what scenarios might the use of ReadWriteLock be beneficial for improved concurrency?
