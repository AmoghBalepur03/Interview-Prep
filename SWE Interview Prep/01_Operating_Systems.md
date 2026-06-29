# Operating Systems

An **operating system (OS)** is a program that acts as an intermediary between users/applications and computer hardware. Its two primary goals are **convenience** (execute user programs, make problem-solving easier) and **efficiency** (manage hardware resources fairly). It can be viewed two ways: as a **resource allocator** (manages CPU, memory, I/O, resolving conflicting requests) and as a **control program** (controls program execution to prevent errors and improper use).

> This note covers the full OS interview surface: architecture, processes/threads, IPC, synchronization, CPU scheduling, memory management, virtual memory, deadlocks, and file systems.

---

## 1. Introduction and System Structure

### The Kernel
The kernel is **the one program running at all times** — the core, essential part of the OS that interfaces with hardware and manages communication between software applications and hardware. Everything else is either a **system program** (compilers, shells) or an **application program** (Word, Chrome).

Components of the kernel:
- **Signals** — mechanisms for handling asynchronous events (external like user interrupts/hardware signals, or internal conditions).
- **Terminal handling** — manages user I/O between user and system.
- **Character I/O system** — handles input/output.
- **Terminal drivers** — let the kernel talk to terminal hardware, managing signaling, baud rate, flow control, and interrupts; provides device independence.

### Computer System Structure
1. **Hardware** — physical parts (CPU, memory, I/O devices).
2. **Operating System** — coordinates use of hardware among applications/users.
3. **Application Programs** — define how resources solve user problems.
4. **Users** — people, machines, or other computers.

### Bootstrap Program (Computer Startup)
- First code that runs on power-up/reboot.
- Stored in **ROM/EPROM (firmware)**, not on disk.
- Initializes the system and **loads the OS kernel** into memory to start execution.

### Interrupts
A signal from hardware or software alerting the CPU to an event needing immediate attention. **The OS is interrupt-driven.**

How it works:
1. CPU is executing instructions.
2. A device controller finishes an I/O operation and triggers an interrupt.
3. CPU saves its current state (registers, program counter).
4. CPU transfers control to an **Interrupt Service Routine (ISR)** via the **interrupt vector** (a table of addresses).
5. The ISR handles the interrupt.
6. CPU resumes its previous task.

**Trap/Exception** — a software-generated interrupt caused by an error (division by zero) or a user request (system call).

### I/O Structure
- **Synchronous I/O** — after a request, the user program waits until I/O completes; CPU is idle during the wait (may use a wait instruction).
- **Asynchronous I/O** — control returns to the program immediately; I/O continues concurrently.
- The OS uses a **device-status table** to track each device. On completion, an interrupt signals the CPU, which updates the table.

### Direct Memory Access (DMA)
- Used for high-speed devices (disks) to transfer large blocks **without constant CPU intervention**.
- The DMA controller manages transfer directly between device and main memory.
- CPU is interrupted **once per block** (not per byte) — greatly improving efficiency.

### Storage Hierarchy and Caching
Storage is organized as a pyramid by **speed** (faster at top), **cost** (more expensive/byte at top), and **volatility** (top volatile, bottom non-volatile).

**Caching** — copying data from a slower level to a faster level temporarily:
- Faster storage (cache) checked first → **cache hit** uses it directly; **cache miss** brings it in.
- Cache management (size, replacement policy) is a critical OS design problem.
- Main memory (RAM) is itself a cache for secondary storage (disk).

| Cache Type | Description | Location & Characteristics |
|---|---|---|
| **L1** | Smallest, fastest, built into each core; split into data (L1d) and instruction (L1i); LRU | On-chip, CPU speed, ~2–64 KB per core |
| **L2** | Larger but slower than L1; dedicated or shared; LRU / pseudo-LRU | On/near CPU chip, ~128 KB–8 MB |
| **L3** | Larger/slower than L2; shared among all cores | On CPU chip or separate, ~1–8 MB |
| **L4** | Less common, extra layer complementing L3 | Separate/embedded chip, slower than L3 |

### Computer-System Architecture
- **Single Processor** — one general-purpose CPU plus special-purpose processors (GPU, disk controller).
- **Multiprocessor (Parallel/Tightly-Coupled)** — advantages: increased throughput, economy of scale, increased reliability (graceful degradation).
  - **Asymmetric Multiprocessing (AMP)** — master-slave; each processor has a specific task.
  - **Symmetric Multiprocessing (SMP)** — most common; all processors are peers.
- **Multicore Systems** — multiple cores on a single chip.
- **Clustered Systems** — multiple separate systems working together, often via a **Storage-Area Network (SAN)**; for **High Availability** and **High-Performance Computing (HPC)**.

### Multiprogramming vs. Timesharing
- **Multiprogramming** (foundation of modern OSes) — when one job waits for I/O, the OS switches to another job in memory; requires CPU scheduling and memory management.
- **Timesharing (Multitasking)** — extends multiprogramming; CPU switches between jobs so frequently that users interact simultaneously; needs fast response (<1s); often requires virtual memory; high context-switch overhead.

### Dual-Mode Operation and Timer
- **Dual-Mode** — protection mechanism:
  - **User Mode** — restricted; cannot execute privileged instructions.
  - **Kernel Mode (Supervisor/System)** — can execute any instruction.
  - A **mode bit** in hardware indicates the current mode. System calls/interrupts/traps switch user → kernel.
- **Timer** — prevents a user program from hogging the CPU. OS sets a timer before giving CPU to a process; on expiry it generates an interrupt, returning control to the OS.

### Multiprogrammed Batch Systems
Because I/O takes much longer than the CPU, the CPU is not idle while waiting — the system switches between programs. Key features: multiple jobs in memory, CPU scheduling, context switching, memory management, I/O interrupts. Challenges: complex OS design, context-switch overhead, memory protection, job-scheduling algorithms, resource contention.

### Time-Sharing OS (detail)
Supports multiple interactive users via terminals/remote access. CPU time is divided into **time slices/quantums**; rapid switching gives the illusion of concurrency. Advantages: maximized CPU utilization, multi-user interaction, efficient resource sharing, improved productivity, reduced idle time. Disadvantages: increased OS complexity, degraded response time under load, security risks, context-switch overhead, resource contention. Examples: **Unix, CTSS, Multics**.

### Other Computing Environments
- **Traditional, Mobile, Distributed** (networked heterogeneous systems appearing as one).
- **Client-Server** — clients request, servers provide (compute-server, file-server).
- **Peer-to-Peer (P2P)** — decentralized (BitTorrent, blockchain, early Napster).

### Virtualization
Run multiple guest OSes concurrently on one physical machine. The **VMM / Hypervisor** creates and runs VMs.
- **Type 1 (Bare-metal)** — runs directly on hardware (VMware ESXi, Xen, Hyper-V); no host OS; higher performance.
- **Type 2 (Hosted)** — runs as an app on a host OS (VMware Workstation, VirtualBox); easier setup.

Benefits: consolidation, isolation, easy dev/testing.

### Cloud Computing
Delivering computing services over the Internet — a logical evolution of virtualization + distributed computing.
- **Service Models:** IaaS (EC2, S3), PaaS (App Engine, Azure App Services), SaaS (Gmail, Salesforce, M365).
- **Deployment Models:** Public, Private, Hybrid.

### Real-Time Embedded Systems
Dedicated systems with strict time constraints; correctness depends on **logical result and timing**. **Hard real-time** — missed deadline = total failure. **Soft real-time** — missed deadline undesirable but not catastrophic.

### Protection vs. Security
- **Protection** — internal mechanism controlling access of processes/users to resources defined by the OS.
- **Security** — defense of the whole system against external/internal attacks (malware, unauthorized access, DoS).
- Mechanisms: **User ID**, **Group ID**, **Privilege Escalation** (e.g., `sudo` → root).

### Kernel Data Structures
- **Linked lists** — singly (simple queues), doubly (scheduling queues), circular (round-robin).
- **Trees** — BST for efficient O(log n) lookups in balanced form.
- **Hash maps** — fast file-descriptor lookups.
- **Bitmap** — string of n bits tracking status of n items (free/used disk blocks, free pages).

### OS Structures (how the codebase is organized)
- **Simple/Monolithic (MS-DOS)** — no layer separation; a misbehaving program can crash the system.
- **Layered Monolithic (original UNIX)** — all functionality in one large kernel-mode executable.
- **Layered Approach** — hierarchical layers; each built on the one below. Pro: simpler design/debugging. Con: overhead passing through layers.
- **Microkernel (Mach)** — minimal kernel (address-space mgmt, IPC, basic scheduling); other services run as user-level servers communicating via **message passing**. Pros: easier to extend/port, more reliable/secure. Con: message-passing performance overhead.
- **Modular (LKMs — Linux, Solaris)** — monolithic core with dynamically loadable modules; combines flexibility, performance, and efficiency.
- **Hybrid** — most modern OSes. Linux/Solaris (monolithic + LKMs), macOS/Darwin (Mach microkernel + BSD in same address space), Windows NT (modified microkernel).

### Modern OSes
- **iOS** — derived from macOS; layered on Darwin (Mach + BSD); ARM; Cocoa Touch API.
- **Android** — modified Linux kernel (wake locks); apps in Java compiled to **Dalvik VM / ART**, not standard JVM.

### Debugging and Performance
- **Log files**, **core dump** (crashed process memory), **crash dump** (kernel memory after panic).
- **Performance tuning** removes bottlenecks; tools: `top`, Task Manager, profiling, **DTrace** (live instrumentation).

### SysGen and Boot
- **SysGen** — configuring/building an OS for specific hardware (e.g., `make config`).
- **Boot:** Power On → firmware **POST** → **bootstrap loader** finds and loads a larger bootloader (GRUB, Windows Boot Manager) → bootloader loads the kernel → kernel initializes, loads drivers, starts the first process (`init`/`systemd`, `launchd`, `smss.exe`).

### OS Services and System Calls
**User services:** UI (CLI/shell, GUI/WIMP, batch, touchscreen), program execution, I/O operations, file-system manipulation, communications (shared memory same machine; message passing across network), error detection.
**System services:** resource allocation, accounting, protection and security.

A **system call** is a kernel function a program invokes to request a service (low-level interface). Programmers use a higher-level **API** instead:
- **Win32** (Windows), **POSIX** (UNIX/Linux/macOS), **Java API** (JVM).

How a system call works: app calls an API function → library places parameters (registers/memory block/stack) → issues a **trap** to switch user → kernel → kernel's system-call handler looks up the call number in a table → executes → returns.

**Parameter passing:** registers (fast, limited), block of memory (address in register; Linux/Solaris), or push onto the stack.

**Categories:** Process Control, File Management, Device Management, Information Maintenance, Communications, Protection.

**System programs** (user's view) are utilities built on system calls: file management (`cp`, `rm`, `mkdir`, `ls`), status info (`date`, `ps`, `top`, `df`), file modification (editors, `grep`, `sort`), language support (`gcc`, `gdb`), loaders/linkers, communications (`ssh`, `ftp`), and **daemons/services** (`sshd`, `httpd`).

---

## 2. Processes

### Process vs. Program
- A **program** is passive — instructions stored on disk.
- A **process** is active — a program loaded into memory and executing, including its current state.
- One program can spawn many processes (e.g., many users running `/bin/ls`).

### Process in Memory (Layout)
| Section | Contents | Growth |
|---|---|---|
| **Text** | Program code (machine instructions) | fixed |
| **Data** | Global variables | fixed |
| **Heap** | Dynamically allocated memory at runtime | grows **upward** |
| **Stack** | Local variables, function parameters, return addresses | grows **downward** |

A single-threaded process has one program counter; a multi-threaded process has multiple (one per thread).

### Process States
**New** → **Ready** (in memory, waiting for CPU) → **Running** (executing) → **Waiting/Blocked** (waiting for an event/I/O) → **Terminated**.

### Process Control Block (PCB)
Per-process data structure holding: process state, **program counter**, CPU registers, CPU-scheduling info (priority, queue pointers), memory-management info (base/limit, page/segment tables), accounting info (CPU time, PID), I/O status (devices, open files).

### Process Scheduling
- **Job queue** — all processes in the system.
- **Ready queue** — processes in memory ready to execute (PCBs linked).
- **Device queues** — processes waiting for a specific I/O device (one per device).

**Schedulers:**
- **Long-term (job scheduler)** — selects which processes enter the ready queue; invoked infrequently; controls the **degree of multiprogramming**; should balance I/O-bound and CPU-bound jobs.
- **Short-term (CPU scheduler)** — selects the next process to run; invoked very frequently (ms); must be fast.
- **Medium-term** — **swapping**: removes a process from memory and reintroduces it later (suspended state) to manage memory pressure.

**I/O-bound** — many short CPU bursts, mostly I/O. **CPU-bound** — few long CPU bursts. A good long-term scheduler balances both.

### Context Switch
Switching the CPU from one process to another: save the old process's state into its PCB, load the new one's. Context-switch time is **pure overhead**; depends on hardware (multiple register sets can speed it up).

### Process Creation: `fork()` / `exec()`
Processes form a **process tree** (parent/child). Resource-sharing options: share all, share a subset, share none. Execution: concurrent, or parent waits.

- **`fork()`** — creates a child that is an **exact duplicate** of the parent; both continue after the call. Return value: child's PID in parent, **0** in child, **-1** on error.
- **`exec()`** — overlays the process's memory with a **new program**; PID stays the same, but code/data/heap/stack are replaced.
- Typical workflow: `fork()` then `exec()` in the child, parent `wait()`s.

What is copied on `fork()` vs. reset on `exec()`:

| Resource | On `fork()` | On `exec()` |
|---|---|---|
| **PID** | New, unique PID | Unchanged (transformed, not replaced) |
| **PC** | Copied (resume after `fork`) | Reset to entry point of new program |
| **Memory** | Entire address space copied; **Copy-on-Write (COW)** until modified | Text/data/heap/stack obliterated, rebuilt from executable |
| **Registers** | Copied (identical machine state) | Reinitialized |
| **File Descriptors** | Copied; share same open-file description and **file offset** | Stay open unless `FD_CLOEXEC` set (how shells wire pipes) |
| **Shared Memory** | References copied; still shared | Detached |
| **Pointers** | Copied verbatim, valid (COW) | Become invalid |
| **Signals** | — | Dispositions reset to default; ignored stay ignored |

### Process Termination
- **Voluntary** — `exit()`, returning a status to the parent.
- **Involuntary** — parent uses `abort()` (child exceeded resources, task no longer needed, parent exiting).
- **Cascading termination** — if a parent terminates, the OS terminates all children.
- **`wait()`** — parent waits for a child to terminate and reaps its exit status (prevents zombies).

**Zombie** — terminated but parent hasn't `wait()`ed; stays in the process table as "defunct." **Orphan** — parent terminated first; adopted by `init`/`systemd` (PID 1), which `wait()`s for it.

### Cooperating Processes
- **Independent** — cannot affect/be affected by others.
- **Cooperating** — share data. Reasons: information sharing, computation speedup, modularity, convenience.

---

## 3. Inter-Process Communication (IPC)

### Models
- **Shared Memory** — processes share a memory segment, read/write directly; fastest after setup; **processes themselves must synchronize** access (not the OS).
- **Message Passing** — exchange messages through the OS; good for small data; easier; slower (kernel involvement per message); no explicit synchronization needed for basic send/receive.

### Producer-Consumer Problem
Producer produces items, consumer consumes them via a **buffer**. **Unbounded** — producer never blocks. **Bounded** — fixed size; producer waits if full, consumer waits if empty.

**Shared-memory bounded-buffer:** circular array with `in` (next free) and `out` (first full). Empty when `in == out`; full when `(in + 1) % BUFFER_SIZE == out` — one slot always unused to distinguish full from empty.

```cpp
// Producer
while (true) {
    /* produce next_produced */
    while (((in + 1) % BUFFER_SIZE) == out) ;   // busy-wait if full
    buffer[in] = next_produced;
    in = (in + 1) % BUFFER_SIZE;
}
// Consumer
while (true) {
    while (in == out) ;                          // busy-wait if empty
    next_consumed = buffer[out];
    out = (out + 1) % BUFFER_SIZE;
    /* consume next_consumed */
}
```

This uses **busy waiting** (wastes cycles). The real solution uses semaphores.

### Message Passing Details
Two operations: `send(message)`, `receive(message)`. Implementation issues:
- **Naming:** *Direct* — processes name each other (`send(P, msg)`, `receive(Q, msg)`); link auto-established between exactly one pair. *Indirect* — via **mailboxes/ports**; many processes can share a mailbox (client-server).
- **Synchronization:** blocking (synchronous) vs. non-blocking (asynchronous) send/receive; **rendezvous** = both blocking.
- **Buffering:** zero capacity (rendezvous), bounded (sender blocks if full), unbounded (sender never blocks).

Real-world IPC: **POSIX shared memory** (`shm_open()` + `mmap()`), **Mach** (everything is message passing via ports), **Windows LPC**.

### Client-Server Communication
- **Sockets** — endpoint defined by IP + port; TCP (reliable, stream) vs. UDP (unreliable, datagram).
- **RPC** — abstracts network communication as a procedure call; **client stub** marshals parameters, **server stub** unpacks/executes/returns; handles endianness via XDR.
- **Pipes** — ordinary/anonymous (related processes, unidirectional, `ls | grep "foo"`) vs. named/FIFO (filesystem files, unrelated processes).

**Anonymous pipe** — `pipe(int filedes[2])`, `filedes[0]` read end, `filedes[1]` write end. Create before `fork()`, close unused ends (closing the write end signals EOF). `read()` blocks if empty; `write()` blocks if full (~4–8 KB).

```cpp
int pipefd[2];
pipe(pipefd);
pid_t cpid = fork();
if (cpid == 0) {                 // child reads
    close(pipefd[1]);
    char buf;
    while (read(pipefd[0], &buf, 1) > 0) cout << buf;
    close(pipefd[0]); _exit(0);
} else {                         // parent writes
    close(pipefd[0]);
    string msg = "Hello from parent!";
    write(pipefd[1], msg.c_str(), msg.length());
    close(pipefd[1]);            // closing write end signals EOF
    wait(nullptr);
}
```

**Named pipe (FIFO)** — `mkfifo(path, mode)` creates a special file (`p` in `ls -l`); persists until `unlink()`. `open()` **blocks** until the other end is opened (built-in synchronization). A writer opens `O_WRONLY`, a reader `O_RDONLY`.

---

## 4. Threads

A **thread** is the basic unit of CPU utilization — a lightweight process. Threads of a process share code, data, and files, but each has its own **program counter, registers, and stack**.

**Benefits:** responsiveness (UI stays active while background work runs), resource sharing (easier/faster than IPC), economy (creating/switching threads is cheaper than processes — just a stack + registers), scalability (true parallelism on multicore).

### Multicore Programming and Amdahl's Law
- **Concurrency** — multiple tasks making progress (can be single-core via time-slicing).
- **Parallelism** — multiple tasks executing simultaneously (needs multiple cores).

**Amdahl's Law** — maximum speedup from parallelizing a program with serial fraction $S$ and parallel fraction $P$ (where $S + P = 1$) on $N$ cores:

$$
\text{speedup} \le \frac{1}{S + \dfrac{P}{N}}
$$

As $N \to \infty$, speedup $\to 1/S$. Even a 10% serial fraction caps speedup at 10×, regardless of cores — so designing highly parallel algorithms is crucial.

### Multithreading Models (user threads ↔ kernel threads)

| Model | Description | Pros | Cons | Examples |
|---|---|---|---|---|
| **Many-to-One** | Many user threads → one kernel thread | Efficient (user-space mgmt) | One blocking call blocks the whole process; no multicore | Green Threads, GNU Portable Threads |
| **One-to-One** | Each user thread → its own kernel thread | True parallelism; blocking call doesn't block process | Thread creation is expensive; OS limits thread count | Windows, Linux, modern Solaris |
| **Many-to-Many** | Many user threads multiplexed onto ≤ kernel threads | Best of both worlds | Complex coordination | Older Solaris |
| **Two-Level** | M:M but some user threads bound to a kernel thread | Critical threads get dedicated support | Even more complex | IRIX, HP-UX |

The **one-to-one** model dominates modern OSes due to multicore and the need for true parallelism.

### Thread Libraries
- **Pthreads** — POSIX standard (IEEE 1003.1c) for C/C++; a specification, not an implementation.
- **Windows Threads** — kernel-level, one-to-one (`CreateThread()`).
- **Java Threads** — built into the language/JVM (uses native OS threading); created by extending `Thread` or implementing `Runnable` (preferred).

### Implicit Threading
Shifts thread management to compilers/runtimes:
- **Thread Pools** — pre-create threads waiting for work; faster service, bounds thread count, separates task logic from mechanics.
- **OpenMP** — compiler directives for shared-memory parallelism (`#pragma omp parallel for`).
- **Grand Central Dispatch (GCD)** — Apple's blocks + dispatch queues.

### Tricky Threading Issues
1. **`fork()` and `exec()`:** `fork()` may duplicate only the calling thread (dangerous if others hold locks); `pthread_atfork()` registers handlers to avoid deadlocks. `exec()` replaces the entire process.
2. **Signal handling:** synchronous signals (e.g., `SIGSEGV`) go to the causing thread; asynchronous signals (e.g., `SIGINT`) go to an arbitrary/designated thread.
3. **Thread cancellation:** *asynchronous* — terminate immediately (dangerous; may hold locks/leave inconsistent state); *deferred* — target periodically checks and cleans up at cancellation points (`pthread_testcancel()`) — safer, preferred.
4. **Thread-Local Storage (TLS):** per-thread private copies of "global-like" variables; crucial for thread-safety of functions like `errno`. Unlike stack locals, TLS persists for the thread's lifetime.

### Concurrency Hazards
- **Race conditions** — outcome depends on non-deterministic interleaving of unsynchronized shared access. `counter++` is **not atomic** (LOAD, ADD, STORE) → lost updates.
- **Deadlocks** — require four conditions (mutual exclusion, hold-and-wait, no preemption, circular wait). Classic example: inconsistent lock ordering (Thread 1 locks A→B, Thread 2 locks B→A). Fix: enforce a **consistent global lock order**.
- **Dependencies** — data dependency (read-after-write), control dependency; managed with condition variables (producer-consumer).
- **Zombie threads** — terminated but not `pthread_join()`ed → small resource leak. Fix: always `pthread_join()` or `pthread_detach()`.

---

## 5. Process Synchronization

### The Critical-Section Problem
A **critical section** accesses shared variables and must not be executed by more than one process at a time. Structure: **entry section → critical section → exit section → remainder section**. A solution must satisfy:
1. **Mutual Exclusion** — only one process in its CS at a time.
2. **Progress** — if no one is in the CS, selection of the next entrant cannot be postponed indefinitely.
3. **Bounded Waiting** — a bound on how many times others can enter before a waiting process is granted entry (prevents starvation).

### Peterson's Solution (software, 2 processes)
Assumes atomic load/store. Shared: `int turn; bool flag[2];`

```cpp
do {
    flag[i] = true;                 // I want to enter
    turn = j;                       // but let you go first
    while (flag[j] && turn == j);   // wait
    // CRITICAL SECTION
    flag[i] = false;
    // REMAINDER SECTION
} while (true);
```

Proof of concept only — uses busy waiting and breaks under out-of-order execution.

### Hardware Atomic Instructions
**`test_and_set`** — atomically sets a boolean to true, returns the old value:

```cpp
boolean test_and_set(boolean *target) {
    boolean rv = *target;
    *target = true;
    return rv;
}
// Mutual exclusion:
do {
    while (test_and_set(&lock)) ;   // busy-wait
    // Critical Section
    lock = false;
} while (true);
```

Satisfies mutual exclusion and progress, but **not bounded waiting**.

**`compare_and_swap`** — atomically compares to an expected value and swaps if matched, returning the old value:

```cpp
int compare_and_swap(int *value, int expected, int new_value) {
    int temp = *value;
    if (*value == expected) *value = new_value;
    return temp;
}
```

### High-Level Tools
- **Mutex Locks (binary)** — `acquire()` / `release()`; **spinlock** uses busy waiting (good only for very short critical sections).
- **Semaphores** — integer `S` + waiting queue, avoids busy waiting:

```cpp
wait(S)   { S--; if (S < 0)  { add process to queue; block(); } }
signal(S) { S++; if (S <= 0) { remove P from queue; wakeup(P); } }
```

  - **Counting semaphore** — any integer (controls a pool of N resources).
  - **Binary semaphore** — 0 or 1 (equivalent to a mutex).

### Classic Synchronization Problems
**A. Bounded-Buffer** — `mutex = 1`, `full = 0`, `empty = N`:

```cpp
// Producer
wait(empty); wait(mutex); /* add item */ signal(mutex); signal(full);
// Consumer
wait(full);  wait(mutex); /* remove item */ signal(mutex); signal(empty);
```

The order of `wait`s is crucial to prevent deadlock.

**B. Readers-Writers** (readers-preference) — `rw_mutex = 1`, `mutex = 1`, `read_count = 0`:

```cpp
// Writer
wait(rw_mutex); /* write */ signal(rw_mutex);
// Reader
wait(mutex); read_count++; if (read_count == 1) wait(rw_mutex); signal(mutex);
/* read */
wait(mutex); read_count--; if (read_count == 0) signal(rw_mutex); signal(mutex);
```

Readers-preference can cause **writer starvation**.

**C. Dining Philosophers** — five philosophers, a chopstick between each pair; need both neighbors' chopsticks to eat. Naive solution deadlocks (all grab left simultaneously). Fixes: allow at most 4 at the table; pick up both only if both available (in a critical section); **asymmetric** ordering (odd grabs left-then-right, even grabs right-then-left).

### Monitors
A high-level ADT that encapsulates shared data and procedures. **Only one process active inside the monitor** at a time (compiler-enforced mutual exclusion). **Condition variables** provide waiting: `x.wait()` suspends and releases the monitor; `x.signal()` resumes exactly one waiter (no effect if none waiting). Synchronization code is centralized and cleaner.

### Common Pitfalls
- **Deadlock** — processes wait indefinitely for events only the others can cause.
- **Starvation** — indefinite denial of an available resource.
- **Priority Inversion** — high-priority task waits for a low-priority task holding a resource; solved by **priority inheritance** (low-priority task temporarily inherits the higher priority).

---

## 6. CPU Scheduling (added detail)

### Scheduling Algorithms

| Algorithm | Description | Preemptive? |
|---|---|---|
| **FCFS** | Order of arrival (FIFO). Long jobs delay short ones (convoy effect). | No |
| **SJF** | Shortest next CPU burst first; minimizes average waiting time. | No |
| **SRTF** | Preemptive SJF; a shorter-remaining arrival preempts. | Yes |
| **Round Robin (RR)** | Fixed time quantum; preempt and requeue. Good for time-sharing. | Yes |
| **Priority** | Highest priority first; risk of starvation (fix: aging). | Both |
| **LJF / LRTF** | Longest job / longest remaining time first. | No / Yes |
| **Multilevel Queue** | Multiple fixed-priority queues; processes permanently assigned. | Varies |
| **MLFQ** | Processes move between queues based on behavior. | Yes |

### Scheduling Metrics
For each process: **Turnaround Time** = completion − arrival; **Waiting Time** = turnaround − burst; **Response Time** = first-run − arrival. Goals: maximize CPU utilization and throughput; minimize turnaround, waiting, and response times.

**Worked example (non-preemptive SJF)** — processes with bursts P1=6, P2=8, P3=7, P4=3 all arriving at t=0. SJF order: P4(3) → P1(6) → P3(7) → P2(8).

```
| P4 |   P1   |    P3     |     P2     |
0    3        9          16           24
```

Waiting times: P4=0, P1=3, P3=9, P2=16 → average = 28/4 = **7**. (FCFS in arrival order P1,P2,P3,P4 gives average 10.25 — SJF is provably optimal for average waiting time.)

### Round Robin Note
With quantum $q$ and $n$ processes, each waits at most $(n-1)q$ before its next slice. Small $q$ → more responsive but higher context-switch overhead; large $q$ → approaches FCFS.

---

## 7. Memory Management (added detail)

### Contiguous Allocation
- **Fixed partitions** — internal fragmentation (unused space inside a partition).
- **Variable partitions** — external fragmentation (free memory split into small holes). Allocation strategies: first-fit, best-fit, worst-fit. **Compaction** shuffles memory to coalesce holes.
- **Internal fragmentation** — allocated more than requested. **External fragmentation** — enough total free memory but not contiguous.

### Paging
Physical memory is divided into fixed-size **frames**, logical memory into **pages** of the same size. A **page table** maps page → frame. A logical address is split into **page number** + **offset**.

- Eliminates external fragmentation (any free frame works); small internal fragmentation in the last page.
- **TLB (Translation Lookaside Buffer)** — a cache for page-table entries.

**Effective Access Time** with TLB hit ratio $\alpha$, TLB access $\varepsilon$, memory access $m$:

$$
\text{EAT} = \alpha (\varepsilon + m) + (1 - \alpha)(\varepsilon + 2m)
$$

(the miss path costs an extra memory access to read the page table).

### Segmentation
Memory divided into variable-length **segments** matching the programmer's view (code, stack, heap). Address = `<segment number, offset>`; a segment table holds **base** and **limit**. Suffers external fragmentation. **Paged segmentation** combines both.

### Virtual Memory and Demand Paging
**Virtual memory** lets a process run with only part of it in physical memory. **Demand paging** loads pages only when referenced; a reference to a non-resident page causes a **page fault**:
1. Trap to the OS; check if the reference is valid.
2. Find a free frame (or evict one).
3. Read the page from disk into the frame.
4. Update the page table; restart the instruction.

**Pure demand paging** — start with no pages in memory; fault them in on first access.

### Page Replacement Algorithms
When no free frame exists, choose a victim.

| Algorithm | Idea | Notes |
|---|---|---|
| **FIFO** | Evict the oldest page | Simple; suffers **Belady's anomaly** (more frames → more faults) |
| **Optimal (OPT/MIN)** | Evict the page not used for the longest future time | Theoretical lower bound; needs future knowledge |
| **LRU** | Evict the least-recently-used page | Good approximation of OPT; needs counters/stack |
| **Clock / Second-Chance** | Circular list + reference bit; give a second chance before eviction | Practical LRU approximation |

**Worked FIFO (Belady's anomaly)** — reference string `1 2 3 4 1 2 5 1 2 3 4 5`: 3 frames → 9 faults, 4 frames → 10 faults (anomaly). LRU and OPT never show this (they are **stack algorithms**).

### Thrashing
A process spends more time paging than executing — caused by too high a degree of multiprogramming (not enough frames per process). 
- **Working-set model** — track the set of pages referenced in a recent window $\Delta$; allocate enough frames to hold each process's working set; suspend processes if total demand exceeds frames.
- **Page-Fault Frequency (PFF)** — adjust frame allocation to keep fault rate within an acceptable band.

---

## 8. Deadlocks (added detail)

### Four Necessary Conditions (must all hold)
1. **Mutual Exclusion** — a resource is held in non-sharable mode.
2. **Hold and Wait** — a process holds resources while waiting for more.
3. **No Preemption** — resources are released only voluntarily.
4. **Circular Wait** — a cycle of processes each waiting for the next.

### Resource-Allocation Graph
Processes (circles) and resources (squares); request edges P→R, assignment edges R→P. With single-instance resources, a **cycle ⇔ deadlock**. With multiple instances, a cycle is necessary but not sufficient.

### Handling Strategies
- **Prevention** — negate one of the four conditions (e.g., request all resources at once → no hold-and-wait; impose a global ordering → no circular wait).
- **Avoidance** — use a priori information; **Banker's Algorithm**.
- **Detection & Recovery** — allow deadlocks, detect via a wait-for graph, then recover.
- **Ostrich algorithm** — ignore it (used by many general-purpose OSes).

### Banker's Algorithm
For multiple resource instances. Data structures: `Available[m]`, `Max[n][m]`, `Allocation[n][m]`, `Need = Max − Allocation`.

**Safety algorithm** — is the state safe (does a sequence exist where all processes can finish)?
1. `Work = Available`, `Finish[i] = false`.
2. Find an `i` with `Finish[i] == false` and `Need[i] ≤ Work`.
3. `Work += Allocation[i]; Finish[i] = true`; repeat.
4. If all `Finish[i] == true`, the state is **safe**.

**Resource-request algorithm** — when process $i$ requests `Request[i]`: if `Request ≤ Need` and `Request ≤ Available`, tentatively allocate and run the safety check; if safe, grant; else, the process waits.

### Recovery
- **Process termination** — abort all deadlocked processes, or abort one at a time until the cycle breaks.
- **Resource preemption** — select a victim, roll it back to a safe state, prevent starvation (avoid always choosing the same victim).

---

## 9. File Systems (added detail)

### Directory Structure
Single-level, two-level, tree-structured, acyclic-graph (shared subdirectories via links), general graph (needs cycle handling/garbage collection).

### File Allocation Methods
| Method | Idea | Pros | Cons |
|---|---|---|---|
| **Contiguous** | File occupies consecutive blocks | Fast sequential + random access | External fragmentation; hard to grow |
| **Linked** | Each block points to the next | No external fragmentation; easy growth | Slow random access; pointer overhead; reliability |
| **Indexed (inode)** | An index block holds all block pointers | Fast random access; no external fragmentation | Index-block overhead; large files need multilevel/indirect blocks |

- **FAT (File Allocation Table)** — a linked scheme keeping all pointers in one table at the start of the volume (cached for fast traversal).
- **inode (UNIX)** — direct blocks + single/double/triple **indirect** blocks → supports very large files.

### Free-Space Management
**Bit vector/bitmap** (one bit per block), **linked list** of free blocks, **grouping**, **counting**.

### Journaling
A **journaling file system** (ext4, NTFS, XFS) logs metadata (and optionally data) changes to a journal **before** applying them. After a crash, replay the journal to restore consistency — avoiding a full `fsck`. Provides atomicity for multi-block updates.

---

## 10. Quick Interview Checklist

- **Process vs. thread:** processes have separate address spaces; threads share code/data/files but have private stack/registers/PC.
- **`fork()` returns** child PID to the parent, 0 to the child; child is a COW duplicate.
- **Zombie** = dead child not reaped; **orphan** = live child whose parent died (adopted by init).
- **Semaphore vs. mutex:** mutex has ownership (only the locker unlocks); a binary semaphore is a signaling construct.
- **Deadlock** needs all four conditions; break any one to prevent.
- **Belady's anomaly** afflicts FIFO, never stack algorithms (LRU, OPT).
- **Paging** kills external fragmentation; **segmentation** matches the programmer's view but reintroduces it.
- **Thrashing** → use the working-set model / PFF.
- **Banker's algorithm** = deadlock avoidance via safe-state checking.
