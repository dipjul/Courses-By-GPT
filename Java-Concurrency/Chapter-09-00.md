# Java Concurrency Utilities

## Phaser and CyclicBarrier

**Phaser:**

**Overview:** `Phaser` is a synchronization barrier that allows a set of threads to synchronize their execution phases. It's a more flexible alternative to `CyclicBarrier` as it supports dynamic registration of parties (threads) and allows for more complex synchronization scenarios.

**Key Concepts:**

1.  **Phase Synchronization:**
    
    *   `Phaser` maintains a phase number, and threads can synchronize at the end of each phase.
2.  **Dynamic Registration:**
    
    *   Threads can dynamically register and deregister with a `Phaser` during the execution.

**Example:**

```java
import java.util.concurrent.Phaser;

public class PhaserExample {
    public static void main(String[] args) {
        Phaser phaser = new Phaser(3); // 3 parties (threads) to synchronize

        new Thread(() -> {
            System.out.println("Thread 1: Phase 1");
            phaser.arriveAndAwaitAdvance(); // Await others

            System.out.println("Thread 1: Phase 2");
            phaser.arriveAndAwaitAdvance(); // Await others

            System.out.println("Thread 1: Phase 3");
            phaser.arriveAndAwaitAdvance(); // Await others
        }).start();

        new Thread(() -> {
            System.out.println("Thread 2: Phase 1");
            phaser.arriveAndAwaitAdvance(); // Await others

            System.out.println("Thread 2: Phase 2");
            phaser.arriveAndAwaitAdvance(); // Await others

            System.out.println("Thread 2: Phase 3");
            phaser.arriveAndAwaitAdvance(); // Await others
        }).start();

        new Thread(() -> {
            System.out.println("Thread 3: Phase 1");
            phaser.arriveAndAwaitAdvance(); // Await others

            System.out.println("Thread 3: Phase 2");
            phaser.arriveAndAwaitAdvance(); // Await others

            System.out.println("Thread 3: Phase 3");
            phaser.arriveAndAwaitAdvance(); // Await others
        }).start();
    }
}

```
In this example, three threads synchronize at the end of each phase using `arriveAndAwaitAdvance`.

**CyclicBarrier:**

**Overview:** `CyclicBarrier` is a synchronization barrier that allows a fixed number of threads to synchronize at a specific point. Once the specified number of threads arrives, the barrier is tripped, and all threads can proceed.

**Key Concepts:**

1.  **Fixed Number of Parties:**
    *   The number of threads that must invoke `await` before the barrier is tripped is fixed during the construction of the `CyclicBarrier`.

**Example:**

```java
import java.util.concurrent.CyclicBarrier;

public class CyclicBarrierExample {
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(3); // 3 parties (threads) to synchronize

        new Thread(() -> {
            System.out.println("Thread 1: Phase 1");
            awaitBarrier(cyclicBarrier);

            System.out.println("Thread 1: Phase 2");
            awaitBarrier(cyclicBarrier);

            System.out.println("Thread 1: Phase 3");
            awaitBarrier(cyclicBarrier);
        }).start();

        new Thread(() -> {
            System.out.println("Thread 2: Phase 1");
            awaitBarrier(cyclicBarrier);

            System.out.println("Thread 2: Phase 2");
            awaitBarrier(cyclicBarrier);

            System.out.println("Thread 2: Phase 3");
            awaitBarrier(cyclicBarrier);
        }).start();

        new Thread(() -> {
            System.out.println("Thread 3: Phase 1");
            awaitBarrier(cyclicBarrier);

            System.out.println("Thread 3: Phase 2");
            awaitBarrier(cyclicBarrier);

            System.out.println("Thread 3: Phase 3");
            awaitBarrier(cyclicBarrier);
        }).start();
    }

    private static void awaitBarrier(CyclicBarrier cyclicBarrier) {
        try {
            cyclicBarrier.await(); // Await others
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```
In this example, three threads synchronize at the end of each phase using `await` on the `CyclicBarrier`.

## CountDownLatch

**Overview:** `CountDownLatch` is a synchronization aid that allows one or more threads to wait until a set of operations being performed in other threads completes. It is initialized with a count, and threads waiting for the operations to complete decrement the count until it reaches zero.

**Example:**

```java
import java.util.concurrent.CountDownLatch;

public class CountDownLatchExample {
    public static void main(String[] args) {
        CountDownLatch countDownLatch = new CountDownLatch(3); // Number of tasks to complete

        new Thread(() -> {
            System.out.println("Task 1: Performing operation");
            countDownLatch.countDown(); // Signal completion

            System.out.println("Task 1: Another operation");
            countDownLatch.countDown(); // Signal completion
        }).start();

        new Thread(() -> {
            System.out.println("Task 2: Performing operation");
            countDownLatch.countDown(); // Signal completion
        }).start();

        new Thread(() -> {
            try {
                countDownLatch.await(); // Wait for all tasks to complete
                System.out.println("All tasks completed. Proceeding...");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
    }
}

```
In this example, three tasks signal completion by calling `countDown`, and a waiting thread proceeds once the count reaches zero.

## Semaphore for Resource Controlling

**Overview:** `Semaphore` is a synchronization primitive that limits the number of concurrent threads that can access a shared resource. It is often used to control access to resources with limited capacity.

**Example:**

```java
import java.util.concurrent.Semaphore;

public class SemaphoreExample {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(2); // Maximum of 2 concurrent threads

        new Thread(() -> {
            try {
                semaphore.acquire(); // Acquire a permit
                System.out.println("Thread 1: Accessing shared resource");
                // Perform resource access
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                semaphore.release(); // Release thepermit after accessing the shared resource.
            }

        }).start();

        new Thread(() -> {
            try {
                semaphore.acquire(); // Acquire a permit
                System.out.println("Thread 2: Accessing shared resource");
                // Perform resource access
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                semaphore.release(); // Release the permit after accessing the shared resource
            }

        }).start();

        new Thread(() -> {
            try {
                semaphore.acquire(); // Acquire a permit
                System.out.println("Thread 3: Accessing shared resource");
                // Perform resource access
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                semaphore.release(); // Release the permit after accessing the shared resource
            }

        }).start();
    }
}
```


In this example, the `Semaphore` is initialized with a maximum of 2 permits, limiting the number of concurrent threads that can access the shared resource. Each thread acquires a permit before accessing the resource and releases it afterward.


**Semaphore with Fairness:**

```java
import java.util.concurrent.Semaphore;

public class FairSemaphoreExample {
    public static void main(String[] args) {
        Semaphore fairSemaphore = new Semaphore(2, true); // Maximum of 2 concurrent threads with fairness

        new Thread(() -> {
            try {
                fairSemaphore.acquire(); // Acquire a permit
                System.out.println("Thread 1: Accessing shared resource");
                // Perform resource access
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                fairSemaphore.release(); // Release the permit after accessing the shared resource
            }

        }).start();

        new Thread(() -> {
            try {
                fairSemaphore.acquire(); // Acquire a permit
                System.out.println("Thread 2: Accessing shared resource");
                // Perform resource access
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                fairSemaphore.release(); // Release the permit after accessing the shared resource
            }

        }).start();

        new Thread(() -> {
            try {
                fairSemaphore.acquire(); // Acquire a permit
                System.out.println("Thread 3: Accessing shared resource");
                // Perform resource access
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                fairSemaphore.release(); // Release the permit after accessing the shared resource
            }

        }).start();
    }
}

```
In this example, a fair `Semaphore` is created with fairness set to `true`. Fairness ensures that threads acquire permits in the order they requested them, providing a fair and predictable order of access to the shared resource.

## Recap:

1.  **Phaser:**
    
    *   `Phaser` supports dynamic registration of parties, making it suitable for scenarios where the number of participants may change.
    *   Threads synchronize at the end of each phase.
2.  **CyclicBarrier:**
    
    *   `CyclicBarrier` allows a fixed number of threads to synchronize at a specific point.
    *   Threads wait at the barrier until the specified number of parties arrive.
3.  **CountDownLatch:**
    
    *   `CountDownLatch` allows one or more threads to wait until a set of operations completes.
    *   Threads decrement the count until it reaches zero.
4.  **Semaphore:**
    
    *   `Semaphore` controls access to a shared resource by limiting the number of concurrent threads.
    *   It can be used with or without fairness, depending on the desired order of access.

> These Java concurrency utilities—`Phaser`, `CyclicBarrier`, `CountDownLatch`, and `Semaphore`—provide powerful mechanisms for coordinating the execution of multiple threads, managing shared resources, and controlling access to critical sections.
> These utilities provide essential building blocks for creating robust concurrent applications in Java.
> The choice of utility depends on the specific synchronization requirements and coordination patterns in your application.
>  Understanding when and how to use each utility can greatly enhance the efficiency and correctness of your concurrent programs.

## Quiz

**Phaser and CyclicBarrier:**

1.  **Phaser Overview:**
    
    *   What is the purpose of `Phaser` in Java concurrency, and how does it differ from `CyclicBarrier`?
    *   Explain one key concept of `Phaser` that makes it flexible in synchronization scenarios.
2.  **CyclicBarrier Usage:**
    
    *   How does `CyclicBarrier` work in synchronizing a fixed number of threads?
    *   Provide an example scenario where using `CyclicBarrier` is appropriate.

**CountDownLatch:**

3.  **CountDownLatch Overview:**
    
    *   What is the main purpose of `CountDownLatch` in Java concurrency?
    *   How does the count in `CountDownLatch` get decremented?
4.  **CountDownLatch Example:**
    
    *   Provide a simple code example demonstrating the use of `CountDownLatch` with multiple tasks.

**Semaphore for Resource Controlling:**

5.  **Semaphore Functionality:**
    
    *   Explain the primary functionality of a `Semaphore` in Java concurrency.
    *   What role does a permit play in the context of a `Semaphore`?
6.  **Semaphore Fairness:**
    
    *   What is the significance of setting a `Semaphore` to be fair?
    *   How does fairness impact the order in which threads acquire permits?

**Bonus Question:**

7.  **Comparison:**
    *   Compare and contrast the use cases of `Phaser`, `CyclicBarrier`, `CountDownLatch`, and `Semaphore` in concurrent programming. Provide an example scenario where each would be the most suitable.

