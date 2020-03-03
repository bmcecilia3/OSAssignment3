# OSAssignment3
*Processes Scheduling*

## Task

Using C++, create a scheduling simulator that will implement various algorithms for scheduling processes on cores of a CPU. Pass the name of a configuration file to the simulator as a command line argument. For this project, you will not actually be spawning and scheduling processes on the CPU. Rather you will be creating simulated "processes" that consist of numerous CPU and I/O bursts.

The configuration file should specify the following:

  -  Line 1: Number of CPU cores (1-4)
  -  Line 2: Scheduling algorithm (RR: round-robin, FCFS: first come first serve, SJF: shortest job first, PP: preemptive priority)
  -  Line 3: Context switching overhead in milliseconds (100-1000)
  -  Line 4: Time slice in milliseconds (200 - 2000) - only used for round-robin algorithm, but include in configuration file regardless
  -  Line 5: Number of processes to run (1-24)
  -  Lines 6 - N: (one line per process - following values separated by commas)
      -  PID (unique number >= 1024)
      -  Start time (number of milliseconds after simulator starts that process should launch: 0 - 8000)
      -  Number of CPU and I/O bursts (odd number: CPU bursts = I/O bursts + 1)
      -  CPU and I/O burst times (sequence of milliseconds: 1000 - 6000 separated by vertical pipe)
          -  Alternates between CPU and I/O (always starting and ending with CPU)
      -  Priority (number: 0 - 4)

### *Implementation*

Your simulator should apply the selected scheduling algorithm to simulate running the processes until all have reaching the terminated state. This simulator will represent a symmetric multiprocessing system that uses a shared ready queue. You should simulate each core as a thread that acts independent of the other cores in order to determine when to load or remove a "process". The main thread should keep track of process states (e.g. determine when an I/O burst finishes and the process is put back in the ready queue).

Throughout the execution of your simulator, you should print a table that shows the status of all "processes". The following provides sample output:

```
|   PID | Priority |      State | Core | Turn Time | Wait Time | CPU Time | Remain Time |
+-------+----------+------------+------+-----------+-----------+----------+-------------+
|  1024 |        0 |      ready |   -- |       7.2 |       3.0 |      3.0 |        11.4 |
|  1096 |        0 |    running |    0 |       7.2 |       2.2 |      2.5 |         4.4 |
|  1042 |        0 |        i/o |   -- |       6.0 |       0.8 |      0.8 |         9.6 |
|  1031 |        0 |      ready |   -- |       5.0 |       5.0 |      0.0 |        49.8 |
|  1082 |        0 | terminated |   -- |       4.9 |       1.0 |      2.9 |         0.0 |
|  1029 |        0 |    running |    1 |       4.2 |       1.4 |      2.4 |         6.1 |
```

  -  PID: unique identifier
  -  Priority: priority (0 if not using preemptive priority)
  -  State: ready, running, i/o, or finished
  -  Core: CPU core currently on (-- if not running)
  -  Turn Time: Total time since creation (until finished)
  -  Wait Time: Total time waiting in the ready queue
  -  CPU Time: Total time spent running on a CPU core
  -  Remain Time: CPU time remaining until terminated
  
After all "processes" finish, you should print the following statistics about the scheduler:

  -  CPU utilization
  -  Throughput
      -  Average for first 50% of processes finished
      -  Average for second 50% of processes finished
      -  Overall average
  -  Average turnaround time
  -  Average waiting time

### *Scheduling Algorithms*

Round-Robin:
  -  Add newly created "processes" to the end of the ready queue
  -  When a core becomes available, schedule the "process" at the front of the ready queue on that core 
  -  If a "process's" time slice expires before the CPU burst is done, remove "process" from core and place at the end of the ready queue
  -  When a "process" finishes an I/O burst, place it at the end of the ready queue
  
First Come First Serve:
  -  Add newly created "processes" to the end of the ready queue
  -  When a core becomes available, schedule the "process" at the front of the ready queue on that core 
  -  When a "process" finishes an I/O burst, place it at the end of the ready queue
  
Shortest Job First:
  -  Add newly created "processes" to the ready queue in a position that is based on their remaining CPU time
  -  When a core becomes available, schedule the "process" at the front of the ready queue (i.e. has the least amount of remaining CPU time) on that core 
  -  When a "process" finishes an I/O burst, place it in the ready queue at a position that is based on its remaining CPU time
  
Preemptive Priority:
  -  Add newly created "processes" to the ready queue in a position that is based on priority number (0: highest prioity, 4: lowest priority)
      -  If processes have the same priorty, then revert to first come first serve
  -  When a core becomes available, schedule the "process" at the front of the ready queue (i.e. has the highest priority) on that core
  -  When a "process" finishes an I/O burst, place it in the ready queue at a position that is based on its priority
  -  IMPORTANT NOTE: If a "process" is newly created or finishes an I/O burst and has a higher priority than a "process" currently running on a core, then the lowest priority "process" that is running should be preempted - removed from the CPU and placed in the ready queue

## Compile and Run

compile: make

run: ./bin/osscheduler
