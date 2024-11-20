A repo demonstrating low level OS code in C.

The OS of choice is Xinu.

PA1:

1) long zfunction(long param)

This function performs a number of operations on the parameter param and then returns it. These operations are:

Clear the 20th to 27th bits, counting from the left starting with 0.
Shift by 8 bits to the left.
Fill the right-most 8 bits with 1s.
For example, the input parameter 0xaabbccdd should generate a return value of 0xbbc00dff. The size of long is assumed to be 4 bytes. The code for this function is entirely written in x86 assembly.

2) void printprocstks(int priority)

For each existing process with larger priority than the parameter, the stack base, stack size, stacklimit, and stack pointer are printed. Also, for each process, the process name, the process id and the process priority are included.

3) void printsyscallsummary()

The summary of the system calls which have been invoked for each process are printed. The frequency (how many times each system call type is invoked) and the average execution time (how long it takes to execute each system call type on average) of the system calls for each process are printed.


PA2:

1) Scheduler 1: Exponential Distribution Scheduler (EXPDISTSCHED)

This scheduler chooses the next process based on a random value that follows the exponential distribution. When a rescheduling occurs, the scheduler generates a random number with the exponential distribution, and chooses a process with the lowest priority that is greater than the random number. If the random value is less than the lowest priority in the ready queue, the process with the lowest priority is chosen.
If the random value is greater than or equal to the highest priority in the ready queue, the process with the highest priority is chosen. When there are processes having the same priority, they should be scheduled in a round-robin way. The processes do not need to run adjacent to each other, but the same process in that priority level should not be run again until all others have had a chance to run.

2) Scheduler 2: Linux-like Scheduler (LINUXSCHED)

In this algorithm, the scheduler divides CPU time into continuous epochs. In each epoch, every existing process is allowed to execute up to a given time quantum, which specifies the maximum allowed time for a process within the current epoch. The time quantum for a process is computed and refreshed at the beginning of each epoch. If a process has used up its quantum during an epoch, it cannot be scheduled until another epoch starts. A process can be scheduled many times as long as it has not used up its time quantum. The scheduler ends an epoch and starts a new one when all the runnable processes (i.e., processes in the ready queue) have used up their time.

At the beginning of a new epoch, the scheduler computes the time quantum for every existing processes, including the blocked ones. As a result, a blocked process can start in the new epoch when it becomes runnable. For a process that has never executed or used up its time quantum in the previous epoch, its new quantum value is set to its process priority (i.e., quantum = priority). For a process that has not used up its quantum in the previous epoch, the scheduler allows half of the unused quantum to be carried over to the new epoch. Suppose for each process there is a variable counter describing how many ticks are left from its previous quantum, then the new quantum value is set to floor(counter / 2) + priority. For example, a counter of 5 and a priority of 10 produce a new quantum value of 12. Any new processes created in the middle of an epoch have to wait until the next epoch to be scheduled. Any priority changes in the middle of an epoch only take effect in the next epoch.

To schedule processes during each epoch, the scheduler introduces a goodness value for each process. For a process that has used up its quantum, its goodness value is 0. For a runnable process, the scheduler considers both the priority and the remaining quantum in computing the goodness value: goodness = counter + priority. The scheduler always schedules a runnable process that has the highest goodness value (it uses the round-robin strategy if there are processes with the same goodness value). Unlike the expdistched, processes with a higher priority value keep running without being preempted.


PA3:

The goal of this project is to implement on-demand paging using system calls and their supporting infrastructure:

SYSCALL xmmap(int virtpage, bsd_t source, int npages) It maps a backing store of size npages pages to the virtual page virtpage. A process may call this multiple times to map data structures, code, etc.

SYSCALL xmunmap(int virtpage) This call removes a virtual memory mapping.

SYSCALL vcreate(int *procaddr, int ssize, int hsize, int priority, char *name, int nargs, long args) This call will create a new process. It is different from create() in that the process' heap will be private and exist in its virtual memory. The size of the heap (in number of pages) is specified by the user through hsize. Processes created with create() should not have a private heap, but should still be able to use xmmap().

WORD *vgetmem(unsigned int nbytes) Allocates the desired amount of memory if possible. vgetmem() will get the memory from a process' private heap located in virtual memory.

SYSCALL srpolicy(int policy) This function will be used to set the page replacement policy, Second-Chance (SC), which will be the default policy.

SYSCALL vfreemem(struct mblock *block, unsigned int size_in_bytes) vfreemem() frees memory from the process' private heap in virutal memory. Complementary to vgetmem(). vfreemem() takes two parameters and returns OK or SYSERR.
