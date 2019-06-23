# **Linux**

[**系统指令**](#系统指令)

[**系统工具**](#系统工具)

[**系统编程**](#系统编程)

[**高级 I/O**](#高级io)

## **系统指令**

## **系统工具**

<b><details><summary>gdb</summary></b>

#### gdb如何进行多线程调试



</details>

## **系统编程**


<b><details><summary>多进程</summary></b>

</details>

<b><details><summary>进程间通信</summary></b>

</details>

<b><details><summary>信号</summary></b>

### **1. 信号的概念**

信号是由用户，系统或进程发送给目标进程的信息，以通知目标进程某个状态的转变或系统异常

信号是在软件层次上对中断机制的一种模拟，在原理上，一个进程收到一个信号与处理器收到一个中断请求可以说是一样的。信号是异步的，一个进程不必通过任何操作来等待信号的到达，事实上，进程也不知道信号到底什么时候到达

信号是进程间通信机制中唯一的异步通信机制，可以看作是异步通知，通知接收信号的进程有哪些事情发生了。信号机制经过POSIX实时扩展后，功能更加强大，除了基本通知功能外，还可以传递附加信息

### **2. 信号的产生方式**
- **对于前台进程，用户可以通过输入特殊终端字符来为它发送信号。** 比如输入`Ctrl + C`通常会给进程发送一个中断信号(SIGINT)，`Ctrl + Z`会发送`SIGTSTP`信号
- **系统异常，** 比如非法内存访问
- **系统状态变化，** 比如`alarm`定时器到期将引起`SIGALRM`信号
- **运行kill命令或调用kill函数**

### **3. 发送信号**

####  **通过按键发送信号**

- `Ctrl + C` 发送 `SIGINT`
- `Ctrl + Z` 发送 `SIGTSTP`
- `Ctrl + \` 发送 `SIGQUIT`
- ......

#### **通过系统调用发送信号**

- **`kill` 函数**，可以给一个指定的进程发送信号

  ```c++
  #include <signal.h>
  int kill(pid_t pid, int sig);
  ```
  
- `pid` 指定目标进程
  
  `pid > 0 `   信号发给PID为`pid`的进程
  
  `pid = 0`   信号发给本进程组内的其他进程
  
  `pid = -1`  信号发给除init进程外的所有进程，但发送者需要有对目标进程发送信号的权限
  
  `pid < -1`  信号发给组ID为`-pid`的进程组中的所有成员
  
- `sig` 指定信号
  
  Linux定义的信号值都大于`0`， 如果 `sig = 0` ，则 kill 函数不发任何信号
  
- 返回值
  
  `0`   函数成功
  
  `-1`  函数失败，并设置`errno`
  
      errno       含义
        
        EINVAL   无效的信号
        
        EPERM    该进程没有权限发送信号给任何一个目标进程
        
        ESRCH    目标进程或进程组不存在



- **`raise`函数**，可以给当前进程发送信号(给自己)
  ```c++
  #include <signal.h>
  int raise(int signo);
  ```
  - `signo`  要发送的信号
  
  - 返回值

    ` 0`   成功

    `-1` 失败
  
- **`alarm`函数**，在进程中设置一个定时器，当定时器指定的时间到时，它向进程发送 `SIGALRM` 信号

  ```c++
  #include <unistd.h>
  unsigned int alarm(unsigned int seconds);
  ```

  - `seconds`  指定定时器的秒数，`seconds = 0` ，取消定时器，并返回剩余时间

  - 返回值

    `≥0`，如果调用此`alarm`前，进程已经设置了闹钟时间，则返回上一个闹钟时间的剩余时间，否则返回`0`

    `-1`，失败

- **简单示例**

  - 通过 `kill` 函数发送信号
  ```C++
  #include <iostream>
  #include <errno.h>
  #include <unistd.h>
  #include <signal.h>
  using namespace std;
  int main()
  {
      int pid = fork();
      if (pid < 0)
      {
          perror("error for fork");
      }
      else if (pid == 0)
      {
          while(1)
          {
              sleep(10);
              cout << "I am child , my pid is: " << getpid() << endl;
          }
      }
      else
      {
          sleep(15);
          cout << "I am father , my pid is: " << getpid() << endl;
          kill(pid, SIGQUIT); //父进程向子进程发送SIGQUIT信号
      }
      return 0;
  }
  
  //运行结果
  I am child , my pid is: 22330
  I am father , my pid is: 22329
  
  //通过strace命令查看进程执行时的系统调用和接收到的信号
  sudo strace -p  22330 > a.txt
  
  //a.txt
  strace: Process 22330 attached
  restart_syscall(<... resuming interrupted nanosleep ...>) = 0
  fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(136, 0), ...}) = 0
  mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f759205d000
  write(1, "I am child , my pid is: 22330\n", 30) = 30
  rt_sigprocmask(SIG_BLOCK, [CHLD], [], 8) = 0
  rt_sigaction(SIGCHLD, NULL, {sa_handler=SIG_DFL, sa_mask=[], sa_flags=0}, 8) = 0
  rt_sigprocmask(SIG_SETMASK, [], NULL, 8) = 0
  nanosleep({tv_sec=10, tv_nsec=0}, {tv_sec=5, tv_nsec=312703}) = ? ERESTART_RESTARTBLOCK (Interrupted by signal)
  --- SIGQUIT {si_signo=SIGQUIT, si_code=SI_USER, si_pid=22329, si_uid=1003} ---
  +++ killed by SIGQUIT +++
  
  //在倒数第二行，接收到了来自父进程22329的信号SIGQUIT，子进程退出
  ```

  - 通过 `raise` 发送信号
  ```C++
  #include <iostream>
  #include <errno.h>
  #include <unistd.h>
  #include <signal.h>
  using namespace std;
  int main()
  {
      while(1)
      {
          cout << "my pid is: " << getpid() << endl;
          sleep(10);
          raise(SIGQUIT);
      }
      return 0;
  }
  
  // 运行结果
  my pid is: 25780
  Quit
  
  //通过strace命令查看进程执行时的系统调用和接收到的信号
  sudo strace -p 25780 -o a.txt
  
  //a.txt
  restart_syscall(<... resuming interrupted nanosleep ...>) = 0
  tgkill(25780, 25780, SIGQUIT)           = 0
  --- SIGQUIT {si_signo=SIGQUIT, si_code=SI_TKILL, si_pid=25780, si_uid=1003} ---
  +++ killed by SIGQUIT +++
  
  //可以看到在倒数第二行，进程自己给自己发送了SIGQUIT信号
  ```

  - 通过 `alarm` 发送信号
  ```c++
  #include <iostream>
  #include <unistd.h>
  #include <signal.h>
  using namespace std;
  int main()
  {
      int i = 5;
      alarm(5);
      while(1)
      {
          cout << i << endl;
          sleep(1);
          i--;
      }
      return 0;
  }
  
  //运行结果
  5
  4
  3
  2
  1
  Alarm clock
  ```
  

### **4. 信号在内核中的表示**

#### **相关概念**

- 实际执⾏行信号的处理动作称为`信号递达(Delivery)`
- 信号从产⽣生到递达之间的状态,称为`信号未决(Pending)`
- 进程可以选择`阻塞(Block)`某个信号，被阻塞的信号产生时将保持在未决状态,直到进程解除对此信号的阻塞,才执行递达的动作
- 注意，阻塞和忽略是不同的，只要信号被阻塞就不会递达，而忽略是在递达之后可选的一种处理动作

#### **信号在内核中的表示示意图**

​	![sig](https://github.com/Mmmmmmi/MyNote/blob/master/resource/sig_struct1.png)

​	![sig](https://github.com/Mmmmmmi/MyNote/blob/master/resource/sig_struct.png)

<table>
<tr>
    <th width=30%>类型</th>
    <th width=10%>字段</th>
    <th width=60%>描述</th>
</tr>
<tr>
    <td>struct signal_struct *</td>
    <td> signal</td>
    <td>指向进程的信号描述符的指针</td>
</tr>
<tr>
    <td>struct signal_struct *</td>
    <td>sighand</td>
    <td>指向进程的信号处理程序描述符的指针</td>
</tr>
<tr>
    <td>sigset_t</td>
    <td>blocked/real_blocked</td>
    <td>表示被阻塞信号的掩码，real_blocked表示临时掩码</td>
</tr>
<tr>
    <td>struct sigpending</td>
    <td>pending</td>
    <td>存放私有挂起信号的数据结构</td>
</tr>
<tr>
    <td>unsigned long</td>
    <td>sas_ss_sp</td>
    <td>信号处理程序备用堆栈的地址</td>
</tr>
<tr>
    <td>size_t</td>
    <td>sas_ss_size</td>
    <td>信号处理程序备用堆栈的大小</td>
</tr>
<tr>
    <td>int (*) (void *)</td>
    <td>notifier</td>
    <td>指向一个函数的指针，设备驱动程序用该函数阻塞进程的某些信号</td>
</tr>
<tr>
    <td>void *</td>
    <td>notifier_data</td>
    <td>指向notifier函数(表中的前一个字段)，可能使用的数据</td>
</tr>
<tr>
    <td>sigset_t *</td>
    <td>notifier_mask</td>
    <td>设备驱动程序通过notifier函数所阻塞的信号的位掩码</td>
</tr>

</table>


### **5. 信号的捕捉**

进程收到一个信号，并不会立即处理，而是

信号常见的处理方法有以下三种：

- **忽略信号**，大多数信号都可使用这种方式进行处理，但有两种信号却决不能被忽略。它们是：`SIGKILL` 和 `SIGSTOP` 。这两种信号不能被忽略的，原因是：它们向超级用户提供一种使进程终止或停止的可靠方法。另外，如果忽略某些由硬件异常产生的信号（例如非法存储访问或除以0），则进程的行为是未定义的
- **默认方式**，对大多数信号的系统默认动作是终止该进程
- **自定义方式捕捉信号**， 提供一个信号处理函数,要求内核在处理该信号时切换到用户态执行这个处理函数,这种方式称为捕捉(Catch)一个信号。注意，不能捕捉`SIGKILL`和`SIGSTOP`信号





</details>

<b><details><summary>多线程</summary></b>

</details>


## **高级 I/O**
任何 IO 过程中，都包含两个步骤：第⼀步是等待，第⼆步是拷贝。而在实际的应用场景中，等待消耗的时间往往都远⾼于拷贝的时间。因此，要让 IO 更高效，最核⼼的办法就是让等待的时间尽量少

几个相关知识点：
- **同步与异步**：
  - 同步：在发出系统调用后，一直等到有结果了才会返回。无论调用是否成功，一定会返回结果。可以理解为，调用者主动等待调用结果，有结果了才会返回
  - 异步：在发出系统调用后，调用就直接返回了，等到有结果之后，再由被调用者通过状态、通知来通知调用者，或通过回调函数处理这个调用
- **阻塞与非阻塞**：
  - 阻塞：在调用结果返回之前，当前线程会被挂起，等到得到调用结果之后，才会返回
  - 非阻塞：无论是否能得到结果，线程都会返回

<b><details><summary>五种 I/O 模型</summary></b>

- **阻塞 IO**：在内核将数据准备好之前，系统调⽤用会一直等待。所有套接字默认都是阻塞 IO

- **非阻塞 IO**：即使内核还未将数据准备好，系统调用也会返回，并且返回并且返回 EWOULDBLOCK 错误码。非阻塞 IO 往往需要程序员循环的方式反复尝试读写文件描述符，这个过程称为轮询。这对 CPU 来说是较大的浪费，一般只有特定场景下才使用

- **信号驱动 IO**：内核将数据准备好的时候，用`SIGIO`信号通知应用程序进行 IO 操作

- **多路转接 IO**：与阻塞 IO 类似，但是可以同时等待多个文件描述符的状态

- **异步 IO**：由内核在数据拷贝完成时，通知应用程序。与信号驱动 IO 不同的地方是，信号驱动 IO 是在数据准备好的时候，通知应用程序进行 IO 操作


</details>

<b><details><summary>非阻塞IO</summary></b>
#### **fcntl**

```cpp
//函数原型
#include <unistd.h>
#include <fcntl.h>
int fcntl(int fd, int cmd, ... /* arg */ );
```
    fd: 需要管理的文件描述符
    cmd: 执行的命令



</details>

<b><details><summary>select</summary></b>

</details>

<b><details><summary>poll</summary></b>
</details>

<b><details><summary>epoll</summary></b>
</details>

<b><details><summary>比较</summary></b>

<table>
<tr>
    <th width=14%>系统调用</th>
    <th width=29%>select</th>
    <th width=29%>poll</th>
    <th width=28%>epoll</th>
</tr>
<tr>
    <td> 事件集合</td>
    <td>用户通过 3 个参数分别传入需要监控的可读、可写及异常等事件，内核通过对这些参数的在线修改来反馈其中的就绪事件(将未就绪的事件从 fd_set 中剔除出去)，这使得用户每次调用 select 都需要重置这3个参数</td>
    <td> 统一处理所有事件类型，因此只需要一个事件集参数。用户通过 pollfd.events 传入需要监控的事件，内核通过修改 pollfd.revents 反馈其中就绪的事件</td>
    <td> 内核通过一个事件表直接管理用户监控的所有事件，因此每次调用 epoll_wait 时，无需反复传入需要监控的事件， epoll_wait 系统调用的参数 events 仅用 来反馈就绪的事件 </td>
</tr>
<tr>
    <td>应用程序索引就绪文件描述符的时间复杂度</td>
    <td>O(n)</td>
    <td>O(n)</td>
    <td>O(1)</td>
</tr>
<tr>
    <td>可监控的文件描述符数目</td>
    <td>一般有最大限制，取决于FD_SETSIZE<br/>这个宏，一般为1024</td>
    <td> 65535</td>
    <td> 65535</td>
</tr>
<tr>
    <td>工作模式</td>
    <td>LT</td>
    <td>LT</td>
    <td>支持 ET 高效模式</td>
</tr>
<tr>
    <td>内核实现和工作效率 </td>
    <td>采用轮询的方式检测就绪事件，算法的时间复杂度为O(n)</td>
    <td>采用轮询的方式检测就绪事件，算法的时间复杂度为O(n)</td>
    <td>通过回调函数的方式检测就绪事件，算法时间复杂度为O(1)</td>
</tr>
</table>

</details>

  ```

  ```