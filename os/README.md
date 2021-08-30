知识体系:

- 硬件

  - CPU
    - 物理核 / 逻辑核
    - 寄存器 / ALU
    - 缓存 L1, L2, L3
    - DMA
  - 内存
    - DRAM
  - 外设
    - 块设备 磁盘
    - 字符设备 鼠标

- 系统初始化

  - BIOS

    - 自检
    - 中断向量和中断服务

  - bootloader grub2 

    - 加载启动盘
    - CPU虚实模式转换

  - 内核初始化

    - 其他模块初始化 内存，文件系统

    - 进程管理初始化 
      - 1号进程 用户进程祖先 Ring0 低权限 
      - 2号进程 内核进程祖先 Ring3 高权限

  - 系统调用

    - 中断
      - 软中断
      - 硬件中断

- 进程管理

  - ELF

    分类：

    - 可重定位 .o
    - 可执行
    - 共享对象 .so

    构成:

    - .text
    - .data
    - .rodata
    - .bss
    - .symtab
    - .strtab

  - 进程
    - exec
    - folk
      - copy on write
    - 数据结构 task_struct
  - 线程
    - 作用：并行执行
    - 数据:
      - 线程栈 默认8M
      - 进程共享的全局数据
      - 线程私有数据
    - mutex
    - 数据结构 task_struct + thread_info
  - 调度
    - 实时调度
      - FIFO
      - RR
      - DEADLINE
    - 普通调度
      - NORMAL
      - BATCH
      - IDLE
    - CPU的结构存放调度的队列

- 内存管理

  - 物理地址和虚拟地址
  - 分段，分页影射方式
  - NUMA
  - 伙伴系统 + slub allocator
  - kswapd

- 文件系统

  - 硬盘文件系统

  - 虚拟文件系统

    - mount

  - 文件缓存

  - 关键词

    - 簇 
    - 扇区 
    - 块 

    - inode 索引
    - dentry 目录项
    - file description  句柄  文件描述符号

- 输入输出系统

  - 字符设备
  - 块设备
  - 驱动

- 进程间通信

  - 信号
  - 管道
    - 命名管道
    - 匿名管道
  - 共享内存
  - 信号量
    - P
    - V

- 网络系统

  - socket
  - 网络包

- 虚拟化

  - 计算虚拟化
  - 存储虚拟化
  - 网络虚拟化

- 容器化

  - 容器

    - docker

    - cgroup
    - namespace

  - 数据中心

    - k8s





### 参考资料

1. [现代操作系统](https://github.com/ShineFan/awesome-programming-books-1/blob/master/ComputerSystem/%E7%8E%B0%E4%BB%A3%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%EF%BC%88%E7%AC%AC3%E7%89%88%EF%BC%89.pdf)