# Python Multiple Processing & Threading
<!--toc-->
**[Optional Operating System Services](https://docs.python.org/2/library/someos.html)**  
The modules described in this chapter provide interfaces to operating system features that are available on selected operating systems only. The interfaces are generally modeled after the Unix or C interfaces but they are available on some other systems as well (e.g. Windows or NT). 
## [select](https://docs.python.org/2/library/select.html): Waiting for I/O completion
**[select](http://www.cnblogs.com/Anker/archive/2013/08/14/3258674.html), [poll](http://www.cnblogs.com/Anker/archive/2013/08/15/3261006.html), [epoll](http://www.cnblogs.com/Anker/archive/2013/08/17/3263780.html) 的区别**   
> select, poll, epoll都是IO多路复用的机制。I/O多路复用就通过一种机制，可以监视多个描述符，一旦某个描述符就绪（一般是读就绪或者写就绪），能够通知程序进行相应的读写操作。但select，poll，epoll本质上都是同步I/O，因为他们都需要在读写事件就绪后自己负责进行读写，也就是说这个读写过程是阻塞的，而异步I/O则无需自己负责进行读写，异步I/O的实现会负责把数据从内核拷贝到用户空间。  

```
IO多路复用是指内核一旦发现进程指定的一个或者多个IO条件准备读取，它就通知该进程。IO多路复用适用如下场合：
  - 当客户处理多个描述字时（一般是交互式输入和网络套接口），必须使用I/O复用。
  - 如果一个TCP服务器既要处理监听套接口，又要处理已连接套接口，一般也要用到I/O复用。
  - 如果一个服务器即要处理TCP，又要处理UDP，一般要使用I/O复用。
  - 如果一个服务器要处理多个服务或多个协议，一般要使用I/O复用。
与多进程和多线程技术相比，I/O多路复用技术的最大优势是系统开销小，系统不必创建进程/线程，
也不必维护这些进程/线程，从而大大减小了系统的开销。  
```

#### [`select`](https://docs.python.org/2/library/select.html)
select的调用过程如下所示： 
![](http://images.cnitblog.com/blog/305504/201308/17201205-8ac47f1f1fcd4773bd4edd947c0bb1f4.png)
```
1. 使用copy_from_user从用户空间拷贝fd_set到内核空间
2. 注册回调函数__pollwait
3. 遍历所有fd，调用其对应的poll方法（对于socket，这个poll方法是sock_poll，
   sock_poll根据情况会调用到tcp_poll,udp_poll或者datagram_poll）
4. 以tcp_poll为例，其核心实现就是__pollwait，也就是上面注册的回调函数。
5. __pollwait的主要工作就是把current（当前进程）挂到设备的等待队列中，不同的设备有不同的等待队列，
   对于tcp_poll来说，其等待队列是sk->sk_sleep（注意把进程挂到等待队列中并不代表进程已经睡眠了）。
   在设备收到一条消息（网络设备）或填写完文件数据（磁盘设备）后，会唤醒设备等待队列上睡眠的进程，这时current便被唤醒了。
6. poll方法返回时会返回一个描述读写操作是否就绪的mask掩码，根据这个mask掩码给fd_set赋值。
7. 如果遍历完所有的fd，还没有返回一个可读写的mask掩码，则会调用schedule_timeout是调用select的进程（也就是current）进入睡眠。
   当设备驱动发生自身资源可读写后，会唤醒其等待队列上睡眠的进程。如果超过一定的超时时间（schedule_timeout指定），
   还是没人唤醒，则调用select的进程会重新被唤醒获得CPU，进而重新遍历fd，判断有没有就绪的fd。
8. 把fd_set从内核空间拷贝到用户空间。
```
Select的几大缺点：  
- 每次调用select，都需要把fd集合从用户态拷贝到内核态，这个开销在fd很多时会很大
- 同时每次调用select都需要在内核遍历传递进来的所有fd，这个开销在fd很多时也很大
- select支持的文件描述符数量太小了，默认是1024

#### [poll](http://www.cnblogs.com/Anker/archive/2013/08/15/3261006.html) 
poll的实现和select非常相似，只是描述fd集合的方式不同，poll使用pollfd结构而不是select的fd_set结构，其他的都差不多。  

#### [epoll](http://www.cnblogs.com/Anker/archive/2013/08/17/3263780.html) 
> epoll既然是对select和poll的改进，就应该能避免上述的三个缺点。那epoll都是怎么解决的呢？在此之前，我们先看一下epoll和select和poll的调用接口上的不同，select和poll都只提供了一个函数——select或者poll函数。而epoll提供了三个函数，epoll_create,epoll_ctl和epoll_wait，epoll_create是创建一个epoll句柄；epoll_ctl是注册要监听的事件类型；epoll_wait则是等待事件的产生。   

- 对于第一个缺点，epoll的解决方案在epoll_ctl函数中。每次注册新的事件到epoll句柄中时（在epoll_ctl中指定EPOLL_CTL_ADD），会把所有的fd拷贝进内核，而不是在epoll_wait的时候重复拷贝。epoll保证了每个fd在整个过程中只会拷贝一次。  
- 对于第二个缺点，epoll的解决方案不像select或poll一样每次都把current轮流加入fd对应的设备等待队列中，而只在epoll_ctl时把current挂一遍（这一遍必不可少）并为每个fd指定一个回调函数，当设备就绪，唤醒等待队列上的等待者时，就会调用这个回调函数，而这个回调函数会把就绪的fd加入一个就绪链表）。epoll_wait的工作实际上就是在这个就绪链表中查看有没有就绪的fd（利用schedule_timeout()实现睡一会，判断一会的效果，和select实现中的第7步是类似的）。  
- 对于第三个缺点，epoll没有这个限制，它所支持的FD上限是最大可以打开文件的数目，这个数字一般远大于2048,举个例子,在1GB内存的机器上大约是10万左右，具体数目可以cat /proc/sys/fs/file-max察看,一般来说这个数目和系统内存关系很大。

#### 总结
> select，poll实现需要自己不断轮询所有fd集合，直到设备就绪，期间可能要睡眠和唤醒多次交替。而epoll其实也需要调用epoll_wait不断轮询就绪链表，期间也可能多次睡眠和唤醒交替，但是它是设备就绪时，调用回调函数，把就绪fd放入就绪链表中，并唤醒在epoll_wait中进入睡眠的进程。虽然都要睡眠和交替，但是select和poll在“醒着”的时候要遍历整个fd集合，而epoll在“醒着”的时候只要判断一下就绪链表是否为空就行了，这节省了大量的CPU时间。这就是回调机制带来的性能提升。  
> select，poll每次调用都要把fd集合从用户态往内核态拷贝一次，并且要把current往设备等待队列中挂一次，而epoll只要一次拷贝，而且把current往等待队列上挂也只挂一次（在epoll_wait的开始，注意这里的等待队列并不是设备等待队列，只是一个epoll内部定义的等待队列）。这也能节省不少的开销。

#### select api  
```python
exception select.error -> Return a pair containing the numeric error code from errno and the corresponding string
select.epoll([sizehint=-1]) -> an edge polling object
select.poll() -> a polling object
  # which supports registering and unregistering file descriptors, and then polling them for I/O events
select.select(rlist, wlist, xlist, timeout=None) -> (rlist, wlist, xlist)

select(...) is a straightforward interface to the Unix select() system call. The first three arguments are 
sequences of ‘waitable objects’: either integers representing file descriptors or objects with a parameterless 
method named fileno() returning such an integer:

rlist: wait until ready for reading
wlist: wait until ready for writing
xlist: wait for an “exceptional condition” (see the manual page for what your system considers such a condition)

'timeout' argument specifies a time-out as a floating point number in seconds. 
When timeout is omitted: the function blocks until at least one file descriptor is ready. 
When timeout is zero: specifie poll never blocks.
```
#### poll api  
```python
poll.register(fd[, eventmask]) # Register a file descriptor with the polling object.
poll.modify(fd, eventmask) # Modifies an already registered fd. 
poll.unregister(fd) # Remove a file descriptor being tracked by a polling object.
poll.poll([timeout]) # Polls the set of registered file descriptors, and returns a possibly-empty list 
                     # containing (fd, event) 2-tuples for the descriptors that have events or errors to report. 
```
####epoll api  
```python
epoll.close() # Close the control file descriptor of the epoll object.
epoll.fileno() # Return the file descriptor number of the control fd.
epoll.fromfd(fd) # Create an epoll object from a given file descriptor.

epoll.register(fd[, eventmask]) # Register a fd descriptor with the epoll object.
        # Note Registering a file descriptor that’s already registered raises 
        # an IOError – contrary to Polling Objects‘s register.
epoll.modify(fd, eventmask) # Modify a register file descriptor.
epoll.unregister(fd) # Remove a registered file descriptor from the epoll object.
epoll.poll([timeout=-1[, maxevents=-1]]) # Wait for events. timeout in seconds (float)
```

## [threading](https://docs.python.org/2/library/threading.html): Higher-level threading interface
> **CPython implementation detail**: In CPython, due to the Global Interpreter Lock, only one thread can execute Python code at once (even though certain performance-oriented libraries might overcome this limitation). If you want your application to make better use of the computational resources of multi-core machines, you are advised to use multiprocessing. However, threading is still an appropriate model if you want to run multiple I/O-bound tasks simultaneously.

functions and objects:  
```python
threading.active_count()
threading.activeCount() -> Return the number of Thread objects currently alive. 
    # The returned count is equal to the length of the list returned by enumerate().
threading.enumerate() -> Return a list of all Thread objects currently alive.
    # The list includes daemonic threads, dummy thread objects created by current_thread(), and the main thread. 
    # It excludes terminated threads and threads that have not yet been started.
threading.current_thread()
threading.currentThread() -> Return the current Thread object, corresponding to the caller’s thread of control.

threading.Thread()
threading.Condition()
threading.Event()
threading.Lock()
threading.RLock()
threading.Semaphore([value])
threading.BoundedSemaphore([value])
threading.Timer()
exception threading.ThreadError # many interfaces use RuntimeError instead of ThreadError.

threading.settrace(func) # Set a trace function for all threads started from the threading module
    # The func will be passed to sys.settrace() for each thread, before its run() method is called.
threading.setprofile(func) # Set a profile function for all threads started from the threading modul
    # The func will be passed to sys.setprofile() for each thread, before its run() method is called.
```
#### **[Thread Object](https://docs.python.org/2/library/threading.html#thread-objects)**  
- A new class inherit Thread, only need override the __init__() and run() methods 
- Once a thread object is created, its activity must be started by calling the thread’s start() method. This invokes the run() method in a separate thread of control.
- Once the thread’s activity is started, the thread is considered ‘alive’. It stops being alive when its run() method terminates – either normally, or by raising an unhandled exception. The is_alive() method tests whether the thread is alive.
- Other threads can call a thread’s join() method. This blocks the calling thread until the thread whose join() method is called is terminated.

Thread Object API:  
```python
threading.Thread(group=None, target=None, name=None, args=(), kwargs={}) : constructor
    # `group`: should be None; reserved for future extension
    # `target`: the callable object to be invoked by the run() method.
    # `name`: default "Thread-N"
    # `args`: the argument tuple for the target invocation. Defaults to ().
    # `kwargs`:  a dictionary of keyword arguments for the target invocation. Defaults to {}.
    
start() 
run()
join([timeout])
name # Multiple threads may be given the same name
ident # Id of this thread or None if the thread has not been started. This is a nonzero integer.
is_alive()
isAlive()
daemon # A boolean value indicating whether this thread is a daemon thread (True) or not (False)
isDaemon()
```
#### **[Lock](https://docs.python.org/2/library/threading.html#lock-objects)**  
- A primitive lock is in one of two states, “locked” or “unlocked”. It is created in the unlocked state. 
- It has two basic methods, acquire(blocking=1) and release(). 
- When more than one thread is blocked in acquire() waiting for the state to turn to unlocked, only one thread proceeds when a release() call resets the state to unlocked; which one of the waiting threads proceeds is not defined, and may vary across implementations.

#### **[RLock](https://docs.python.org/2/library/threading.html#rlock-objects)**  
- A reentrant lock is a synchronization primitive that may be acquired multiple times by the same thread. 
- In the locked state, some one thread owns the lock; in the unlocked state, no thread owns it.
- acquire(blocking=1)/release() call pairs may be nested;
- only the final release() resets the lock to unlocked and allows another thread blocked in acquire() to proceed.

#### **[Condition](https://docs.python.org/2/library/threading.html#condition-objects)**  
- A condition variable is always associated with some kind of lock
- acquire(\*args)/release() call the associated lock
- wait([timeout])/notify(n=1)/notifyAll() must only be called when the calling thread has acquired the lock, otherwise a RuntimeError is raised.
- wait([timeout]): releases the underlying lock, and then blocks until it is awakened by a notify() or notifyAll() call for the same condition variable in another thread, or until the optional timeout occurs.
- wait([timeout]): Once awakened or timed out, it re-acquires the lock and returns.
- notify(n=1): By default, wake up one thread waiting on this condition, if any. If the calling thread has not acquired the lock when this method is called, a RuntimeError is raised.
- notify(n=1): This method wakes up at most n of the threads waiting for the condition variable; it is a no-op if no threads are waiting.
- notify(n=1): it’s not safe to rely on this behavior. A future, optimized implementation may occasionally wake up more than n threads.
- notifyAll(): Wake up all threads waiting on this condition. 
- Note: an awakened thread does not actually return from its wait() call until it can reacquire the lock. Since notify() does not release the lock, its caller should.

## [multiprocessing](https://docs.python.org/2/library/multiprocessing.html): Process-based “threading” interface 
> multiprocessing is a package that supports spawning processes using an API similar to the threading module. The multiprocessing package offers both local and remote concurrency, effectively side-stepping the `GIL` by using subprocesses instead of threads. Due to this, the multiprocessing module allows the programmer to fully leverage multiple processors on a given machine. It runs on both Unix and Windows.

- Old Class: Lock, RLock, Condition, Event, Semaphore, BoundSemaphore, ...
- New Class: Process, Queue, Pool, Pipe, Manager, ...
- Shared Memory: Value, Array


## [mmap](https://docs.python.org/2/library/mmap.html): Memory-mapped file support

