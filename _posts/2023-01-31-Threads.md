---
title: Threads
date: 2023-01-31 22:46:00 +0900
categories: [OS]
tags: [os, system]
---

# Threads

## Overview

### Motivation

Most modern applications are multithreaded

Threads run within application

Multiple tasks with the application can be implemented by separate threads

Process creation is heavy, thread creation is light

Can simplify code, increase efficiency

Kernels are generally multithreaded

### Multithreaded Server Architecture

Client request to server

server create new thread to service the request

server resume listening for additional client requests

### Benefits

- Responsiveness - allow continued execution
- Resource Sharing - threads share resources of process, easier than shared memory or message passing
- Economy - cheaper than process creation, lower overhead for context switching
- Scalability - process can take advantage of multiprocessor architectures

## Multicore Programming

**Multicore** or **multiprocessor** systems putting pressure on programmers, challenges include:

- Dividing activities
- Balance
- Data splitting
- Data dependency
- Testing and debugging

**Parallelism** implies a system can perform more than one task simultaneously

**Concurrency** supports more than one task making process

- Single processor/core, scheduler providing concurrency

Types of parallelism

- **Data parallelism** - distributes subsets of the same data across multiple cores, same operation on each
- **Task parallelism** - distributing threads across cores, each thread performing unique operation

As number of threads grows, so does architectural support for threading

- CPUs have cores as well as hardware threads

### Concurrency vs. Parallelism

- Concurrent execution on single-core system:
    
    T1 T2 T3 T4 T1 T2 T3 T4 …
    
- Parallelism on a multi-core system:
    
    core 1 : T1 T3 T1 T3 …
    
    core 2 : T2 T4 T2 T4 …
    

### Single and Multithreaded Processes

- Single threaded process
    
    Process - code, data, files | registers, stack | thread
    
- Multithreaded process
    
    Process - code, data, files
    
    - registers, stack → for each thread
    - registers, stack
    - …

### Amdahl’s Law

Identifies performance gains from adding additional cores to an application that has both serial and parallel components

S is serial portion

N is processing cores

speedup ≤ 1 / { S + (1-S)/N }

That is, if application is 75% parallel and 25% serial, moving from 1 to 2 cores results in speedup of 1.6 times

As N approaches infinity, speedup approaches 1 / S

Serial protion of an application has disproportionate effect on performance gained by adding additional cores

But does the law take into account contemporary multicore systems?

### User Threads and Kernel Threads

**User threads** - management done by user-level threads library

Three primary thread libraries:

- POSIX Pthreads
- Windows threads
- Java threads

**Kerner threads** - supported by the kernel

Examples - virtually all general purpose operating systems, including:

- Windows
- Solaris
- Linux
- Tru64 UNIX
- Mac OS X

## Multithreading Models

### Many-to-One

Many user-level threads mapped to single kernel thread

One thread blocking causes all to block

Multiple threads may not run in parallel on multicore system because only one may be in kernel at a time

Few systems currently use this model

Examples:

- Solaris Green Threads
- GNU Protable Threads

### One-to-One

Each user-level thread maps to kernel thread

Creating a user-level thread creates a kernel thread

More concurrency than many-to-one

Number of threads per process sometimes restricted due to overhead

Examples:

- Windows
- Linux
- Solaris 9 and later

### Many-to-Many

Allows many user-level threads to be mapped to many kernel threads

Allows the operating system to create a sufficient number of kernel threads

number of kernel threads < number of user threads

Examples:

- Solaris prior to version 9
- Windows with the *ThreadFiber* package

### Two-level Model

Similar to M:M, except that it allows a user thread to be **bound** to kernel thread (using both O:O and M:M)

Examples:

- IRIX
- HP-UX
- Tru64 UNIX
- Solaris 8 and earlier

## Thread Libraries

Provides programmer with API for creating and managing threads

Two primary ways of implementing

- Library entirely in user space
- Kernel-level library supported by the OS

### Pthreads

May be provided either as user-level or kernel-level

A POSIX standard (IEEE 1003.1c) API for thread creation and synchronization

*Specification*, not *implementation*

API specifies behavior of the thread library, implementation is up to development of the library

common in UNIX operating systems (Solaris, Linux, Mac OS X)

### Java Threads

Java threads are managed by the JVM

Typically implemented using the threads model provided by underlying OS

Java threads may be created by:

```java
public interface Runnable
{
	public abstract void run();
}
```

- Extending Thread class
- Implementing the Runnable interface

## Implicit Threading

Growing in popularity as numbers of threads increase, program correctness more difficult with explicit threads

Creation and management of threads done by compilers and run-time libraries rather than programmers

Three methods explored

- Thread Pools
- OpenMP
- Grand Central Dispatch

### Thread Pools

Create a number of threads in a pool where they await work

Advantages:

- Usually slightly faster to service a request with an exesting thread than create a new thread
- Allows the number of threads in the application(s) to be bound to the size of the pool
- Seperating task to be performed from mechanics of creating task allows different strategies for running task
    - Tasks could be scheduled to run periodically
- Windows API supprots thread pools

### OpenMP

Set of compiler directives and an API for C, C++, FORTRAN

Provides support for parallel programming in shared-memory environments

Identifies **parallel regions** - blocks of code that can run in parallel

### Grand Central Dispatch

Apple technology for Mac OS X and iOS operating systems

Extensions to C, C++ languages, API, and run-time library

Allows identification of parallel sections

Manages most of the details of threading

Block is in “^{ }”

Blocks placed in dispatch queue

- Assigned to available thread in thread pool when removed from queue

Two types of dispatch queues:

- serial - blocks removed in FIFO order, queue is per process, called **main queue**
    - Programmers can create additional serial queues within program
- concurrent - removed in FIFO order but several may be removed at a time
    - Three system wide queues with priorities low, default, high

## Threading Issues

### Semantics of fork() and exec() system calls

Does form() duplicate only the calling thread or all threads?

- some UNIXes have two versions of fork

exec() usually works as normal - replace the running process including all threads

### Signal handling

Signals are used in UNIX systems to notify a process that a particular event has occurred.

A signal handler is used to process signals

- Signal is generated by particular event
- Signal is delivered to a process
- Signal is handled by one of two signal handlers:
    - default
    - user-defined

Every signal has default handler that kernel runs when handling signal

- User defined signal handler can override default
- For single-threaded, signal delivered to process

Where should a signal be delivered for multi-threaded?

- to which the signal applies
- to every thread in the process
- to certain threads in the process
- Assign a specific thread to receive all signals for the process

### Thread Cancellation

Terminating a thread before it has finished

Thread to be canceled is **target thread**

Two general approaches:

- Asynchronous cancellation terminates the target thread immediately
- Deferred cancellation allows the target thread to periodically check if it should be cancelled

Invoking thread cancellation requests cancellation, but actual cancellation depends on thread state

If thread has cancellation disabled, cancellation remains pending until thread enables it

Default type if deferred

- cancellation only occurs when thread reaches cancellation point
    - Then cleanup handler is invoked

On Linux systems, thread cancellation is handled through signals

### Thread-local storage

Thread-local storage (TLS) allows each thread to have its own copy of data

Useful when you do not have control over the thread creation process (i.e., when using a thread pool)

Different from local variables

- Local variables visible only during single function invocation
- TLS visible across function invocations

Similar to static data

- TLS is unique to each thread

### Scheduler Activations

Both M:M and Two-level models require communication to maintain the appropriate number of kernel threads allocated to the application

Typically use an intermediate data structure between user and kernel threads - lightweight process (LWP)

- Appears to be a virtual processor on which process can schedule user thread to run
- Each LWP attached to kernel thread
- How many LWPs to create?

Scheduler activations provide **upcalls** - a communication mechanism from the kernel to the **upcall handler** in the thread library

This communication allows an application to maintain the correct number kernel threads

## Operating System Examples

### Windows Threads

### Linux Threads