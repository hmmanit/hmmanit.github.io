---
layout: post
title:  Coroutines
date:   2020-05-17 10:05:55 +0700
image:  assets/images/blogs/kotlin_coroutines.webp
author: Man Ho
tags:   Android Development
---

## What is Coroutines

* Generally, there are two types of multitasking methods to manage multiple processes:
    * "Preemptive Multitasking": the OS manages the switching between
      processes
    * "Cooperative Multitasking": each process manages its own behavior
      it
* Coroutines is a software component that generates sub coroutines for
  Cooperative Multitasking
* Coroutines were first used in 1958 for assemblies
  language; Python, Javascript, C# also used coroutines in
  many years.
* In Kotlin, coroutines are introduced as a "Sequence of well
  managed sub tasks.” To some extent, coroutine can be seen as
  a lightweight Thread
* One thread can run multiple coroutines, one coroutine can also
  can be passed between threads, can suspend on one thread and
  resume in another thread.

## Why we need Coroutines

* All "painful tasks" are done using RxJava, AsyncTask
  or other methods like executors, HandlerThreads and
  IntentServices are all simply implemented using coroutines.
* Coroutines API also allows writing asynchronous code in one
  sequential manner.
* Avoid boilerplate code coming from callbacks, make the code easy
  readable and easy to maintain.

## Why we need asynchronous programming in android development?

* Most smartphones have a refresh frequency of at least 60Hz. 
  * This means the app will refresh 60 times per second (16,666ms for every refresh). 
  * Therefore, if we run an application that will draw on screen by main thread every 16.66s. At the same time, there are also smartphones with a refresh frequency of 90Hz or 120Hz 
  * Similarly, the application only needs 11.11ms and 8.33ms to perform a refresh on the main thread.
* By default, the android main thread will have a set of regulars responsibilities - it will always parse XML, inflate view components and draw them repeated every refresh
* Main thread must also listen for user interactions like click events
    * So, if we write too many tasks to handle on the main thread, if time
      its execution time exceeds the extremely small time between times
      refresh, the app will show performance errors, freeze the
      screen, unpredictable behaviours.
    * With technology, the refresh frequency will be higher and higher, we need to
      Deploy long-running asynchronous tasks in
      a separate thread. To achieve that, the newest, most effective way
      Currently Kotlin coroutines.
    
## Coroutines vs. Thread

* Coroutine and Thread are similar? - NO
* We have Main thread (aka UI Thread), in addition we
  there is another background worker thread, but the thread is not the same
  coroutine.
* Any thread can have multiple coroutines executed
  in the same time.
* Coroutines are just "separate processors" that run on a single thread, even
  Up to 100 coroutines can run at the same time.
* But by default, coroutines do not help us track them, or track
  the work done by them. So if we don't manage
  Be careful, they can lead to memory leaks.
* In Kotlin coroutines, we have to run all coroutines in
  a scope, using properties throughout the scope, we can easily
  Easily monitor coroutines, cancel and handle errors or exceptions
  thrown out by them.

[comment]: <> (### What exactly does concurrency mean?)

[comment]: <> (### How is concurrency related to parallelism?)

[comment]: <> (### What about threads? Why should we consider using coroutines if we have threads? What are the benefits?)

[comment]: <> (### How threads work at a very low level inside our CPU?)

#### Threads vs cores

* Imagine 4-cores CPU as a factory, each core corresponds to
  a worker. In this context, we have 4 workers, representing cores
  processor's own. Normally, the whole process is controlled by
  Boss - OS who will give commands to the worker.
* Threads are like sequences of commands sent to the CPU core -
  threads transfers the task to the CPU core.
* When workers work, OS will manage all threads and pay attention to
  schedule, as we know, OS is very expensive, like threads, both are
  costly and requires a lot of resources. Basically, each thread in
  The JVM takes up about 1MB of memory.

#### Physical vs logical core

* A physical core is a hardware part of the CPU, it is simply the
  transistor inside CPU
* A logical core is like a piece of code, it exists in the machine
  count. The number of cores is the number of threads that can execute in
  in the same time.
    * For example, we have 4 CPU cores, but 4 threads can execute at the same time
      At a time, we have 4 physical cores and 4 logical cores.

Imagine, we have 2 working lines, worker is working on line 1, but
line 1 has a problem and cannot continue to work. Meanwhile, line 2
is ready to go, instead of standing at line 1 and waiting for it to be available
can work again, worker can switch to line 2 and continue working
work, at the same time can watch line 1 work again and come back
work on line 1. That will increase work efficiency.

### Kotlin coroutines are not managed by the OS

* Kotlin coroutines are language features. OS doesn't need to care about coroutine or
  plan for it. Coroutines will manage itself by cooperative
  multitasking.
* At the time the coroutine suspends, the Kotlin runtime will look for the coroutine
  another to continue the execution. This is like everything
  OS to do previously can be done by a supervisor with
  much cheaper cost.
* Coroutines are not like threads, it doesn't take a lot of memory, just a few
  bytes for each coroutine. Therefore, we can run a lot at the same time
  work at a very low cost.

### Comparing

* Thread is very limited, we know Thread Pool, it will limit the number of Threads at
  for a time, and coroutines are almost free, we can start
  running thousands of coroutines at the same time. It allows to run asynchronously
  in a synchronous way of writing code.

### Blocking and Non-blocking

* Blocking and Non-blocking are ways of describing how to execute an order
  of a program
    * Blocking is the command lines are executed sequentially,
      when a command line in front is not completed, the line in front
      later will not be able to execute, so if that command line executes
      cooperate with IO, networking, it will itself become an obstacle and block
      rear processing.
    * Non-blocking means that the command lines are not necessarily always
      is performed sequentially. If the command line behind doesn't need the result from
      previous statements, it can be executed immediately after
      when the preceding command line is called (Asynchronous), accompanied by each
      dongf command we will have a callback, callback is the code that will be executed
      execute after the results returned from the asynchronous command line.
    * Example: `launch { delay(1000L) println("World!") } print("Hello,")
      Thread.sleep(2000L)`
        * Here, Thread.sleep() is a blocking, delay() is a
          non-blocking. Thread.sleep() will put the thread to sleep completely,
          while delay() just pauses it, allowing the rest to work
          normal movement.
    * Coroutines are computations that can be suspended without blocking a
      stream.

## Coroutines vs. Callbacks

* In the past, to use time-consuming tasks, most of us
  use callbacks, this way the task will be run under
  background thread, when the task is finished, return the result to main
  thread.

* Use coroutines to remove callbacks:
    * Callbacks are a good way, but the code will be heavy and hard to read, hard
      debug.
    * Kotlin coroutines will convert callback structure into sequential code,
      easier to read.
    * Both callbacks and coroutines give the same result
    * Keyword suspend to mark function as coroutine

* Between callback and coroutine, coroutine's code will be more readable,
  shorter and easier to understand despite the same results. And if you want more
  task runs better, just keep writing, no need to create much
  callbacks.

## New concept

### CoroutineScope

* CoroutineScope is an interface that we will use to provide a scope for coroutines.
* In Kotlin coroutines, we also have another scope
  GlobalScope. GlobalScope used for running top-levels
  coroutines - which are operating on the whole application lifetime.
* In android development we rarely use GlobalScope
* Both of these scopes are described as a reference for the coroutine context

### Context - Dispatchers

* Dispatcher describes a type of thread where coroutines will be run
* In Kotlin Android structured concurrency, it is always recommended
  recommend using main thread then move to background thread
    * Dispatchers.Main: coroutines will be run on the main thread (UI
      thread), we only use main dispatcher for small tasks,
      lightweight and has an impact on the UI such as: calling a UI function, calling a
      suspending function to get updated data from LiveData.
      In structured concurrency, the best advice is to launch
      coroutines on the main thread and then switch to the background thread.
    * Dispatchers.IO: coroutines will be run in the background thread "from a
      shared pool of on-demand created threads.” We will use IO
      dispatcher to work with the local database, communicate with the network and
      work with files.
    * Dispatchers.Default: used for CPU intensive tasks like about
      sort a large list, parse a huge JSON file,...
    * Dispatchers.Unconfined: is a dispatcher used with
      GlobalScope, if we use Unconfined, coroutines will be run
      on the current thread, but if they are suspended or resumed,
      it will run on the thread that has the suspending function running.
      This Dispatcher is not recommended for Android use
      Development.
* In addition to these 4 dispatchers, the coroutines API also facilitates them
  we convert from "executors" to "dispatchers", as well as create
  custom dispatcher.
* To summarize, in Android development, the most commonly used are Main and
  IO Dispatcher.

### Coroutines Builder

[https://github.com/Kotlin/kotlinx.coroutines/tree/master/kotlinx-coroutines-core](https://github.com/Kotlin/kotlinx.coroutines/tree/master/kotlinx-coroutines-core)

* Coroutine builder is an extension function of coroutine scopes,
  We have 4 main builders:
    * launch: will run a new coroutine without blocking the current thread,
      This builder will return an instance of Job, which can be used
      used as a reference for coroutine. We can use
      this instance to monitor the coroutine's activity and cancel it.
      We use launch builder for coroutines with no price
      return value. This builder returns a Job instance but no value
      value "return", we cannot use this coroutine to calculate
      and return the final result.
    * async: if we want to get the result returned, we should use async
      builder, the main thing about async builder is to allow coroutines to run
      Parallelly, the async builder also won't block the current thread
      (same as launch builder). This builder will return an instance of
      Deferred. In fact, the Deferred interface is an extension of Job
      interface, so we can use it as a Job and cancel
      coroutine. If our return is a String value. to
      To get data from a deferred object, we must call await()
      function, async is also one of the most commonly used builder
      most variable.
    * produce: used for coroutines that create a stream of
      elements. This builder returns an instance of ReceiveChannel.
    * runblocking: In Android Development we use
      runblocking is mainly for testing, this builder will block the thread for
      until it is done executing, this builder returns type T.

#### Job

* Holds the information of the coroutine, the job provides methods like
  cancel(), join()
    * cancel(): cancel coroutine, the special thing here is that the cancel function is only
      reset property isActive = false, but coroutine continues
      run, there are 2 ways to actually stop the coroutine that was called to cancel:
        * check the isActive property before performing the action
        * call any suspending function before executing the task,
          suspending function has the ability to check if the coroutine is still active
          no, otherwise it won't execute the following lines.
    * join(): when we call join, the coroutine must finish running
      new program continues
    * finally block: if coroutine is canceled, it will look for finally block
      to run. We can take advantage of this feature to close all resources first
      when the coroutine is canceled. Going back to the example with cancel(), if we set 1
      suspending function (example with delay()) in the finally block,
      coroutine will stop right here without continuing the following lines.
    * NonCancellable coroutine: with the withContext suspending function, we have
      You can pass the context as NonCancellable to make it run even if it's already
      cancel or be checked with a suspending function.

#### Deferred

* Deferred is a non-blocking, can be canceled if requested, about
  it also basically represents the Job coroutine, which contains the value for a
  respective work.
* Using Deferred allows us to combine

## Switch the thread of a coroutine



### Suspending functions

* In Kotlin coroutines, whenever a coroutine is suspended, the
  current thread will stack frame of the function is copied and saved in
  the memory.
* When the function resumes after completing its task, the stack frame
  is copied back from where it was saved and starts running again.
* Kotlin coroutines API provides a lot of functions to help us work
  with it simpler:
    * withContext():
    * withTimeout():
    * withTimeoutOrNull():
    * join():
    * delay():
    * await():
    * supervisorScope:
    * coroutineScope:
    * and more...
    * Some other libraries such as Room or Retrofit also provide these
      suspending functions to support working with coroutines
* Notes:
    * A suspending function can only be called within a suspending
      function
    * Suspending function is used as a "label" for heavy functions
      and take a long time to run.
    * Coroutine can call both suspending functions and regular functions
    * Suspending function does not block thread

## Async & Await

* Example:
    * Task 1: 10s
    * Task 2: 15s
    * Task 3: 8s
    * Task 4: 12s
        * With synchronous code, we will have to wait at least 10 + 15 + 8 +
          12 = 45s to get the final result
        * But with asynchronous, we will only have to wait about 15 seconds for it
          results can be obtained.

* Decomposition Parallel, usually, to write it will be very complicated
  complicated, difficult to write, difficult to read, difficult to maintain
* But with Kotlin coroutines, we can do it simply
  simple.

## Unstructured Concurrency vs. Structured Concurrency

* In case we want to run multiple coroutines at the same time in one
  suspending function and get the result, there are 2 ways to do this
  there we call Structured Concurrency and Unstructured Concurrency

### Unstructured Concurrency

* This is the wrong application --- Demo StructuredConcurrency (Un)---
* Unstructured Concurrency will not guarantee completion of all your tasks
  suspending function before returning.
* Here, in fact, child coroutines (delay 1000) are still running, even
  after the parent coroutine has completed (setText), the result is
  unexpected bugs, that's with the launch . use case
  builder as demo.
* With async builder and using await function can get result
  The result is as expected, at first glance it seems to work properly, but here
  problem still occurs. In Android, if an error occurs in
  function, it will throw exception, so we can catch exception
  in the functions that call it and handle it. In Unstructured Concurrency, though
  using launch or async builder, we can't handle it
  exception properly. So although it may run properly in some
  case, but should not actually be used.

### Structured Concurrency

<!--* Set of language features and best practices introduced for Kotlin coroutines to avoid coroutines leak and manage coroutines productively-->
* All problems arising in Unstructured Concurrency are possible
  easily solved with coroutineScope function, pay attention here
  coroutineScope is different from CoroutineScope.
    * CoroutineScope is an interface.
    * coroutineScope is a suspending function that allows us to create
      child scopes within a certain coroutine scope, coroutine
      This scope ensures the completion of tasks when suspending function
      return results.

* When using coroutineScope, it will guarantee completion of all tasks
  in the child scope provided by it before return (here
  launch and async).
* In the previous example, the result we get is 70, because the problem occurs
  with unstructured concurrency.
* In this example, the result should be 120.

* This example is the best recommended practice, when we have many
  coroutines, we should always start Dispatcher.Main, with
  CoroutineScope interface, and inside suspending function, we should use
  Use the coroutineScope function to provide child scope.
* Notes:
    * Structured Concurrency will ensure completion of all running tasks
      by coroutines inside child scope before suspending function
      return. In fact, in the coroutineScope, it waits for the child coroutines to complete
      not only that, it also has another benefit. When errors occur,
      exception is thrown, structured concurrency is also guaranteed
      notify the caller function. So we can easily handle,
      we can also use structured concurrency to cancel them
      necessary.
    * If we cancel the entire child scope, everything happens inside
      in it are all cancelled.
    * You can also cancel the coroutine independently.

### Exception in Coroutines

* With launch:
    * When throw Exception, coroutine will stop and throw exception
* With async:
    * The difference is, the coroutine still stops, but the exception doesn't work
      throw out
    * Because the exception is encapsulated into Deferred, only if we await()
      then it is thrown out.

* But if we run 100 coroutines at the same time, how can we catch them all
  exception?

#### CoroutineExceptionHandler

* CoroutineExceptionHandler is used as a generic catch block of
  all coroutines.
* Exception will be caught and returned for a callback function that is override
  handleException(context: CoroutineContext, exception: Throwable)
* Notes:
    * CoroutineExceptionHandler could not catch the closed exception
      wrapping into Deferred, and coroutine in the runBlocking block, so we
      have to catch yourself

* As said, when an exception is encountered, the coroutine will look for the code in the block
  finally to run, so if the code in finally also throws an exception, via
  Usually, the first exception encountered will be thrown, this time the exception
  in the finally block will be suppressed, to print all we can
  call exception.getSuppressed()
    * Example: Caught java.io.IOException with suppressed
      [java.lang.ArithmeticException, java.lang.IndexOutOfBoundsException]

### SupervisorJob

* Normally, when a child coroutine occurs Exception, all
  Other child coroutines will also be stopped. If you want a coroutine can you happen
  Exception, the other child coroutines still work normally, ta
  can use SupervisorJob instead of Job
* When SupervisorJob cancels, all its children will be canceled
* In addition, we also have supervisorScope, its effect is similar to
  SupervisorJob

### viewModelScope

* Following Android Architecture Component - MVVM Architecture
* Use viewModelScope in ViewModel:
    * This makes it possible for any coroutines running in this scope
      is automatically destroyed when ViewModel isCleared without override
      onCleared()
    * This is also convenient when we want to complete coroutines only if
      ViewModel works.
    
### lifecycleScope

* Google also introduced a handy scope called
  lifecycleScope, one lifecycleScope is defined for each Lifecycle
  object
* Any coroutines running in this scope will cancel when
  Lifecycle destroyed
* Sometimes we need to create coroutines in objects with a lifecycle,
  like activities or fragments
* All coroutines will be canceled at onDestroy (Activity and Fragment)
* Here, we have 3 new builders:
    * launchWhenCreated: when there are long running tasks that only happen in
      lifecycle of activity or fragment, this coroutine will run when
      activity or fragment created for the first time
    * launchWhenStarted: this coroutine will run when activity or fragment
      started
    * launchWhenResumed: run coroutine as soon as the app is up and running
    
### Live Data Builder

* this block will automatically execute when live data is active, it's automatic
  decide when to stop and cancel coroutines inside it based on
  lifecycle owner.
* Inside Live Data building block, we can use emit() function
  to set value for LiveData