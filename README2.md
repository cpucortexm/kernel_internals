## Concurrency vs Parallelism
Concurrency is running multiple tasks on the single CPU core with the help of software called scheduler.

Parallelism simply means having two tasks run at the same time, or in parallel. This is possible on CPU with multiple cores.

To have true parallelism you need multiple cores which can run each task on a separate core, while to have concurrent execution with multiple tasks running on the single CPU core you need to have a scheduler which helps each task get a some amount of CPU time before a context switch happens.


## Task Scheduler (scheduling strategy)
The task scheduler itself is a piece of program that needs a slice of the CPU core to be executed. So, how does it manage different tasks in order to use the CPU core when another task is using it? Let's suppose that the task scheduler itself is using the CPU core. Firstly, it selects a task from its queue before it sets a timer for a timer interrupt to happen, and it then leaves the CPU
core and gives its resources over to the selected task.
Now that we have assumed that the task scheduler will give each task a certain amount of time, there is a time that the interrupt will act, and the CPU core stops
the execution of the current task and immediately loads the task scheduler back into the CPU. Now, the scheduler stores the latest status of the previous task and loads the next one from the queue. All of this goes on until the kernel is up and running. Regarding a machine having a CPU with multiple cores, this can change, and the kernel can use various cores while scheduling the tasks for other cores.

Scheduling strategy can be one of either priority preemptive or cooperative.
***Priority Preemptive***:
If the scheduling strategy is preemptive, the scheduler should be able to forcefully take back the CPU core from the running task in order to give it to the next task. This is called preemptive scheduling.
Preemptive scheduling algorithms try to share time slices evenly and fairly between different tasks.The time slice sharing algorithms can be ***round-robbin*** only without priority and also could be with priority depending on the kernel. In linux we use priority based preemptive scheduling.


Prioritized tasks may get chosen more frequently, or they may even get longer time slices depending upon the implementation of the scheduler.


***Cooperative***:
The task releases the CPU voluntarily, which is called
cooperative scheduling. The task is cooperating to give the CPU back.There is no forcibly taking back the CPU.



## Context switch for process and threads
Every time a preemptive scheduler stops a process in the middle of running and puts another process into the running state, it is said that a context switch has occurred.
The faster the context switches are, the more a user will feel as if the tasks are being run in parallel.



## Atomic instruction
An atomic instruction cannot be broken down into smaller operations and it cannot be interrupted by context switches.


## Synchronisation techniques
1. Busy Wait
Busy-waiting is not an efficient approach to wait for an event to happen, but it is a simple one. Since inside a busy-wait nothing special can be done by a task, it wastes its given time slice completely. Busy-waits are avoided in long waits. That wasted time of CPU could be granted to some other task in order to complete a portion of its job. However, in some circumstances in which the waiting times are expected to be short, busy-waits are used.
*Implementing busy wait*:
~~~
Concurrent System {
Shared State {
Done : Boolean = False
}
Task P {
1.1. print 'A'
1.2. Done = True
}
Task Q {
2.1. While(!Done){ /* busy wait and do nothing until Done is set to True*/}
2.2. print 'B'
}

}
~~~
2. Sleep/Notify:
In this case the task is put to sleep when the condition is not met, hence the scheduler will put this task in the wait queue which results in scheduler not giving CPU time to the task. As soon as the condition is met the task is awakened with signalling or notifying. This is an advantage over busy wait as there is no CPU wastage
*Implementing Sleep/Notify*
Concurrent System {
Shared State {
Done : Boolean = False
}
Task P {
1.1. print 'A'
1.2. Done = True
1.3. Notify Task Q   // Scheduler will trigger task Q to wake up
}
Task Q {
2.1. While ! Done {// As soon as task Q wakes up after task P signals, Done is set to true
2.2.
 Go To Sleep Mode If Done is False (Atomic)
2.3. }
2.4. print 'B'
}
}


## Critical Section
***Critical Section*** contains the portion of the code which needs to be protected to avoid concurrent access by two or more threads.
e.g. shared memory, updating some device (GPIO) registers etc


## Memory Ordering and Barriers
In general the compilers have the tendency to re-order instructions for better performance or performance gain. However this can result in corrupted or undesired results.
Memory barriers are instructions which help resolve this as they do not allow reordering of instructions and hence read/write or load/store happens in the order the user expects.
All of Linux's locking primitives, including spinlocks, reader-writer locks, semaphores and read-copy updates (RCUs), include any needed barrier primitives. So if you are working with code that uses these primitives, you don't even need to worry about Linux's memory-ordering primitives.
API:
smb_mb() orders both reads and writes
smb_rmb() orders only reads
smb_wmb() orders only writes


## Deadlock
A deadlock is a condition involving one or more threads of execution and one or more resources, such that each thread is waiting for one of the resources, but all the resources are already held. The threads are all waiting for each other, but they will never make any progress toward releasing the resources that they already hold. Therefore, none of the threads can continue, which means we have a deadlock.
~~~
Thread 1                      Thread 2

acquire lock A              acquire lock B

TRy to acquire lock B       try to acquire lock A

wait for lock B             wait for lock A

~~~
To avoid deadlock:
1. Follow proper lock ordering when there are nested locks.
2. Do not double acquire the same lock.

## Priority inversion
Can be explained wrt 3 threads or tasks. Consider 3 tasks
Task A with low priority
Task B with high priority
Task C with mid priority
If Task A starts executing and aquires a lock L(semaphore) and is preempted by higher priority Task B which also tries to acquire the lock L, it goes to sleep as lock L was acquired by Task A and due to context switch again Task A starts executing. If another mid priority Task C preemts task A and starts executing until timeout happens and control switches back to Task A which finally releases the lock L and then the high prio Task B gets a chance to execute.

In such a scenario, the high priority Task B is not getting the CPU as it was occupied by Task A and C. This is ***Priortiy Inversion*** where the high priority task behaves like a low priority task for short time i.e. its priority is inverted.

**Solution**:
Priority Inheritance Protocol:
Here priority of Task A is temporarily raised to the priority of Task B so that mid priority Task C does not preempt Task A. Task A finishes execution sooner and releases the lock L for Task B which helps to resolve priority inversion.
e.g. In RTOS (FreeRTOS), instead of semaphore lock we prefer Mutex lock as mutex is implemented to follow the priority inheritance protocol and hence mutex solves the priority inversion problem.



## Thread and Process execution and synchronisation

## Linux Cache Policys