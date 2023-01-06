# os

## process

informally speaking, a process is a program in execution.

the program itself is lifeless, but when it is executed, it becomes a process.

the os creates illusion of multiple processes running simultaneously by switching between them very quickly. by **virtualization**, the os makes it appear that each process has its own processor.

time sharing and space sharing is counterparts of each other.

on top these mechanisms resides from **policy**. it is algorithm that decides which process to run next.

Scheduling policy is a function that takes a set of processes and returns a process to run next.

Address space is a set of memory locations that a process can access.

Registers are small memory locations that are part of the processor.
For example :

- Program counter (PC) : holds the address of the next instruction to be executed.
- Frame pointer (FP) : holds the address of the top of the stack.
- Stack pointer (SP) : holds the address of the bottom of the stack.

Process API

- CreateProcess() : creates a new process and its primary thread.
- DestroyProcess() : terminates the specified process and all of its threads.
- Wait for Process() : suspends the execution of the calling thread until the specified process terminates.
- Status of Process() : retrieves the termination status of the specified process.

Transforming a program into a process is called **loading**. it is done by the os.

**loading** is the process of transferring a program from secondary storage to main memory and initializing its execution.

**memory allocation** is the process of reserving space in main memory for a process. Run time stack and heap are two parts of the address space. **Stack** is used for local variables and function parameters. **Heap** is used for dynamically allocated data structures.

**linking** is the process of combining all the object modules into a single executable file.

**i\o related setup** is done by the os. for example, opening a file, reading from a file, writing to a file, closing a file.

The process can be in one of the following states :

- Ready : the process is waiting to be assigned to a processor.
- Running : the process is currently executing on a processor.
- Blocked : the process is waiting for an event to occur, such as an i\o operation to complete.

From ready to running, the process is said to be **scheduled**.
from running to ready or blocked, the process is said to be **descheduled**.

OS keep information about process in some sort of list called **process table**. it contains information about all the processes in the system.

The snipped below is the process table in xv6.

```c
// the registers xv6 will save and restore
// to stop and subsequently restart a process
struct context {
  int eip;
  int esp;
  int ebx;
  int ecx;
  int edx;
  int esi;
  int edi;
  int ebp;
};
// the different states a process can be in
enum proc_state { UNUSED, EMBRYO, SLEEPING,
                  RUNNABLE, RUNNING, ZOMBIE };
// the information xv6 tracks about each process
// including its register context and state
struct proc {
  char *mem;                  // Start of process memory
  uint sz;                    // Size of process memory
  char *kstack;               // Bottom of kernel stack
                              // for this process
  enum proc_state state;      // Process state
  int pid;                    // Process ID
  struct proc *parent;        // Parent process
  void *chan;                 // If !zero, sleeping on chan
  int killed;                 // If !zero, has been killed
  struct file *ofile[NOFILE]; // Open files
  struct inode *cwd;          // Current directory
  struct context context;     // Switch here to run process
  struct trapframe *tf;       // Trap frame for the
                              // current interrupt
};
```

### key process terms

- The register context hold the state of the process. it is used to save and restore the state of the process.

- The process is the major OS abstraction of a running program. At any point in time, the process can be described by its state: the contents of memory in its address space, the contents of CPU registers (including the program counter and stack pointer, among others), and information about I/O (such as open files which can be read or written).

- The process API consists of calls that programs can make related to processes. Typically, this includes creation, destruction, and other useful calls.

- Processes exist in one of many different process states, including running, ready to run, and blocked. Different events (e.g., getting scheduled or descheduled, or waiting for an I/O to complete) transition a process from one of these states to the other.

- A process list contains information about all processes in the system. Each entry is found in what is sometimes called a process control block (PCB), which is really just a structure that contains information about a specific process.

