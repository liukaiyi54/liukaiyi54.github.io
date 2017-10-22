---
layout: post
title: Understanding Multitasking Through Terms
date: 2016-08-21
author: Kai
---

> There are important terms that we have to figure out to understand the almighty multitasking better!!

### Serial vs. Concurrent
These two terms describe how the tasks are executed. Tasks executed **serially** are always executed only one at a time while tasks executed **concurrently** might be executed at the same time.

### Synchronous vs. Asynchronous
A **synchronous** function returns only after the completion of a task, but an **asynchronous** function does not wait for the task to be done and it returns immediately. So the major difference between these two is a **synchronous** function blocks the current thread of execution from processing on to the next one, but an **asynchronous** function does not.


### Concurrency vs Parallelism
> A major reason why I write this in English is that it's much easier to tell the difference between these two terms, while in Chinese translation it might not be that clear.

A multi-core device can execute multiple threads at the same time via **parallelism**, but a single-core device can only achieve this by performing a context switch, which gives you the illusion that it also doing a parallel execution.

So the picture below clearly shows that **concurrency** only makes it **FEEL** like we're executing multiple threads at the same time, while in fact it's just constantly doing the so-called "context switch" process. On the other hand, we can see that **parallelism** is the real multi-tasking, let's hear the applause.
![](https://koenig-media.raywenderlich.com/uploads/2014/01/Concurrency_vs_Parallelism.png)


### Deadlock
Two or more threads are deadlocked if they all get stuck waiting for each other to complete their action. The first can't finish its action because it's waiting for the second to finish, and the second can't finish because it's waiting for the first to finish. And now we're deadlocked.

### Thread safe
Thread safe code can be safely called from multiple threads without causing any problems. Meanwhile, code that is not thread safe must be run in one thread at a time.

### Queues
> Meaning GCD queues here.

GCD provides **dispatch queues** to handle blocks of code. Like all queues, queues in GCD also take FIFO order, which guarantee that first task added to the queue is the first task that'll start in the queue.

There are **serial queues** and **concurrent queues**. Tasks in **serial queues** execute one at a time, and the second won't be executed until the previous one gets finished.

Tasks in **concurrent queues** are guaranteed to start in the order they were added and that's the only one thing that can be guaranteed. We have no idea in what order they'll finish, and we don't know when will the next given block start, nor how many blocks are running at any moment.

#### Types
The system provides a serial queue known as the main queue, which can be accessed by `dispatch_get_main_queue`. We already know that tasks in serial queue execute one at a time. But the important thing is this queue guarantees that all tasks will execute on the main thread, **which is the only thread you are allowed to update your UI.**

The system also provides some concurrent queues, knowns as the **global dispatch queues**, which can be accessed by `dispatch_get_global_queue`. This queue has four priorities: background, low, default, and high.

Other than these, you're able to create your own custom serial or concurrent queues. `dispatch_queue_create` at your service any time!

------

I have tried my best to explain all these terms in my own way. Hope that you have a better understanding about all these after reading. Thx!!


