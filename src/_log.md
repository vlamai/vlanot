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

