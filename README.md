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
