# Periodic Tasks
Run Periodic tasks using Thread.Wait(), Sorted-List and simulated RTC

Alternative to using ‘Thread.Sleep()’ to run periodic tasks, we can use ‘Thread.Wait()’. ‘Thread.Wait()’ is one of the thread states, others are ‘Thread.Start()’, ‘Thread.Run()’, and etc.

In scenario we have many periodic tasks, but we have limited number of process execution threads. Some brainstorming thoughts:

-	Assuming ‘currTimeDate’ class is an accurate RTC that can notify ‘SortedList_PeriodicTasks.run_PeriodicTask()’, and it will check if latest periodic task is time-up to be executed by ‘workerThread’.

-	We would not want to initialize all tasks’ process before the timed preset time period. Because initialize a process will consume memory. We would only initialize and run the process’s function when preset time period timed.
-	To manage periodic tasks, we can put periodic tasks’ tag number and it function into ‘Priority Queue’ / ‘Sorted List’. ‘Priority Queue’ sorting keys are each task’s next trigger time, ‘Priority Queue’ values would be the tasks’ function intended to be executed.
c# inherent ‘SortedList’ can be used as Priority Queue.
-	We would not want to start a new execution thread for the periodic task ONLY after preset time period timed. Because if we started a new process thread from current thread, we will need to wait till this thread finished the process execution. It would result in current thread being block for any subsequent code execution until this new process thread finished it execution.
Hence, we declare a fixed number of ‘process execution threads’ / ‘worker threads’ / ‘threads pool’ at the start. When they finished any timed periodic tasks’ function, worker threads enter Thread.Wait() state to idle. Therefore, we don’t need to repeatedly create and exit ‘worker threads’.
-	To keep track of the fixed number of ‘worker threads’ / ‘threads pool’ state, we can have a Stack to record which ‘worker threads’ are in Wait state. Only notify those ‘worker threads’ that are in Wait state to run the timed periodic tasks. After finished timed periodic task execution, ‘worker threads’ will push its Thread ID back to Stack and enter Thread.Wait() state to idle.
-	Using Stack to notify the most recently ran ‘worker thread’ wake up from idle, such that idling ‘worket threads’ will remain idle longer time.
<br /><br /><br />

### MS Visual C# Console App coding
PeriodicTask_ThreadPool_WaitState.cs.txt

<br /><br />
The program doesn’t execute the periodic tasks correctly at the beginning, I still wonder why.
