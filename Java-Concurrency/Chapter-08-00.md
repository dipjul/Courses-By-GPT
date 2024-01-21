# Asynchronous Programming with CompletableFuture

## Overview of CompletableFuture

**Introduction:** `CompletableFuture` is a class introduced in Java to facilitate asynchronous programming by providing a powerful and flexible way to handle asynchronous computations. It is part of the `java.util.concurrent` package.

**Key Features:**

1.  **Asynchronous Execution:**
    
    *   `CompletableFuture` allows the execution of tasks asynchronously, enabling non-blocking operations.
2.  **Composition and Chaining:**
    
    *   Multiple `CompletableFuture` instances can be composed and chained together to create complex asynchronous workflows.
3.  **Callback Mechanism:**
    
    *   It supports a callback mechanism, allowing you to specify actions to be performed when a `CompletableFuture` completes, either normally or exceptionally.

## Chaining and Combining CompletableFutures

**Chaining `CompletableFuture` Instances:**

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> "Hello")
    .thenApplyAsync(result -> result + " World")
    .thenApply(String::toUpperCase);

String finalResult = future.join();
System.out.println(finalResult);

```
In this example, we start with a `CompletableFuture` that supplies the initial value "Hello" asynchronously. We then chain two `thenApplyAsync` operations to transform the result by appending " World" and converting it to uppercase.

**Combining `CompletableFuture` Instances:**

```java
CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "Hello");
CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> " World");

CompletableFuture<String> combinedFuture = future1.thenCombine(future2, (result1, result2) -> result1 + result2);

String finalResult = combinedFuture.join();
System.out.println(finalResult);

```
In this example, we have two independent `CompletableFuture` instances, and we use `thenCombine` to combine their results when both are completed. The combining function concatenates the results.

## Exception Handling in CompletableFutures

**Handling Exceptions:**

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    // Simulating an exception
    throw new RuntimeException("Exception in CompletableFuture");
});

CompletableFuture<String> exceptionHandledFuture = future.exceptionally(ex -> "Handled: " + ex.getMessage());

String result = exceptionHandledFuture.join();
System.out.println(result);

```
In this example, if an exception occurs during the execution of the original `CompletableFuture`, the `exceptionally` method is used to handle the exception and provide a fallback value.

**Handling Multiple CompletableFutures:**

```java
CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "Hello");
CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> {
    // Simulating an exception
    throw new RuntimeException("Exception in CompletableFuture");
});

CompletableFuture<String> result = future1.exceptionally(ex -> "Handled: " + ex.getMessage())
        .thenCombine(future2.exceptionally(ex -> "Handled: " + ex.getMessage()),
                (result1, result2) -> result1 + " " + result2);

String finalResult = result.join();
System.out.println(finalResult);

```
In this example, we handle exceptions independently for two `CompletableFuture` instances and then combine their results. If an exception occurs in either future, the exception handling logic will be applied.

> These examples showcase the capabilities of `CompletableFuture` in asynchronous programming, including chaining, combining, and handling exceptions.
> The flexibility provided by `CompletableFuture` makes it a powerful tool for building responsive and efficient asynchronous systems in Java.

Certainly! Here are some quiz questions to test your understanding of `CompletableFuture` in asynchronous programming:

## Quiz:

1.  **CompletableFuture Overview:**
    
    *   What is `CompletableFuture` in Java, and what purpose does it serve in asynchronous programming?
    *   Name one key feature of `CompletableFuture` that facilitates asynchronous execution.
2.  **Chaining CompletableFutures:**
    
    *   Explain how the `thenApplyAsync` method is used for chaining `CompletableFuture` instances.
    *   In the context of `CompletableFuture` chaining, what is the purpose of the `thenApply` method?
3.  **Combining CompletableFutures:**
    
    *   How does the `thenCombine` method work in combining the results of two `CompletableFuture` instances?
    *   Provide an example scenario where combining `CompletableFuture` instances is useful.
4.  **Exception Handling:**
    
    *   How is an exception handled using the `exceptionally` method in a `CompletableFuture`?
    *   Why might it be important to handle exceptions separately for each `CompletableFuture` before combining them?

**Bonus Questions:**

5.  **Parallel Execution:**
    
    *   How can you achieve parallel execution of multiple `CompletableFuture` instances?
    *   Describe a situation where parallel execution of `CompletableFuture` instances is beneficial.
6.  **Callback Mechanism:**
    
    *   Explain how the callback mechanism is utilized in `CompletableFuture` to perform actions upon completion.
    *   Provide an example scenario where the callback mechanism is useful.
7.  **CompletableFuture vs. Future:**
    
    *   Differentiate between `CompletableFuture` and the traditional `Future` interface in Java.
