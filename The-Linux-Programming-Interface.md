# Chapter 1: History and Standards
## 1.1 A Brief History of UNIX and C 
## 1.2 A Brief History of Linux 
### 1.2.1 The GNU Project 
### 1.2.2 The Linux Kernel 
## 1.3 Standardization 
### 1.3.1 The C Programming Language
### 1.3.2 The First POSIX Standards
### 1.3.3 X/Open Company and The Open Group 
### 1.3.4 SUSv3 and POSIX.1-2001 
### 1.3.5 SUSv4 and POSIX.1-2008 
### 1.3.6 UNIX Standards Timeline 
### 1.3.7 Implementation Standards
### 1.3.8 Linux, Standards, and the Linux Standard Base
## 1.4 Summary

# Chapter 2: Fundamental Concepts 
## 2.1 The Core Operating System: The Kernel
## 2.2 The Shell
## 2.3 Users and Groups
## 2.4 Single Directory Hierarchy, Directories, Links, and Files 
## 2.5 File I/O Model
## 2.6 Programs
## 2.7 Processes
Each process has an environment list, which is a set of environment variables that are
maintained within the user-space memory of the process.

Environment variables are created with the `export` command in most shells (or
the `setenv` command in the C shell), as in the following example:
```bash
export MYVAR='Hello world'
```

### Resource limits
Using the `setrlimit()` system call

## 2.8 Memory Mappings
The `mmap()` system call creates a new memory mapping in the calling process’s virtual
address space.

The memory in one process’s mapping may be shared with mappings in other pro-
cesses.
* *private mapping*: modifications to the contents of the mapping are not visible to other processes and
are not carried through to the underlying file.
* *shared mapping*: visible and carried through

## 2.9 Static and Shared Libraries

## 2.10 Interprocess Communication and Synchronization 
**IPC** includes:
* *signals*: to indicate that an event has occurred.
* *pipes* and *FIFO*: to transfer data between processes.
* *sockets*: to transfer data from one process to another, either
on the same host computer or on different hosts connected by a network.
* *file locking*: allows a process to lock regions of a file in order to prevent
other processes from reading or updating the file contents
* *message queues*: to exchange messages (packets of data) between
processes.
* *semaphores*: to synchronize the actions of processes.
* *shared memory*: allows two or more processes to share a piece of memory.
When one process changes the contents of the shared memory, all of the other
processes can immediately see the changes.

## 2.11 Signals
Signals are often described as “software interrupts.”

Each signal type is identified by a different integer, defined with symbolic names of
the form `SIGxxxx`.

Within the shell, the `kill` command can be used to send a signal to a process. The
`kill()` system call provides the same facility within programs.

In the interval between the time a signal is generated and the time it is delivered, a
signal is said to be *pending* for a process. Normally, a pending signal is delivered as
soon as the receiving process is next scheduled to run, or immediately if the process is already running.
* it is also possible to block a signal by adding it to
the process’s signal mask. If a signal is generated while it is blocked, it remains
pending until it is later unblocked.

## 2.12 Threads
One way of envisaging threads is as a set of processes that share the same
virtual memory, as well as a range of other attributes. Each thread is executing the
same program code and shares the same data area and heap. However, each thread
has it own stack containing local variables and function call linkage information.

Threads can communicate with each other via the global variables that they share.
* with help from *condition variables* and *mutexes*.
* Threads can also communicate with one another using the IPC and synchronization mechanisms.

The primary advantages of using threads are that they make it easy to share
data (via global variables) between cooperating threads and that some algorithms
transpose more naturally to a multithreaded implementation than to a multiprocess
implementation. Furthermore, a multithreaded application can transparently take
advantage of the possibilities for parallel processing on multiprocessor hardware.

## 2.13 Process Groups and Shell Job Control
A *session* is a collection of process groups ( jobs). All of the processes in a session
have the same *session identifier*. A *session leader* is the process that created the session, and its process ID becomes the session ID.

## 2.14 Sessions, Controlling Terminals, and Controlling Processes
Sessions are used mainly by job-control shells. 

Job-control shells provide commands for listing all jobs, sending signals to jobs,
and moving jobs between the foreground and background.

## 2.15 Pseudoterminals


## 2.16 Date and Time
## 2.17 Client-Server Architecture
## 2.18 Realtime
Traditional UNIX implementations are not realtime
operating systems, although realtime variants have been devised. Realtime variants
of Linux have also been created, and recent Linux kernels are moving toward full
native support for realtime applications.

## 2.19 The /proc File System
The `/proc` file system is a virtual file system that provides an interface to kernel
data structures in a form that looks like files and directories on a file system.

## 2.20 Summary

# Chapter 3: System Programming Concepts
Whenever we make a system call or call a library function, we should always
check the return status of the call in order to determine if it was successful.

## 3.1 System Calls
A *system call* is a controlled entry point into the kernel, allowing a process to
request that the kernel perform some action on the process’s behalf.
* via the system call application programming interface (API).

System calls have a significant overhead (by comparison with a user-space function calls):
![systemcall](https://cloud.githubusercontent.com/assets/14265605/10780179/b0a76ada-7d08-11e5-953c-6da718acecaa.png)

Since, from the point of view of a C program, calling the C library wrapper func-
tion is synonymous with invoking the corresponding system call service routine, in
the remainder of this book, we use wording such as “invoking the system call `xyz()`”
to mean “calling the wrapper function that invokes the system call `xyz()`.”

## 3.2 Library Functions
functions that constitutes the standard C library.

Many library functions don’t make any use of system calls (e.g., the string-
manipulation functions). On the other hand, some library functions are layered on
top of system calls. For example, the `fopen()` library function uses the `open()` system
call to actually open a file. Often, library functions are designed to provide a more
caller-friendly interface than the underlying system call. For example, the `printf()`
function provides output formatting and data buffering, whereas the `write()` system
call just outputs a block of bytes. Similarly, the `malloc()` and `free()` functions perform
various bookkeeping tasks that make them a much easier way to allocate and free
memory than the underlying `brk()` system call.

## 3.3 The Standard C Library; The GNU C Library ( `glibc` )
Various other C libraries are available for Linux, including libraries with
smaller memory requirements for use in embedded device applications. Examples
include [uClibc](http://www.uclibc.org/) and [diet libc](http://www.fefe.de/dietlibc/).

```c
#include <gnu/libc-version.h>
const char *gnu_get_libc_version(void); // Returns pointer to null-terminated, statically allocated string containing GNU C library version number
```
## 3.4 Handling Errors from System Calls and Library Functions
Always check return values of system calls.
* A few system calls never fail. For example, `getpid()` always successfully returns
the ID of a process, and `_exit()` always terminates a process. It is not necessary
to check the return values from such system calls.

### Handling system call errors

The manual page for each system call documents the possible return values of the
call, showing which value(s) indicate an error.
```c
fd = open(pathname, flags, mode); /* system call to open a file */
if (fd == -1) {
/* Code to handle the error */
}

if (close(fd) == -1) {
/* Code to handle the error */
}
```
When a system call fails, it sets the global integer variable errno to a positive value
that identifies the specific error.
* `#include <errno.h>`
```c
cnt = read(fd, buf, numbytes);
if (cnt == -1) {
  if (errno == EINTR)
    fprintf(stderr, "read was interrupted by a signal\n");
  else {
    /* Some other error occurred */
  }
}
```
POSIX: `errno` is defined in threaded programs
as a macro that expands into a function call that returns a modifiable lvalue.

Successful system calls and library functions never reset errno to 0, so this variable
may have a nonzero value as a consequence of an error from a previous call.
* SUSv3 permits a successful function call to set `errno` to a nonzero value
 * few functions do this
* when checking for an error, always first check if the function return value indicates an error, and only then
examine `errno` to determine the cause of the error.

A few system calls (e.g., `getpriority()`) can legitimately return –1 on success. To
determine whether an error occurs in such calls, we set `errno` to 0 before the call,
and then check it afterward.

A common course of action after a failed system call is to print an error message
based on the errno value. The `perror()` and `strerror()` library functions are provided
for this purpose:
```c
#include <stdio.h>
void perror(const char *msg);
```
e.g.
```c
fd = open(pathname, flags, mode);
if (fd == -1) {
  perror("open");
  exit(EXIT_FAILURE);
}
```
```c
#include <string.h>
char *strerror(int errnum); // Returns pointer to error string corresponding to errnum
// If errnum specifies an unrecognized error number, 
// strerror() returns a string of the form Unknown error nnn, or NULL
```

`perror()` and `strerror()` functions are locale-sensitive.

### Handling errors from library functions
Library functions could use `perror()`, `strerror` and `errno` or not.
* check `man` page.

## 3.5 Notes on the example programs in this book

## 3.6 Portability Issues
Most of the standard system data types have names ending in `_t`. Many of them are
declared in the header file `<sys/types.h>`, although a few are defined in other
header files.

# Chapter 4: File I/O: The Universal I/O Model
## 4.1 Overview
All system calls for performing I/O refer to open files using a *file descriptor*, a (usually
small) nonnegative integer.

By convention, most programs expect to be able to use the three standard file
descriptors:

| File descriptor | Purpose | POSIX name | `stdio` stream |
|:---------------:|:-------:|:----------:|:--------------:|
| `0`   |    standard input | `STDIN_FILENO` | *stdin*    |
| `1`   | standard output   | `STDOUT_FILENO`| *stdout*   |
| `2`   | standard error    | `STDERR_FILENO`| *stderr*   |

* the POSIX standard names are defined in `<unistd.h>`.

### Four system calls
`open()`, `read()`, `write()`, `close()`

## 4.2 Universality of I/O
the same four system calls can be used to perform I/O on all types of files.
* including devices such as terminals

Universality of I/O is achieved by ensuring that each file system and device driver
implements the same set of I/O system calls.

For specific features of a file system or device: use `ioctl()` system call.

## 4.3 Opening a file: `open()`
The `open()` system call either opens an existing file or creates and opens a new file.
```c
#include <sys/stat.h>
#include <fcntl.h>
int open(const char *pathname, int flags, ... /* mode_t mode */); //Returns file descriptor on success, or –1 o
```
* valid `flag`s:

|Access mode |Description|
|:----------:|:---------:|
|`O_RDONLY`  |  read-only|
|`O_WRONLY`  | write-only|
|`O_RDWR` |read-and-write|

SUSv3 specifies that if `open()` succeeds, it is guaranteed to use the lowest-numbered
unused file descriptor for the process and we can use this feature:
```c
if (close(STDIN_FILENO) == -1) /* Close file descriptor 0 */
  errExit("close");
  
fd = open(pathname, O_RDONLY); // 0 is unused, open() is guaranteed to open the file using that descriptor.
if (fd == -1)
  errExit("open");
```
### The `creat()` system call
Obsolete because modern UNIX has `fd = open(pathname, O_WRONLY | O_CREAT | O_TRUNC, mode);`

## 4.4 Reading from a file: `read()`
```c
#include <unistd.h>
ssize_t read(int fd, void *buffer, size_t count); // Returns number of bytes read, 0 on EOF, or –1 on error
```
The `buffer` must be at least `count` bytes long.
* System calls don’t allocate memory for buffers that are used to return information
to the caller. Instead, we **must** pass a pointer to a previously allocated
memory buffer of the correct size. This contrasts with several library functions
that **do** allocate memory buffers in order to return information to the caller.

`read()` reads any sequence of bytes from a file.
* won't stop at a `whitespace` or even `\0`
 * `printf()` requries a terminating `\0` to properly print strings. If using `read()`, we need to explicitly do this:
```c
char buffer[MAX_READ + 1];
ssize_t numRead;

numRead = read(STDIN_FILENO, buffer, MAX_READ);
if (numRead == -1)
  errExit("read");
  
buffer[numRead] = '\0';
printf("The input data was: %s\n", buffer);
```

## 4.5 Wring to a file: `write()`
```c
#include <unistd.h>
ssize_t write(int fd, void *buffer, size_t count); // Returns number of bytes written, or –1 on error
```
The kernel performs
buffering of disk I/O in order to reduce disk activity and expedite `write()` calls.

## 4.6 Closing a file: `close()`
```c
#include <unistd.h>
int close(int fd); // Returns 0 on success, or –1 on error
```
File descriptors are resources.
* should be released when done for long-lived programs.

Check return values of `close()` for diagnose purposes.
* closed twice?
* not open? etc.

## 4.7 Changing the file offset: `lseek()`
*file offset*: the location in the file at which the next `read()` or `write()` will commence.
* The first byte of the file is at offset 0.

```c
#include <unistd.h>
off_t lseek(int fd, off_t offset, int whence); // Returns new file offset if successful, or –1 on error
```

![whence](https://cloud.githubusercontent.com/assets/14265605/10826013/aa55c4d2-7e35-11e5-8726-fffea77aeffd.png)

The following
call retrieves the current location of the file offset without changing it:
`curr = lseek(fd, 0, SEEK_CUR);`

e.g.
```c
lseek(fd, 0, SEEK_SET); /* Start of file */
lseek(fd, 0, SEEK_END); /* Next byte after the end of the file */
lseek(fd, -1, SEEK_END); /* Last byte of file */
lseek(fd, -10, SEEK_CUR); /* Ten bytes prior to current location */
lseek(fd, 10000, SEEK_END); /* 10001 bytes past last byte of file */
```
Calling `lseek()` simply adjusts the kernel’s record of the file offset associated with a
file descriptor. 
* It does not cause any physical device access.
### file hole
Writing data at a position
beyond the previous end of the file creates a hole in the file. Reads from a file hole
return bytes containing zeros.

## 4.8 Operations Outside the Universal I/O Model: `ioctl()`

## 4.9 Summary
For each open file, the kernel maintains a file offset, which determines the
location at which the next read or write will occur. The file offset is implicitly
updated by reads and writes.

# Chapter 5: File I/O: Further Details

# Chapter 6: Processes

# Chapter 7: Memory Allocation

# Chapter 8: Users and Groups

# Chapter 9: Process Credentials

# Chapter 10: Time

# Chapter 11: System Limits and Options

# Chapter 12: System and Process Information

# Chapter 13: File I/O Buffering

# Chapter 14: File Systems

# Chapter 15: File Attributes

# Chapter 16: Extended Attributes
# Chapter 17: Access Control Lists
# Chapter 18: Directories and Links
# Chapter 19: Monitoring File Events 
# Chapter 20: Signals: Fundamental Concepts
# Chapter 21: Signals: Signal Handlers
# Chapter 22: Signals: Advanced Features
# Chapter 23: Timers and Sleeping
# Chapter 24: Process Creation
# Chapter 25: Process Termination
# Chapter 26: Monitoring Child Processes
# Chapter 27: Program Execution
# Chapter 28: Process Creation and Program Execution in More Detail
# Chapter 29: Threads: Introduction
# Chapter 30: Threads: Thread Synchronization
# Chapter 31: Threads: Thread Safety and Per-Thread Storage
# Chapter 32: Threads: Thread Cancellation
# Chapter 33: Threads: Further Details
# Chapter 34: Process Groups, Sessions, and Job Control
# Chapter 35: Process Priorities and Scheduling
# Chapter 36: Process Resources
# Chapter 37: Daemons
# Chapter 38: Writing Secure Privileged Programs
# Chapter 39: Capabilities
# Chapter 40: Login Accounting
# Chapter 41: Fundamentals of Shared Libraries
# Chapter 42: Advanced Features of Shared Libraries
# Chapter 43: Interprocess Communication Overview
# Chapter 44: Pipes and FIFOs
# Chapter 45: Introduction to System V IPC
# Chapter 46: System V Message Queues
# Chapter 47: System V Semaphores
# Chapter 48: System V Shared Memory 
# Chapter 49: Memory Mappings 
# Chapter 50: Virtual Memory Operations
# Chapter 51: Introduction to POSIX IPC
# Chapter 52: POSIX Message Queues 
# Chapter 53: POSIX Semaphores
# Chapter 54: POSIX Shared Memory
## Round_up and Round_down tricks

```c
// for integers
#define ROUND_UP(N, S) ((((N) + (S) - 1) / (S)) * (S))
#define ROUND_DOWN(N,S) ((N / S) * S)

// for power of 2, e.g. page alignment
#define PAGE_ROUND_DOWN(x) (((ULONG_PTR)(x)) & (~(PAGE_SIZE-1)))
#define PAGE_ROUND_UP(x) ( (((ULONG_PTR)(x)) + PAGE_SIZE-1)  & (~(PAGE_SIZE-1)) )

#define ROUND_UP_TO_A_PAGE(s) ((s & (PAGESIZE - 1)) ? (s & ~(PAGESIZE - 1)) + PAGESIZE : s)
```

# Chapter 55: File Locking
# Chapter 56: Sockets: Introduction
# Chapter 57: Sockets: UNIX Domain
# Chapter 58: Sockets: Fundamentals of TCP/IP Networks
# Chapter 59: Sockets: Internet Domains
# Chapter 60: Sockets: Server Design
# Chapter 61: Sockets: Advanced Topics
# Chapter 62: Terminals
# Chapter 63: Alternative I/O Models
# Chapter 64: Pseudoterminals
