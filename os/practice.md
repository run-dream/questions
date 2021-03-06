### 你能说一下操作系统的启动和初始化过程吗？

关键字: ***BIOS*** ***Bootload*** ***内核初始化***

回答:

1. BIOS 

   - 自检
   - 建立中断向量表和中断服务程序

2. Bootloader 如 Grub2

   - 加载操作系统启动所需的引导扇区
   - 并将CPU从实模式切换到保护模式
   - 建立分段分页
   - 打开地址线
   - 选择操作系统

3. Linux 内核初始化

   - 初始化0号进程

   - 初始化中断处理模块,内存管理模块，进程管理模块，文件系统

   - 其他初始化，包括初始化用户态的祖先进程1号进程以及内核态的祖先进程2号进程


### 请问进程和线程的概念和区别是什么

关键字: ***作用***  ***开销***  ***共享空间***

回答：

概念：

进程即进行中的程序，线程是进程中一个单一顺序的控制流。

线程是进程中的一部分，一个进程可以有多个线程，但线程只能存在于一个进程中。

区别：

1. 根本区别：

   进程是操作系统资源调度（文件，文件系统，信号，信号处理函数，内存）的基本单位

   线程是任务的调度（CPU）执行的基本单位

2. 开销方面：

   进程有自己的独立数据空间，程序之间的切换开销大；

   线程也有自己的运行栈和程序计数器，线程间的切换开销较小。

3. 共享空间：

   进程拥有各自独立的地址空间、资源，所以共享复杂，需要用IPC，但是同步简单。

   而线程共享所属进程的资源，因此共享简单，但是同步复杂，需要用加锁等措施。

在linux中，进程和线程都属于任务，不同的是创建进程的话，调用的系统调用是 fork，在 copy_process 函数里面，会将五大结构 files_struct、fs_struct、sighand_struct、signal_struct、mm_struct 都复制一遍，从此父进程和子进程各用各的数据结构。

而创建线程的话，调用的是系统调用 clone，在 copy_process 函数里面， 五大结构仅仅是引用计数加一，也即线程共享进程的数据结构。



### 线程池是不是越大越好

并不是配置越大越好，而是要根据任务的属性来进行划分：

- 如果是 CPU 密集型任务应当分配较少的线程，比如 CPU 个数相当的大小
- 如果是 IO 密集型任务，由于线程并不是一直在运行，所以可以尽可能的多配置线程，比如 CPU 个数 * 2 



### 请问函数调用堆栈的原理是什么

关键字: ***栈桢***  ***寄存器***

当调用一个函数时，将创建一个栈桢来支持该函数的执行。栈桢包含函数的局部变量和调用者传递给函数的参数。栈桢还包含管理信息，允许被调用的函数(被调用方)安全地返回给调用方。

计算机通过保存以及还原 CPU的栈指针和基址指针寄存器的相互配合来实现了指令的栈式调用。



### 怎么排查接口调用性能

关键词：***[vmstat](https://www.cnblogs.com/ggjucheng/archive/2012/01/05/2312625.html)*** ***ip***

指标:

- 进程

  - ***r*** 运行队列  当这个值超过了CPU数目，就会出现CPU瓶颈了

  - ***b*** 阻塞进程 

- CPU
  - **in** 每秒CPU的中断次数，包括时间中断
  - **cs** 每秒上下文切换次数, 太多的话考虑是不是NUMA导致，可以为特定进程绑定CPU
  - **us** 用户CPU时间
  - **sy** 系统CPU时间，如果太高，表示系统调用时间长，例如是IO操作频繁
  - **id**  空闲 CPU时间
  - **wt** 等待IO CPU时间
- 内存
  - **swpd** 虚拟内存已使用的大小
  - **free**   空闲的物理内存的大小
  - **buff**   Linux/Unix系统是用来存储，目录里面有什么内容，权限等的缓存
  - **cache** cache直接用来记忆我们打开的文件,给文件做缓冲
- 磁盘IO
  - **si**  每秒从磁盘读入虚拟内存的大小，如果这个值大于0，表示物理内存不够用或者内存泄露了，
  - **so**  每秒虚拟内存写入磁盘的大小，如果这个值大于0，同上。
  - **bi**  块设备每秒接收的块数量，这里的块设备是指系统上所有的磁盘和其他块设备，默认块大小是1024byte
  - **bo** 块设备每秒发送的块数量，
- [网络](https://segmentfault.com/a/1190000022275523)
  - ***nethogs*** 按进程查看流量占用
  - ***iptraf*** 按连接/端口查看流量
  - ***ifstat*** 按设备查看流量
  - ***ethtool*** 诊断工具
  - ***tcpdump*** 抓包工具
  - ***ss*** 连接查看工具



### 物理内存和虚拟内存的概念

- **物理内存：** 真实的内存，就是我们常说的那个4G、8G、16G的内存条。 是CPU 实际操作的内存。
- **虚拟内存：** 是操作系统对物理内存的抽象，提供给程序访问。可以实现进程间隔离。



### 虚拟文件系统的机制

虚拟文件系统（VFS）是物理文件系统与服务之间的一个接口层，它对Linux的每个文件系统的所有细节进行抽象，使得不同的文件系统在[Linux](https://baike.baidu.com/item/Linux)核心以及系统中运行的其他进程看来，都是相同的。

ulimit



### 文件写入的流程

- 直接IO
- 缓存IO



### 信号和中断机制

信号是中断机制的一种模拟。

中断分为软中断和硬中断，中断产生以后，查中断表，执行中断程序。

信号产生以后，也有信号处理函数。




### TCP/IP分层模型

- 物理层
- 数据链路层
- 网络层
- 传输层
- 应用层



### 三次握手 四次挥手

