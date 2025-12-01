## SYSC 4001 Assignment 3 - Part 1
This project implements three CPU scheduling algorithms for the SYSC 4001 Assignment 3 Part 1 specification:
- EP: External Priority (non-preemptive)
- EP_RR: External Priority with Round Robin (preemptive on priority or quantum expiry)
- RR: Pure Round Robin (no priority)

The simulator reads .txt test case files from the input_files directory and produces detailed transition tables and metrics for each scheduling algorithm.
Generated metric CSVs detail the Throughput, Average Wait Time, Average Turnaround time and Average Response Time of each test.
All output filestables are located in the organized directory structure below:

    output_files/
    │
    ├── EP_outputs/        # Output tables and .csv files for the EP scheduler
    ├── EP_RR_outputs/     # Output tables and .csv files for the EP_RR scheduler
    └── RR_outputs/        # Output tables and .csv files for the RR scheduler
  
### Priority Model
In this assignment, we define priority = process.PID so smaller PID  →  higher priority.

Assumption:
If two processes have the same PID-based priority (unlikely in real scheduling but possible if manually created), the tie is broken by arrival order. The earlier-arriving process is treated as higher priority.

### Overview of Scheduler Architecture
All schedulers share common PCB fields:
- remaining_time: counts downwards as the process executes
- time_slice_used (RR & EP_RR only): counts upwards, tracking quantum progress
- time_to_next_io: counts downwards until next I/O event
These values are updated each simulated millisecond inside the main loop.

#### Quantum Handling
The quantum is not decremented. Instead, we increment a counter (time_slice_used) and compare against the quantum value. This allows clean resets when the process is preempted or returns from I/O.

#### EP() Helper
EP() sorts the ready queue using a comparator where higher numeric priority (larger PID) appears earlier in the vector, while the true highest priority (smallest PID) ends up at the back. At first glance the comparator looks reversed, but this is intentional. Our scheduler always selects the next process from ready_queue.back(). Thus, we reverse-sorting by (priority, arrival_time) and select from the back. This produces correct EP behavior: back() → smallest PID (highest priority)
This design is consistent and correct as long as all schedulers select from the back, which our implementation does.

Note: 
- We decided to make EP() a function because we were given FCFS() in the original. We do not use FCFS() but it still exists.
- RR does not use the EP() helper function. Instead, most RR logic is implemented directly inside execute_one_ms().





