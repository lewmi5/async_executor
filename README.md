# Asynchronous Executor in C
This project implements a simplified single-threaded asynchronous executor in C, inspired by Rust's Tokio. It uses Linux's epoll mechanism for efficient, event-driven task management without multithreading.

## Overview
Purpose:
Manage concurrent tasks (I/O operations, computations) using cooperative multitasking, ideal for resource-constrained systems and high-concurrency applications.

## Key Features:
 - Single-threaded asynchronous execution
 - Epoll-based I/O event monitoring
 - Future combinators (Then, Join, Select) for composing tasks

## Basic Future
### Definition:
A Future represents a single asynchronous operation. It encapsulates both the operation (via a pointer to a progress function) and any state needed between execution steps.

### Behavior:
Progress Function: The executor repeatedly calls the Future’s progress() method.  
States:  
COMPLETED: The operation finished successfully.  
FAILURE: The operation encountered an error.  
PENDING: The operation isn’t complete and may register a wake-up callback (Waker) to be resumed later.  
Execution: It runs within the single-threaded event loop, yielding control when waiting for I/O or other events.  



## ThenFuture
### Definition:
A combinator that chains two Futures together.  

### Behavior:  

Sequential Execution:  
First Future: It begins by executing the first Future.  
Second Future: Once the first Future completes successfully, its result is passed to start the second Future.  
Error Handling: If the first Future fails, the entire ThenFuture fails.  
Use Case: Useful when a task’s outcome is needed to decide or parameterize the subsequent operation.  


## JoinFuture
### Definition:  
A combinator that runs two Futures concurrently.  

### Behavior:  

Concurrent Execution: Both Futures are executed in parallel (within the cooperative single-threaded model).  
Completion Criteria: The JoinFuture completes only when both underlying Futures have finished executing.  
Result Aggregation: It may combine the results (often as a pair) from the two Futures.  
Use Case: Suitable when two independent tasks must complete before moving on.  

## SelectFuture
### Definition:
A combinator that runs two Futures concurrently and completes as soon as one finishes successfully.

### Behavior:

Concurrent Execution: Both Futures are started at the same time.  
Early Completion: The SelectFuture finishes when either Future returns a COMPLETED state.  
Cancellation: Once one Future completes successfully, the other is abandoned (i.e., its progress is no longer called).  
Failure Handling: If both Futures fail, then the SelectFuture is considered to have failed.  
Use Case: Ideal for scenarios where you need the fastest successful result, such as racing two different sources of data.  
