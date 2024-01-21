# Bonus Section: Dive into Java Threads

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

## Thread Lifecycle

![image](https://github.com/dipjul/Courses-By-GPT/assets/20329508/8b116fa9-2610-4ac8-899e-83395790301e)


### Starting a Thread:

* Invoking the `start()` method initiates a new thread.
* The JVM calls the `run()` method, and the thread transitions to the "Runnable" state.

### Running a Thread:

* The thread is in the "Runnable" state, actively executing the code in its `run()` method.

### Terminating a Thread:

* The `run()` method completes, or the thread's `stop()` method is called.
* The thread enters the "Terminated" state.

## Quiz:

* What are the two primary ways to create threads in Java?
* Explain the steps involved in creating a thread by extending the Thread class.
* Contrast the advantages of extending the Thread class with implementing the Runnable interface.
* Why is it recommended to use the Runnable interface for creating threads in Java?
* Describe the key phases in the lifecycle of a thread.
* What are the two ways to create a thread in Java?
* Briefly describe the lifecycle of a thread.
