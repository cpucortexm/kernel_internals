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

## Concurreny issues (Critical section)

## Race Condition


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

## Semaphores vs Mutex


## Memory Ordering and Barriers


## Deadlock

## Priority inversion

## Thread and Process execution and synchronisation

## Linux Cache Policys