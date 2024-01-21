# Java Threads

Creating and managing threads in Java is fundamental to concurrent programming. 
This section provides a comprehensive guide on how to create and effectively manage threads using the Thread class and the Runnable interface.

## Creating Threads with Thread Class

### 1. Extending Thread Class:
* Define a new class that extends the Thread class.
* Override the `run()` method with the code to be executed by the thread.
* Instantiate the new class and invoke the `start()` method to begin thread execution.
  ```java
  // Example of creating a thread by extending the Thread class
  class MyThread extends Thread {
      public void run() {
          // Code to be executed by the thread
          for (int i = 1; i <= 5; i++) {
              System.out.println("Thread A - Count: " + i);
              try {
                  Thread.sleep(1000); // Simulating some work
              } catch (InterruptedException e) {
                  e.printStackTrace();
              }
          }
      }
  }
  
  public class ThreadExample {
      public static void main(String[] args) {
          // Creating an instance of MyThread and starting the thread
          MyThread threadA = new MyThread();
          threadA.start();
          
          // Code in the main method continues to execute concurrently with the thread
          for (int i = 1; i <= 5; i++) {
              System.out.println("Main Thread - Count: " + i);
              try {
                  Thread.sleep(500); // Simulating some work
              } catch (InterruptedException e) {
                  e.printStackTrace();
              }
          }
      }
  }
  
  ```
### 2. Implementing Runnable Interface:

* Create a class that implements the Runnable interface.
* Implement the `run()` method with the code to be executed.
* Create an instance of the class and pass it to the constructor of a Thread object.
* Start the thread using the `start()` method.
  ```java
  // Example of creating a thread by implementing the Runnable interface
  class MyRunnable implements Runnable {
      public void run() {
          // Code to be executed by the thread
          for (int i = 1; i <= 5; i++) {
              System.out.println("Thread B - Count: " + i);
              try {
                  Thread.sleep(1000); // Simulating some work
              } catch (InterruptedException e) {
                  e.printStackTrace();
              }
          }
      }
  }
  
  public class RunnableExample {
      public static void main(String[] args) {
          // Creating an instance of MyRunnable and passing it to a Thread
          Thread threadB = new Thread(new MyRunnable());
          threadB.start();
          
          // Code in the main method continues to execute concurrently with the thread
          for (int i = 1; i <= 5; i++) {
              System.out.println("Main Thread - Count: " + i);
              try {
                  Thread.sleep(500); // Simulating some work
              } catch (InterruptedException e) {
                  e.printStackTrace();
              }
          }
      }
  }

  ```

## Extending Thread vs. Implementing Runnable Interface

### Extending Thread Class:

* Simplifies thread creation by inheriting directly from the Thread class.
* May limit flexibility since Java supports single inheritance.

### Implementing Runnable Interface:

* Allows a class to extend another class while still being able to run as a thread.
* Encourages a separation of concerns, as the class can focus on its primary purpose.

## Thread States and Lifecycle

![image](https://github.com/dipjul/Courses-By-GPT/assets/20329508/8b116fa9-2610-4ac8-899e-83395790301e)

### Thread Lifecycle
* **Starting a Thread**:
  * Invoking the `start()` method initiates a new thread.
  * The JVM calls the `run()` method, and the thread transitions to the "Runnable" state.

* **Running a Thread**:
  * The thread is in the "Runnable" state, actively executing the code in its `run()` method.

* **Terminating a Thread**:
  * The `run()` method completes, or the thread's `stop()` method is called.
  * The thread enters the "Terminated" state.

### Thread States
* **New State**
  * Definition**: The initial state when a thread is created using the new keyword.
  * Characteristics**: The thread is not yet scheduled for execution.
  * Transition**: Moves to the "Runnable" state when start() is called.

* **Runnable State**
  * **Definition**: The thread is ready to run and waiting for CPU time.
  * **Characteristics**: The thread is in the ready-to-run queue.
  * **Transition**: Becomes eligible to run when the scheduler selects it.

* **Blocked State**
  * **Definition**: The thread is waiting for a monitor lock to enter a synchronized section.
  * **Characteristics**: Prevents multiple threads from executing the synchronized code simultaneously.
  * **Transition**: Moves to the "Runnable" state when the lock is acquired.

* **Waiting and Timed Waiting States**
  * **Waiting State**: The thread is waiting indefinitely for another thread to perform a particular action.
  * **Timed Waiting State**: The thread is waiting for a specified amount of time.
  * **Transition**: Move back to the "Runnable" state after the specified condition is met.

* **Terminated State**
  * **Definition**: The thread has completed its execution or has been explicitly stopped.
  * **Characteristics**: The thread is no longer active.
  * **Transition**: Irreversible; the thread cannot return to any previous states.

### Micro Quiz:
* What is the significance of the "New" state in the thread lifecycle?
* List and briefly explain the different states a thread can be in during its lifecycle.
* Why is it essential to understand the thread lifecycle in concurrent programming?

## Thread Synchronization
Thread synchronization is crucial in concurrent programming to ensure that multiple threads can safely access shared resources without causing data corruption or race conditions. 
This section provides insights into synchronization mechanisms using the synchronized keyword in Java.

### Synchronization Overview
* **Need for Synchronization**: Preventing data corruption and ensuring consistent results in a multi-threaded environment.
* **Critical Sections**: Identifying sections of code where only one thread should execute at a time.
* **Shared Resources**: Understanding the challenges when multiple threads access shared resources concurrently.

### Synchronized Methods
* **Definition**: Methods declared with the synchronized keyword.
* **Usage**: Ensures that only one thread can execute the synchronized method at a time. The entire method becomes a critical section, preventing interleaved execution.
* **Example**:
  ```java
  class SharedResource {
      private int count = 0;
  
      // Synchronized method
      public synchronized void increment() {
          for (int i = 0; i < 5; i++) {
              count++;
              System.out.println(Thread.currentThread().getName() + " - Count: " + count);
              try {
                  Thread.sleep(200); // Simulating some work
              } catch (InterruptedException e) {
                  e.printStackTrace();
              }
          }
      }
  }
  
  public class SynchronizedMethodsExample {
      public static void main(String[] args) {
          // Creating an instance of SharedResource
          SharedResource sharedResource = new SharedResource();
  
          // Creating multiple threads that share the same instance
          Thread threadA = new Thread(() -> sharedResource.increment(), "Thread A");
          Thread threadB = new Thread(() -> sharedResource.increment(), "Thread B");
  
          // Starting the threads
          threadA.start();
          threadB.start();
      }
  }

  ```
### Synchronized Blocks
* **Definition**: Blocks of code surrounded by the synchronized keyword.
* **Usage**: Allows synchronization of specific sections rather than entire methods. Allows more fine-grained control over synchronization.
* **Example**:
  ```java
  class SharedResource {
      private int count = 0;
      private Object lock = new Object(); // Object used for synchronization
  
      // Synchronized block
      public void increment() {
          synchronized (lock) {
              for (int i = 0; i < 5; i++) {
                  count++;
                  System.out.println(Thread.currentThread().getName() + " - Count: " + count);
                  try {
                      Thread.sleep(200); // Simulating some work
                  } catch (InterruptedException e) {
                      e.printStackTrace();
                  }
              }
          }
      }
  }
  
  public class SynchronizedBlocksExample {
      public static void main(String[] args) {
          // Creating an instance of SharedResource
          SharedResource sharedResource = new SharedResource();
  
          // Creating multiple threads that share the same instance
          Thread threadA = new Thread(() -> sharedResource.increment(), "Thread A");
          Thread threadB = new Thread(() -> sharedResource.increment(), "Thread B");
  
          // Starting the threads
          threadA.start();
          threadB.start();
      }
  }

  ```

In both examples, two threads (Thread A and Thread B) are concurrently incrementing a shared variable (count) within a synchronized context. This ensures that only one thread can execute the increment operation at a time, preventing race conditions.

### Micro Quiz:
* Why is synchronization necessary in concurrent programming?
* How does the synchronized keyword help in preventing race conditions?
* What is a critical section in the context of thread synchronization?

## Quiz:

* What are the two primary ways to create threads in Java?
* Explain the steps involved in creating a thread by extending the Thread class.
* Contrast the advantages of extending the Thread class with implementing the Runnable interface.
* Why is it recommended to use the Runnable interface for creating threads in Java?
* Describe the key phases in the lifecycle of a thread.
* What are the two ways to create a thread in Java?
* Briefly describe the lifecycle of a thread.
* Explain the role of synchronized methods in preventing thread interference.
* What advantages do synchronized blocks offer over synchronized methods?
