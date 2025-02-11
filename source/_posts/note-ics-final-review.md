---
title: ICS 期末复习
date: 2024-12-22 21:31:19
tags:
  - ICS
  - 笔记
  - 本科课程
categories: [笔记, 本科课程]
---

## Chap. 7 Linking

关于编译器驱动程序：

- `cpp [other args] main.c /tmp/main.i` 是预处理器，`main.i` 是 ASCII 中间文件。
- `cc1 /tmp.main.i -Og [other args] -o /tmp/main.s` 是编译器，`main.s` 是 ASCII 汇编代码。
- `as [other args] -o /tmp/main.o /tmp/main.s` 是汇编器，`main.o` 是**可重定位目标文件**
- `ld` 是链接器。

---

ELF 的各个节：

- 0：ELF header
- 11：节头部表
- 4：`.bss` 在**目标文件中不占空间**，在运行时分配。
- 5：`.symtab`，不一定要 `-g` 才能得到。
- `.debug`，`.line` 才是需要 `-g` 的。前者存*局部变量*。

有三个伪节（在可执行文件中不存在）

- `COMMON`：**未初始化的全局变量**
- `UNDEF`：未定义的外部符号
- `ABS`：不该被重定位的符号

有多个弱符号重名的时候会随便选一个。

---

静态库编译：每个函数弄一个 `.o`，然后打包成 `.a`。用 `ar` 打包。

链接器解析引用的流程：

- 定义可重定位目标文件的集合 $E$，未解析符号 $U$，已定义符号 $D$
- 对于每个文件，若是 `.o`，正常进行维护
- 若是 `.a`，尝试寻找包含 $U$ 中符号定义的 `.o` 成员，找到了就加进去 $E$，然后相应更新 $U$ 和 $D$ 

`.o` 的顺序无冇所谓，但是 `.a` 的需要注意。

把依赖关系图画出来，按着有向边的方向走一遍。

---

重定位的步骤：

- 重定位节与符号定义：把所有 `.o` 的同类型节 merge 到一起，然后把运行时内存地址赋给节、符号。
- 重定位节的符号引用：字面意思

代码的重定位条目在 `.rel.text`，*已初始化数据的重定位条目在 `.rel.data`*。

```
foreach section s {
  foreach relocation entry r {
    refptr = s + r.offset
    // 此时已经知道了节的地址 ADDR(s) 和对象的地址 ADDR(r.symbol)
    // refptr 是我们要修改的地址（在这个地址引用了符号）
    if (r.type == R_X86_64_PC32) {
      // PC相对寻址
      refaddr = ADDR(s) + r.offset // 这是 ref 的运行时地址
      *refptr = (unsigned) (ADDR(r.symbol) + r.addend - refaddr)
      // r.addend = 4
      // r.addend 实际上等于 %rip - refaddr，%rip 就是下一条指令的地址
    } else if (r.type == R_X86_64_32) {
      *refptr = (unsigned) (ADDR(r.symbol) + r.addend)
      // 这种情况 r.addend = 0，所以其实就是直接把地址弄上去
    }
  }
}
```

---

可执行目标文件多了一个 `.init` 节，里面有 `_init` 函数。由于不需要重定位所以没有 `.rel` 了。

代码段总从 `0x400000` 开始。用户栈从 $2^{48}-1$ 开始往下增长。

注意到由于 ASLR（地址空间布局随机化）的存在，绝对地址会变，相对的不变。

加载器过程：`_start`->`__libc_start_main`->`main`。

---

动态链接：共享目标文件 `.so`，里面有个 **`interp` 节，包含了动态链接器的路径名，动态链接器 `ld.so` 本身也是个共享目标文件**。

---

TODO：PIC, GOT, PLT

## Chap. 8 ECF

异常表，异常表基址寄存器 + 84*异常号

异常处理程序运行在**内核态**

四种异常：

- 中断 interrupt：异步，总是返回到下一条。e. g. I/O
- 陷阱 trap：同步，有意的异常，**系统调用**
- 故障 fault：**可能返回到当前**，常见的缺页，潜在可恢复
- 终止 abort：**不会返回**，致命错误，硬件的那种

---

系统调用：

**%rax：系统调用号**以及返回值。出现故障时等于负的 errno

参数的顺序与过程调用不同：%rdi,%rsi,%rdx,**%r10**,%r8,%r9。**寄存器 %rcx 和 %r11 会被破坏**。

---

进程上下文：内存中的代码与数据，栈，寄存器，PC，环境变量，opened fd

每个进程有自己的私有地址空间，代码段从 `0x400000` 开始。顶部空间给内核。

内核模式：控制寄存器中 mode bit，可以停止处理器，改变模式位，发起 IO 操作，访问任何地址。

用户模式引用内核区的地址会导致**保护故障**。

---

四种导致进程停止的信号：

- SIGSTOP
- SIGTSTP：terminal stop
- SIGTTIN：TTY input for bg
- SIGTTOU：TTY output for bg

后两者的意思是：后台进程想要通过终端进行 I/O 操作的话必须要停下来直到转为前台。

---

`waitpid` 的 options：

- 默认：挂起，等待集合中任意子进程**终止**
- `WNOHANG`：不挂起，如果没有的话，**立即返回 0**
- `WUNTRACED`：多关心**停止**的进程
- `WCONTINUED`：多关心**收到 SIGCONT 然后继续的进程**

可以用按位或给他们或起来。

> wait 不等子进程的子进程

---

execve 的时候

从栈顶到栈底依次是：main 未来的栈帧 -> libc_start_main 的栈帧 -> argv[0], argv[1], ... -> envp[0], envp[1], ... -> argv 的内容 -> envp 的内容（字符串）

---

SIGKILL 和 SIGSTOP 不能被捕获/忽略。

内核给每个进程维护一个 pending 位向量。接收到一个就把 pending 的对应位置 1，接收一个就置 0。

**进程**可以通过 signal 函数修改收到信号的行为，注意只管当前进程不管父进程。

一个 handler 在运行的时候可能接收到信号，那么就会转到另一个 handler。

- 在 handler 里面**保存并恢复 `errno`**
- 如果要访问共享全局数据结构，**暂时阻塞所有信号**
- 用 `volatile` 声明全局变量（不会被缓存到寄存器中）
- 用 `sig_atomic_t` 声明
- 不要利用 handler 做给信号计数之类的工作。一来要视为来了一车。`while (waitpid)`

---

**异步信号安全**：**要么可重入（不引用线程间的共享数据），要么不能被信号处理程序中断**

在 handler 中唯一安全的输出方法是用 `write`。

---

显式等待信号（shell 的父进程等待子进程结束 SIGCHLD）

基本思路是：

```c
volatile sig_atomic_t pid;

void sigchld_handler(int s) {
    int olderrno = errno;
    pid = waitpid(-1, NULL, 0);
    errno = errno;
}

int main() {
    ...
    while (1) {
        Sigprocmask(SIG_BLOCK, &mask, &prev); // 阻塞 SIGCHLD
        if (Fork() == 0) {
            exit(0);
        }
        // parent
        pid = 0;
        Sigprocmask(SIG_SETMASK, &prev, NULL);
        // 等待 SIGCHLD
        while (!pid);
        
        ...
    }
}
```

但是这个 `while` 很占用资源。如果改成 `while(!pid) pause();` 会**竞争**（`while` 测试后且 `pause` 执行前收到信号的话，`pause` 就永远 `pause` 了）

用 `sleep(1)` 替代，也很浪费时间。

合理的方法是用 `sigsuspend(mask)`（阻塞 `mask` 的信号，收到信号后若是被杀就直接杀，若是 handler 就执行完后返回）。

改成 `while (!pid) sigsuspend(&prev)` 就可以了。

---

非本地跳转。

`setjmp(env)` 在 `env` 保存当前调用环境（包括 PC，%rsp 和通用目的寄存器），**返回值不能被赋值给变量但是可以用在 `switch` 或条件语句的测试**。`longjmp` 从 `env` 中恢复调用环境，然后触发一个从最近一次初始化 `env` 的 `setjmp` 的返回。

前者被调用一次返回多次，后者不返回。

## Chap. 9 VM

Core i7：四级页表。有 TLB（注意 TLB 是可以直接搞到页面基地址的）

PTE 里的一些位：

- 第一级 PTE 独有的：第七位 PS，表明页面大小是 4KB 还是 4MB。

- > zzy 的小班课 PPT 进行了勘误，这个应该是第三级的，代表是大页还是小页

- 第四级 PTE 独有的：第六位 D，dirty bit。**大题对 PTE 修改之后是需要更新 dirty bit 的**

- 其他：U/S 是用户/内核，R/W 是可读/只读，XD 是禁止执行。

- 不使用最低的 12 位，所以物理页表和物理内存页都要 4KB 对齐。

注意到 CI + CO = 12 = VPO，所以可以**并行**翻译 VPN 和在 cache 中查询，然后用 PPN 和 CT 来对比。

---

缺页的时候可能发生的事情：

- 访问不存在的页面：**段错误**
- 写一个只读的页面（权限问题）：**保护异常**
- 正常缺页

---

COW 的时候可能发生的事情：**保护故障**，尝试写私有区域的某个页面，这个时候触发 COW，然后重新执行写操作。

映射到匿名文件？.bss

应用级内存映射：`void* mmap(void *start, size_t length, int prot, int flags, int fd, off_t offset)`

`start` 通常是 NULL，`prot` 代表这段内存区域的权限位，`flags` 可以是 `MAP_ANON`（匿名文件），`MAP_PRIVATE`（私有，COW），`MAP_SHARED` 共享对象。

`munmap(void *start, size_t length)` 可以对 `mmap` 出来的东西进行释放。

---

`malloc` 返回的块地址在 32 位下是 8 的倍数，在 64 位下是 16 的倍数。

外部碎片：没分配的小碎块

内部碎片：有效载荷<块大小

**吞吐率：next fit > first fit**

**util: first fit > next fit**

显式链表：按照地址维护比 LIFO 利用率会高

## Chap. 10 SysIO

Unix 万物皆文件，输入输出等价于对文件的读写。

每次打开文件都有唯一的非负 fd 与其对应。

- 0 是 STDIN
- 1 是 STDOUT
- 2 是 STDERR

所以正常而言 fd 是从 3 开始分配的，**每次分配最小的未被占用的**

对于每个打开的文件，内核维护一个**文件位置 $k$**，可以通过 `seek` 显式设置。

读文件：**文件结尾没有显式的 EOF 记号**

---

文件的分类：普通文件，目录文件，socket。

---

打开文件：`int open(char* filename, int flags, mode_t mode)`，返回一个 fd。

`flags` 参数，也是位向量掩码。

- `O_RDONLY`：只读
- `O_WRONLY`：只写
- `o_RDWR`：可读可写

额外指示：

- `O_CREAT`：不存在就创建空的
- `O_TRUNC`：存在就夹断
- `O_APPEND`：写之前把文件位置设置到最尾

进程上下文包含 **`umask`**，`mode` 可以是`S_I R/W/X USR/GRP/OTH`，用 `open` **创建新文件**的时候，其权限位会被设置成 `mode & ~umask`。默认情况下是拥有者有读写权限，其他用户只读。

用 `close(fd)` 可以关闭一个 fd，*关一个已关闭的会出错*

> 做题的时候注意 open 的参数是什么，是 `O_TRUNC` 或者 `O_APPEND` 的话都要格外注意。

---

RIO 包健壮读写

- **无缓冲的 I/O 函数**
- **带缓冲的输入函数**，没有带缓冲输出。

> 缓冲区的意义是减少系统调用 `read` 的次数。每次系统调用都要陷入内核，比用户调用函数慢。

不要用 `rio_readline(b)` 来读二进制文件。 

---

`stat` 和 `fstat` 可以读取文件的 metadata。

`st_size` 文件大小，`st_mode` 是许可位和类型，可以用相关的宏判定文件类型。

---

`opendir(name)` 返回指向目录流的指针。

`readdir` 调用会返回指向下一个目录项的指针。

---

内核用三个数据结构维护打开的文件：

- fd 表：每个进程都有自己的 fd 表。

- 文件表：打开文件的集合，**所有进程共享**

  文件表表项：**文件位置，refcnt，指向 v-node 表的指针**。

- v-node 表：**所有进程共享**，每个表项包含 stat 结构中大部分信息。

注意：**多个 fd 可以通过不同的文件表表项引用同一个文件**，因为**每个 fd 都有自己的文件位置**，对不同 fd 操作就可以从文件不同位置读信息。

fork 之后，子进程会有和父进程一样的 fd 表，自然其引用的文件，`refcnt` 会增加。内核直到 `refcnt` 为 $0$ 才会真正关闭文件。

---

I/O 重定向：用 `dup2(oldfd, newfd)` 函数。

**会把 `oldfd` 的 fd 表项复制到 `newfd` 的 fd 表项**。

相当于这个时候，oldfd 和 newfd 都对应了原来 oldfd 指向的文件，`refcnt` 会增加。

把 STDIN 重定向到 5 就是 `dup(5, 0)`。

> 做题的时候千万看清楚哪个进程里，哪个文件表项，对应的文件位置是什么。不知道为什么出题的就喜欢在这上面整花活

## Chap. 11 NetProg

client 和 server 都是**进程**

- 最低层是*局域网 LAN*，常见技术 以太网。主机之间由**集线器 hub**连接，**帧会被转发到每个主机**
- 用**网桥**可以把若干以太网连起来形成**桥接以太网**。网桥有分配算法。
- 多个不兼容的局域网通过**路由器 router** 连接成**互联网 Internet**（注意区分 Internet）

各种协议：

- IP 协议：**主机到主机**，**数据报 datagram**，**不可靠**
- UDP 协议：**进程间**，不可靠
- TCP 协议：**进程间，全双工**

client 和 server 通过在**连接**上发送字节流来通信，**点对点，全双工**。

接下来分别考虑 client 和 server 的套接字接口：

- client：
  - `getaddrinfo`：传进去主机名，返回一个 `ai_addr` 链表。记得用 `freeaddrinfo` 给释放
  - `socket`：创建一个套接字 fd，在这里为 `clientfd`，**不能直接用于读写**
  - `connect`：传入 `clientfd` 和待连接服务器的套接字地址 `addr`，**阻塞，直到连接成功或者失败**。成功了就可以用 `clientfd` 来 IO 了。
  - 然后就用 `rio_writen` 和 `rio_readlineb` 来进行通信。
  - `close` 来结束。
- server：
  - `getaddrinfo`：主机名的地方*传 `NULL`* 进去，`hints` 里面的 `ai_flags` 要带上 **`AI_PASSIVE`**。
  - `socket` 创建一个套接字 fd，一样的。
  - `int bind(int sockfd, const struct sockaddr *addr, socketlent_t addrlen)`：*把 `socket` 创建的 fd 与 `getaddrinfo` 搞到的 `addr` 给绑定起来*。
  - `listen`：把 `sockfd` 从**主动套接字转化为监听套接字**，注意：**不会阻塞**。
  - `int accept(int listenfd, sockaddr *addr, int *addrlen)`：**阻塞**，直到收到连接请求，把客户端的 sockaddr 填写到第二个参数里面，返回一个**connfd，接下来用这个 fd 来通信**。
  - 然后就用 `rio_writen` 和 `rio_readlineb` 来进行通信。read 到 EOF 了就结束。

`getaddrinfo` 和 `getnameinfo` 二者是相反关系。注意其与具体协议无关。

> 注意 `open_listenfd` 和 `open_clientfd` 的实现，注意失败的情况一定要记得关闭对应的 fd

HTTP 是应用级协议。
