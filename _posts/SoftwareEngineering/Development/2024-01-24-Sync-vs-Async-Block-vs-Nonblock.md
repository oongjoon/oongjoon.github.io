---
title : "Synchronize,Asynchronize vs Non-blocking,Blocking: Aren’t Synchronize and Blocking the same thing?"

excerpt: "I/O , CPu overlaping"

categories:
  - Development
tags:
  - [SoftwareEngineering , Resource Management]
# classes : wide
toc: true
toc_sticky: true
---

# Synchronize,Asynchronize,Non-blocking,Blocking??
Recently, while reading the pytorch docs, a deep learning framework, I came across the word blocking. I thought the word blocking didn't mean anything, but while looking up related information on the forum, I saw that NVIDIA's Patrick had provided a link for deeper understanding. It was a post from NVIDIA, and blocking, non-blocking, synchronize, and asynchronize were repeated dozens of times, but I didn't understand the content at all. While searching for blocking and non-blocking, I found out that this concept is related to controlling the behavior of processes. This is the first time I learned that synchronize, asynchronize, blocking, and non-blocking are different concepts. While looking at related posts, I realized that I did not understand synchronize and asynchronize in detail. As an engineer, I feel like I need to know motion control because it's important for designing processes.
## Sync vs Async

![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217840&authkey=%21APnBHuRo6P7JPu0&width=435&height=250)
The picture above shows Synchronous and Asynchronous. Looking at the picture, the difference is whether process A is waiting for the action of process B. Let us define this generally.
  1. synchronization is the task of coordinating multiple of processes to join up or handshake at a certain point, in order to reach an agreement or commit to a certain sequence of action.
  2. Asynchrony, in computer programming, refers to the occurrence of events independent of the main program flow and ways to deal with such events.
   
Sync and Async are predicted to be opposite concepts because they have the letter a in front of them. However, I don't think the explanation is contradictory. Since the explanation in the reference below is easy to understand, I will write it down as is.

1. Synchronization is when process A, which called process B, waits carefully for the processing results of process B.
2. In asynchronization, process A, which called process B, performs its own actions without waiting for the processing results of process B.

The fact that process A, which is called by other processes, cares about each other's order or shared resources in a specific part can be seen as process A caring about the processing results of the remaining processes.
The main program, i.e. process A and how to handle certain events, i.e. process B occurs without paying attention to each other. This means that process A performs its own actions without caring about the processing results of process B.
## Block vs Nonblock
blocking, non blocking are concepts used in I/O API. Operations such as reading or writing data are I/O operations.
1. Blocking: approach to I/O would be to start the access and then wait for it to complete.
2. Non-locking: a form of input/output processing that permits other processing to continue before the I/O operation has finished.

Compared to the previous sync and async, the explanation explicitly has the opposite nature. When called, blocking does not transfer control to the calling process, while non-blocking transfers control to the calling process. This is simply defined as follows: This time too, I borrowed the explanation from the reference blog.

1. Blocking: The called function does not hand over control to the calling function until its operation is completed.
2. Non-blocking: Control is immediately transferred to the calling function even though the called function has not completed its operation.

## Four patterns
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217846&authkey=%21AIFoSWztVEf8__U&width=280&height=230)  

This time, we will look at four cases that combine sync, async, blocking, and non-blocking. The examples below are taken from IBM's blog.
###Synchronous blocking I/O
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217845&authkey=%21ACDJFjlBed31WXw&width=768&height=493)
The user application makes a system call, and the system call blocks the application until the system call is completed.
It is said to be efficient because it waits for the response of the system call without taking up CPU.
When the read system call is called, the system call blocks the application and the context switches to kernel.

### Synchronous non-blocking I/O
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217849&authkey=%21APD7PUTY2InbhPo&width=546&height=392)
In this case, the user application requests a system call, but read returns an error and the call is not performed. In most cases, the user application must read data to perform the next task, so countless system calls are made until read is performed.
Since data becomes accessible in kerenl and the time it takes for the read called by the user to return becomes longer, latency in IO increases and overall throughput decreases.

### Asynchronous blocking I/O
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217850&authkey=%21AOh9xd1HXab2_wU&width=622&height=408)
In this case, the user application requests a system call called select. select tells you whether the file descriptor is writable or readable. It will tell you about not only one file descriptor, but also about file descriptors less than or equal to 1024. From here, the user application can move on to the next task. That is, asynchronous. However, you will have to wait because select blocks the user application.
Because this select is inefficient, it is not recommended for use in high-performance I/O.

### Asynchronous non-blocking I/O (AIO)
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217848&authkey=%21AKSg7EXa9R4YtLk&width=559&height=388)
The asynchronous non-blocking I/O model is executed by overlapping the called process and I/O work. When the user application makes a system call called aio_read, you will immediately receive a return indicating that the read was successful. While the user application is executing another process, I/O reading is performed in the background at the same time. When the read response arrives at the user application, a signal or callback occurs and data is transmitted from the kernel to the user application.
Overlapping computation and I/O processing in a single process for multiple I/O requests takes full advantage of the difference between process processing speed and I/O processing speed. While one or more slow I/O requests are being processed, the CPU can handle other tasks in the process. Once the I/O has been processed, the CPU can perform actions on it and new I/O requests can be performed at the same time.
## Conclusion

I realized that when writing a program as code, I was only interested in whether it performed the desired operation, but did not pay much attention to how efficiently the device utilized the computer's resources. I took an Operating System class during my undergraduate days, but I didn't really think much about how to actually incorporate it into my program. From now on, I'll think about how to incorporate the knowledge I learned from Operating System into my program and additionally study Operating System in more depth. 

## References

https://musma.github.io/2019/04/17/blocking-and-synchronous.html

https://developer.ibm.com/articles/l-async/

https://en.wikipedia.org/wiki/Asynchronous_I/O

https://en.wikipedia.org/wiki/Asynchrony_(computer_programming)

https://en.wikipedia.org/wiki/Synchronization_(computer_science)