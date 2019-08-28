# Operating System (OS)

[**信号**](#信号)

[**多进程**](#多进程)

[**多线程**](#多线程)

[**I/O模型**](#I/O模型)

# **信号**

<b><details><summary>1. 信号的概念</summary></b>

#### **1.1 基本概念**

  信号是由用户，系统或进程发送给目标进程的信息，以通知目标进程某个状态的转变或系统异常

  信号是在软件层次上对中断机制的一种模拟，在原理上，一个进程收到一个信号与处理器收到一个中断请求可以说是一样的。信号是异步的，一个进程不必通过任何操作来等待信号的到达，事实上，进程也不知道信号到底什么时候到达

  信号是进程间通信机制中唯一的异步通信机制，可以看作是异步通知，通知接收信号的进程有哪些事情发生了。信号机制经过POSIX实时扩展后，功能更加强大，除了基本通知功能外，还可以传递附加信息


#### **1.2 信号分类**

<b><details><summary>可靠性方面：**可靠信号**与**不可靠信号**</summary></b>

`Linux` 信号机制基本上是从 `Unix` 系统中继承过来的。早期 `Unix` 系统中的信号机制比较简单和原始，信号值小于`SIGRTMIN` 的信号都是 **不可靠信号**。这就是不可靠信号的来源。它的主要问题是**信号可能丢失**


随着时间的发展，实践证明了有必要对信号的原始机制加以改进和扩充。由于原来定义的信号已有许多应用，不好再做改动，最终只好又新增加了一些信号，并在一开始就把它们定义为 **可靠信号**，这些信号 **支持排队，不会丢失**


信号值位于 `SIGRTMIN` 和 `SIGRTMAX` 之间的信号都是 **可靠信号** ，**可靠信号克服了信号可能丢失的问题*。** `Linux` 在支持新版本的 信号安装函数 `sigation()` 以及 信号发送函数 ` sigqueue()` 的同时，仍然支持早期的 `signal() ` 信号安装函数 ，支持 信号发送函数 `kill()`


**信号的可靠与不可靠只与信号值有关，与信号的发送及安装函数无关。** 目前 `linux` 中的 `signal()` 是通过 `sigation()` 函数实现的，因此，即使通过 `signal()` 安装的信号，在信号处理函数的结尾也不必再调用一次信号安装函数。同时，由 `signal()` 安装的实时信号支持排队，同样不会丢失

对于目前 `linux` 的两个信号安装函数：`signal()` 及 `sigaction()` 来说，它们都不能把 `SIGRTMIN` 以前的信号变成可靠信号（都不支持排队，仍有可能丢失，仍然是不可靠信号），而且对 `SIGRTMIN` 以后的信号都支持排队。这两个函数的最大区别在于，**经过 `sigaction` 安装的信号都能传递信息给信号处理函数，而经过 `signal` 安装的信号不能向信号处理函数传递信息。对于信号发送函数来说也是一样的**

</details>

<b><details><summary>与时间关系上：**实时信号**与 **非实时信号**</summary></b>

早期 `Unix` 系统只定义了 `32` 种信号，前 `32` 种信号已经有了预定义值，每个信号有了确定的用途及含义，并且每种信号都有各自的缺省动作。如按键盘的 `CTRL ^C` 时，会产生 `SIGINT` 信号，对该信号的默认反应就是进程终止。后`32` 个信号表示**实时信号**，等同于前面阐述的可靠信号。这保证了发送的多个实时信号都被接收。

**非实时信号都不支持排队，都是不可靠信号；实时信号都支持排队，都是可靠信号。**
</details>

#### **1.3 信号查看**

  信号定义在`signal.h`头文件中，信号名都定义为正整数

  在Linux中可以通过`kill -l`查看信号名及序号，信号从`1`开始编号，不存在`0`号信号

</details>


<b><details><summary>2. 信号的产生方式</summary></b>

- **对于前台进程，用户可以通过输入特殊终端字符来为它发送信号。** 比如输入`Ctrl + C`通常会给进程发送一个中断信号(SIGINT)，`Ctrl + Z`会发送`SIGTSTP`信号
- **系统异常，** 比如非法内存访问
- **系统状态变化，** 比如`alarm`定时器到期将引起`SIGALRM`信号
- **运行kill命令或调用kill函数**

</details>

<b><details><summary>3. 发送信号</summary></b>

#### **3.1 通过按键发送信号**

- `Ctrl + C` 发送 `SIGINT`
- `Ctrl + Z` 发送 `SIGTSTP`
- `Ctrl + \` 发送 `SIGQUIT`
- ......

#### **3.2 通过系统调用发送信号**

<b><details><summary>**`kill` 函数**，可以给一个指定的进程发送信号</summary></b>

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

        `errno`含义：

        `EINVAL`   无效的信号

        `EPERM`    该进程没有权限发送信号给任何一个目标进程

        `ESRCH`    目标进程或进程组不存在

</details>

<b><details><summary>**`raise`函数**，可以给当前进程发送信号(给自己)</summary></b>

  ```c++
  #include <signal.h>
  int raise(int signo);
  ```
  - `signo`  要发送的信号

  - 返回值

    ` 0`   成功

    `-1` 失败
</details>

<b><details><summary>**`alarm`函数**，在进程中设置一个定时器，当定时器指定的时间到时，它向进程发送 `SIGALRM` 信号</summary></b>

  ```c++
  #include <unistd.h>
  unsigned int alarm(unsigned int seconds);
  ```

  - `seconds`  指定定时器的秒数，`seconds = 0` ，取消定时器，并返回剩余时间

  - 返回值

    `≥0`，如果调用此`alarm`前，进程已经设置了闹钟时间，则返回上一个闹钟时间的剩余时间，否则返回`0`

    `-1`，失败

</details>

**简单示例**

<b><details><summary> 通过 `kill` 函数发送信号 </summary></b>

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
</details>

<b><details><summary>  通过 `raise` 发送信号 </summary></b>
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
</details>


<b><details><summary>  通过 `alarm` 发送信号 </summary></b>
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
</details>


</details>

<b><details><summary>4. 信号在内核中的表示</summary></b>


#### **4.1 相关概念**

- 实际执行信号的处理动作称为`信号递达(Delivery)`
- 信号从产生到递达之间的状态,称为`信号未决(Pending)`
- 进程可以选择`阻塞(Block)`某个信号，被阻塞的信号产生时将保持在未决状态，直到进程解除对此信号的阻塞，才执行递达的动作。
- 注意，阻塞和忽略是不同的，只要信号被阻塞，它就不会递达，只有在解除阻塞后才传递它。而忽略一个信号总是被传递，只是没有进一步的操作

#### **4.2 信号相关的数据结构**

​	![sig](https://github.com/Mmmmmmi/MyNote/blob/master/resource/sig_struct1.png)

   <b><details><summary> 信号相关的字段 </summary></b>

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
    <td>struct sighand_struct *</td>
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

`blocked`字段存放进程当前阻塞的信号。它是一个`sigset_t`数组，每种信号类型对应一个元素：


```c++
typedef struct {
	unsigned long sig[2];
}sigset_t
```

因为每个无符号长整数由`32`位组成，所以在Linux中可以声明的信号最大数是`64`(`_NSIG`宏表示这个值)。没有值为`0`的信号，因此，信号的编号对应于`sigset_t`类型变量中相应位下标加`1`

每个信号只有一个`bit`的未决标志，非`0`即`1`，不记录该信号产生了多少次，阻塞标志也是这样表示的。因此，未决和阻塞标志可以用相同的数据类型 `sigset_t` 来存储,`sigset_t` 称为**信号集**，这个类型可以表示每个信号的`有效` 或 `无效`状态，在阻塞信号集中`有效`和`无效`的含义是该信号是否被阻塞，而在未决信号集中`有效`和`无效`的含义是该信号是否处于未决状态。阻塞信号集也叫做当前进程的 ` 信号屏蔽字(Signal Mask) ` ，这⾥里的屏蔽应该理解为阻塞而不是忽略

`signal` 字段指向信号描述符(一个 ` signal_struct `类型的结构)，用来跟踪共享挂起信号。实际上，信号描述符还包括与信号处理关系并不密切的一些字段，如：每进程的资源限制数组 ` rlim ` ，分别用于存放进程的组领头进程和会话领头进程 ` PID ` 的字段 ` pgrp ` 和 ` session ` 。实际上，**信号描述符被属于同一线程组的所有进程共享 ，也就是被调用`clone() `系统调用创建的所有进程共享 ，因此，对属于同一线程组的每个进程而言，信号描述符中的字段必须都是相同的**

</details>

<b><details><summary> 信号描述符中与信号处理有关的字段 </summary></b>

<table>
<tr>
    <th width=30%>类型</th>
    <th width=10%>字段</th>
    <th width=60%>描述</th>
</tr>
<tr>
    <td>atomic_t</td>
    <td>count</td>
    <td>信号描述符的使用计数器</td>
</tr>
<tr>
    <td>atomic_t</td>
    <td>live</td>
    <td>线程组中的活动进程的数量</td>
</tr>
<tr>
    <td>wait_queue_head_t</td>
    <td>wait_chldexit</td>
    <td>在系统调用wait4()中睡眠的进程的等待队列</td>
</tr>
<tr>
    <td>struct task_struct *</td>
    <td>curr_target</td>
    <td>接收信号的线程组中最后一个进程的描述符</td>
</tr>
<tr>
    <td>struct sigpending</td>
    <td>shared_pending</td>
    <td>存放共享挂起信号的数据结构</td>
</tr>
<tr>
    <td>int</td>
    <td>group_exit_code</td>
    <td>线程组的进程终止代码</td>
</tr>
<tr>
    <td>struct task_struct *</td>
    <td>group_exit_task</td>
    <td>在杀死整个线程组的时候使用</td>
</tr>
<tr>
    <td>int</td>
    <td>notify_count</td>
    <td>在杀死整个线程组的时候使用</td>
</tr>
<tr>
    <td>int</td>
    <td>group_stop_count</td>
    <td>在停止整个线程组的时候使用</td>
</tr>
<tr>
   <td>unsigned int</td>
   <td>flags</td>
   <td>在传递修改进程状态的时候使用的标志</td>
</tr>
</table>

`sighand`字段指向信号处理程序描述符(一个`sighand_struct`类型的结构)，描述每个信号必须怎样被线程组处理。**在调用clone()系统调用时，设置`CLONE_SIGHAND`标志，信号处理程序描述符就可以由几个进程共享**

</details>

<b><details><summary> 信号处理程序描述符的字段 </summary></b>

<table>
    <tr>
    <th width=30%>类型</th>
    <th width=10%>字段</th>
    <th width=60%>描述</th>
</tr>
<tr>
    <td>atomic_t</td>
    <td>count</td>
    <td>信号处理程序描述符的使用计数器</td>
</tr>
<tr>
    <td>struct k_sigaction [64]</td>
    <td>action</td>
    <td>说明在所传递信号上执行操作的结构数组</td>
</tr>
<tr>
    <td>spinlock_t</td>
    <td>siglock</td>
    <td>保护信号描述符和信号处理程序描述符的自旋锁</td>
</tr>
</table>

描述符的 `count` 字段表示共享该结构的进程个数。**在一个POSIX的多线程应用中，线程组中的所有轻量级进程都引用相同的信号描述符和信号处理程序的描述符**

</details>
</details>

<b><details><summary>5. 信号集</summary></b>

<b><details><summary>信号集函数</summary></b>

Linux用数据结构 ` sigset_t ` 来表示一组信号，前面已经说过它的定义，Linux提供了一组函数来设置、修改、删除和查询信号集。

```c++
#include <signal.h>
int sigemptyset(sigset_t *set); 	/* 清空信号集 */
int sigfillset(sigset_t *set); 		/* 在信号集中设置所有信号 */
int sigaddset (sigset_t *set, int signo); /* 将信号 _signo 添加至信号集中 */
int sigdelset(sigset_t *set, int signo);  /* 将信号 _signo 从信号集中删除 */
int sigismember（const sigset_t *set, int signo); /* 测试 _signo 是否在信号集中 */
```
在使用 `sigset_t` 之前，一定要调用 `sigmptyset` 或 `sigfillset` 做初始化，使信号集处于确定的状态。初始化 `sigset_t` 变量之后就可以再调用 `sigaddset` 和 `sigdelset` 在该信号集中添加或删除某种有效信号

前四个函数都是成功返回 `0` ,出错返回 `-1` 。 `sigismember` 是一个布尔函数，用于判断一个信号集的有效信号中是否包含某种信号，若包含则返回 `1` 。不包含则返回 `0` ，出错返回 `-1`

</details>

<b><details><summary>进程信号掩码</summary></b>

调用`sigprocmask`可以读取或更改进程的信号屏蔽字 (阻塞信号集)

```c++
#include <signal.h>
int sigprocmask(int _how, sigset_t *_set, sigset_t *_oset);
```

`_set` 参数指定新的信号掩码，`_oset` 参数输出原来的信号掩码(如果不为  `NULL` )的话

如果 `_set` 参数不为 `NULL`，则 `_how` 参数指定设置进程信号掩码的方式可为以下三种：

<table>
<tr>
    <th width=20%>_how 参数</th>
    <th width =80%>含义</th>
</tr>
 <tr>
     <td> SIG_BLOCK </td>
     <td> 新的进程信号掩码是其当前值和 _set 指定信号集的并集 </td>
</tr>
<tr>
    <td> SIG_UNBLOCK </td>
    <td> 新的进程信号掩码是其当前值和 ~_set 信号集的交集，因此 _set 指定的信号集将不被屏蔽 </td>
</tr>
<tr>
    <td> SIG_SETMASK </td>
    <td> 直接将进程信号掩码设置为 _set </td>
</tr>
</table>

如果 `_set` 为 `NULL`，则进程信号掩码不变，此时我们仍然可以利用 `_oset` 参数来获得进程当前的信号掩码

sigprocmask 成功时返回 `0` ，失败返回 `-1` 并设置  `errno` 

</details>

<b><details><summary>被挂起的信号</summary></b>

设置进程信号掩码之后，被屏蔽的信号将不能再被进程接收。如果给进程发送一个被屏蔽的信号，则操作系统将给该信号设置为进程的一个被挂起的信号(未决状态)。如果我们取消对被挂起信号的屏蔽，则它能立即被进程接收到。

```c++
#include <signal.h>
int sigpending(sigset_t * set)
```

`set`  用于保存被挂起的信号集。如果进程多次接收到同一个被挂起的信号，`sigpending` 函数也只能反映一次。并且当我们再次使用 `sigprocmask` 使能该挂起信号时，该信号的处理函数也只能被触发一次

`sigpending` 成功时，返回 `0`  ，失败时返回 `-1` 并设置 `errno` 

</details>
</details>

<b><details><summary>6. 信号的处理流程</summary></b>

对于一个完整的信号生命周期(从信号发送到相应的处理函数执行完毕)来说，可以分为三个阶段：**信号产生** ， **信号在进程中注册** ，**信号的执行和注销**

<b><details><summary>信号产生</summary></b>

即上面介绍到的`2` ，`3`点

</details>

<b><details><summary>信号在进程中注册</summary></b>

在进程表的表项中有一个软中断信号域，该域中每一位对应一个信号。内核给一个进程发送软中断信号的方法，是在进程所在的进程表项的信号域设置对应于该信号的位。如果信号发送给一个正在睡眠的进程，如果进程睡眠在可被中断的优先级上，则唤醒进程；否则仅设置进程表中信号域相应的位，而不唤醒进程。如果发送给一个处于可运行状态的进程，则只置相应的域即可

进程的 `task_struct` 结构中有关于本进程中未决信号的数据成员 `struct sigpending pending`

```c++
struct sigpending{
    struct sigqueue *head, *tail;
    sigset_t signal;
};
```

`head` 和 `tail` 分别指向一个 `sigqueue` 类型的结构链（称之为 **未决信号信息链** ）的首尾，信息链中的每个 `sigqueue` 结构刻画一个特定信号所携带的信息，并指向下一个 `sigqueue` 结构

```c++
struct sigqueue{
    struct sigqueue *next;
  	siginfo_t info;
}
```

`signal` 是进程中所有 **未决信号集**。

信号在进程中注册指的就是信号值加入到进程的未决信号集 `sigset_t signal`（每个信号占用一位）中，并且信号所携带的信息被保留到未决信号信息链的某个 ` sigqueue `   结构中。只要信号在进程的未决信号集中，表明进程已经知道这些信号的存在，但还没来得及处理，或者该信号被进程阻塞。


当一个 **实时信号** 发送给一个进程时，不管该信号是否已经在进程中注册，都会被再注册一次，因此，信号不会丢失，因此，实时信号又叫做 **可靠信号** 。这意味着同一个实时信号可以在同一个进程的未决信号信息链中占有多个 `sigqueue` 结构（进程每收到一个实时信号，都会为它分配一个结构来登记该信号信息，并把该结构添加在未决信号链尾，即所有诞生的实时信号都会在目标进程中注册）。


当一个 **非实时信号** 发送给一个进程时，如果该信号已经在进程中注册（通过 `sigset_t signal` 指示），则该信号将被丢弃，造成信号丢失。因此，非实时信号又叫做 **不可靠信号**  。这意味着同一个非实时信号在进程的未决信号信息链中，至多占有一个 `sigqueue` 结构。


总之信号注册与否，与发送信号的函数（如 `kill()` 或 `sigqueue()` 等）以及信号安装函数（ `signal()` 及` sigaction()` ）无关，只与信号值有关（信号值小于 ` SIGRTMIN ` 的信号最多只注册一次，信号值在 `SIGRTMIN` 及` SIGRTMAX` 之间的信号，只要被进程接收到就被注册）

</details>

<b><details><summary>信号的执行和注销</summary></b>

内核处理一个进程收到的软中断信号是在该进程的上下文中，因此，进程必须处于运行状态。当其由于被信号唤醒或者正常调度重新获得CPU时，在其从内核空间返回到用户空间时会检测是否有信号等待处理。如果存在未决信号等待处理且该信号没有被进程阻塞，则在运行相应的信号处理函数前，进程会把信号在未决信号链中占有的结构卸掉。

对于 **非实时信号** 来说，由于在未决信号信息链中最多只占用一个 `sigqueue` 结构，因此该结构被释放后，应该把信号在进程未决信号集中删除（信号注销完毕）；而对于 **实时信号** 来说，可能在未决信号信息链中占用多个 `sigqueue` 结构，因此应该针对占用 `sigqueue` 结构的数目区别对待：如果只占用一个 `sigqueue` 结构（进程只收到该信号一次），则执行完相应的处理函数后应该把信号在进程的未决信号集中删除（信号注销完毕）。否则待该信号的所有`sigqueue` 处理完毕后再在进程的未决信号集中删除该信号。

当所有未被屏蔽的信号都处理完毕后，即可返回用户空间。对于被屏蔽的信号，当取消屏蔽后，在返回到用户空间时会再次执行上述检查处理的一套流程。

内核处理一个进程收到的信号的时机是在一个进程从内核态返回用户态时。所以，当一个进程在内核态下运行时，软中断信号并不立即起作用，要等到将返回用户态时才处理。进程只有处理完信号才会返回用户态，进程在用户态下不会有未处理完的信号。

</details>
</details>

<b><details><summary>7. 信号的处理方式</summary></b>

<b><details><summary>默认的处理方式</summary></b>

由内核预定义的缺省操作取决于信号的类型，可以是以下几种之一：

- `Terminate`，进程被终止（杀死）

- `Dump`，进程被终止（杀死），并且，如果可能，创建包含进程执行上下文的核心转储文件，这个文件可以用于调试

- `Ignore`，进程被忽略

- `Stop`，进程被停止，即把进程置为 `TASK_STOPPED` 状态

- `Continue`，继续进程，如果进程被停止（ `TASK_STOPPED` ），就把它置为 `TASK_RUNNING` 状态

</details>

<b><details><summary>显式地忽略信号</summary></b>

当进程接收到一个它忽略的信号时，进程丢弃该信号，就像没有收到该信号似的继续运行

</details>

<b><details><summary>通过调用相应的信号处理函数捕获信号</summary></b>

信号处理函数应该是可重入的，否则很容易引发一些竞态条件

</details>
</details>

<b><details><summary>8. 信号的捕捉</summary></b>


<b><details><summary>信号捕获过程</summary></b>

![sig](https://github.com/Mmmmmmi/MyNote/blob/master/resource/do_signal.png)

如果信号有一个专门的处理程序（**信号处理程序**），`do_signal()` 函数就必须强迫该处理程序执行。这是通过 `handle_signal()` 进行的：

```c++
handle_signal(signr, &info, &ka, oldset, regs);
if (ka->sa.sa_flags & SA_ONESHOT)
    ka->sa.sa_handler = SIG_DFL;
return 1;
```

如果所接收信号的 `SA_ONESHOT ` 标志被置位，就必须重新设置它的缺省操作，以便同一信号的再次出现不会再次触发这一信号处理程序的执行。注意 `do_signal()` 在处理了一个单独的信号后怎样返回。直到下一次调用 `do_signal()` 时才考虑其他挂起的信号。这种方式确保了实时信号将以适当的顺序得到处理

信号处理程序是用户态进程定义的函数，并包含在用户态的代码段中。`handle_signal()` 函数运行在内核态，而信号处理程序运行在用户态，这就意味着在当前进程恢复 “正常” 执行之前，它必须首先执行用户态的信号处理程序。此外，当内核打算恢复进程的正常执行时，内核态堆栈不再包含被中断程序的硬件上下文，因为每当从内核态向用户态转换时，内核态堆栈都被清空。同时，因为信号处理程序可以调用系统调用，在这种情况下，执行了系统调用的服务例程后，控制权必须返回到信号处理程序而不是被中断程序的正常代码流

` Linux ` 采用的解决方法是把保存在内核态堆栈中的硬件上下文拷贝到当前进程的用户态堆栈中。用户态堆栈也以这样的方式被修改，即当信号处理程序终止时，自动调用 `  sigreturn()` 系统调用把这个硬件上下文拷贝回到内核态堆栈中，并恢复用户态堆栈中原来的内容

当中断、异常或系统调用发生时，进程切换到内核态，在返回用户态前，内核执行 ` do_signal() `函数，这个函数又依次处理信号（通过调用 ` handle_signal() ` ）和 建立用户态堆栈 （通过调用 `  setup_frame()` 或 `setup_rt_frame()` ）。当进程又切换到用户态时，因为信号处理程序的起始地址被强制放入程序计数器中，因此开始执行信号处理程序。当处理程序终止时，`setup_frame()` 或 `setup_rt_frame()` 函数放在用户态堆栈中的返回代码就被执行。这个代码调用 `  sigreturn()` 或 `rt_sigreturn()` 系统调用，相应的服务例程将正常程序的用户态堆栈硬件上下文拷贝到内核态堆栈，并把用户态堆栈恢复到它原来的状态（通过调用  `  restore_sigcontext()` ）。当这个系统调用结束时，普通进程就因此能恢复自己的执行

</details>

<b><details><summary> `  signal` 系统调用</summary></b>

```c++
#include <signal.h>
typedef void (*sighandler_t)(int);
sighandler_t signal(int signum, sighandler_t handler);
```

`sighandler_t` 除了指向自定义的信号处理函数之外，`bits/signum.h` 头文件中还定义了信号的两种其他处理方式 `SIG_IGN` 和 `SIG_DEL`

```c++
#include <bits/signum.h>
#define SIG_DFL ((sighandler_t) 0)	//默认处理方式 前面7介绍到
#define SIG_IGN ((sighandler_t) 1)	//忽略信号
```

`signum` 参数指定要捕获的信号类型

`_handler` 参数是 `sighandler_t` 类型的函数指针，用于指定信号 `sig` 的处理函数

`signal` 函数成功调用时返回一个函数指针，该函数指针类型也是  `sighandler_t`  。这个返回值是前一次调用 `signal` 函数时传入的函数指针，或者是信号 `  sig` 对应的默认处理函数指针 ` SIG_DEF` （如果是第一次调用 `signal`的话 ）

`signal` 系统调用出错时返回 `SIG_ERR`，并设置 `errno`

</details>

<b><details><summary> `  sigaction` 系统调用</summary></b>

```c++
#include <signal.h>
int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);
```

`signum` 参数指定要捕获的信号类型

`act` 参数指定新的信号处理方式

`oact` 参数输出信号先前的处理方式（如果不为 `NULL` 的话）

`act` 和 `oact` 都是 `sigaction` 结构体类型的指针，`sigaction` 结构体描述了信号处理的细节，其结构如下：

```c++
struct sigaction {
    void     (*sa_handler)(int);
	void     (*sa_sigaction)(int, siginfo_t *, void *);
	sigset_t   sa_mask;
	int        sa_flags;
	void     (*sa_restorer)(void);
};
```

`sa_handler` 成员指定信号处理函数

`sa_mask` 成员设定信号掩码（准确地说是在原有信号掩码的基础上增加信号掩码），以指定哪些信号不能发送给本进程

`sa_mask` 是信号集`sigset_t` 类型，前面已经介绍过

`sa_flag` 设置程序收到信号时的行为，其可选值如下：

<table>
    <tr>
        <th width=25%>选项</th>
        <th width=75%>含义</th>
    </tr>
    <tr>
        <td>SA_NOCLDSTOP</td>
        <td>如果 sigaction 的 signum 参数是 SIGCHLD ，则设置该标志表示子进程暂停时不生成 SIGCHLD 信号 </td>
    </tr>
    <tr>
        <td>SA_NOCLDWAIT</td>
        <td>如果 sigaction 的 signum 参数是 SIGCHLD ，则设置该标志表示子进程结束时不产生僵尸进程</td>
    </tr>
    <tr>
        <td>SA_NODEFER</td>
        <td>当接收到信号并进入其信号处理函数时，不屏蔽该信号。默认情况下，我们期望进程在处理一个信号时不再接收到同种信号，否则将引起一些竞态条件</td>
    </tr>
    <tr>
        <td>SA_ONSTACK</td>
        <td>调用 sigltstack 函数设置的可选信号栈上的信号处理函数</td>
    </tr>
    <tr>
        <td>SA_RESETHAND</td>
        <td>信号处理函数执行完毕后，恢复信号的默认处理方式</td>
    </tr>
    <tr>
        <td>SA_RESTART</td>
        <td>重新调用该信号终止的系统调用</td>
    </tr>
</table>

`sa_restorer` 成员已经过时，一般不使用

`sigaction` 成功时返回 `0`，失败时则返回 `-1`，并设置 `errno`

</details>

<b><details><summary>示例</summary></b>


![sig](https://github.com/Mmmmmmi/MyNote/blob/master/resource/sig_struct.png)

每个信号都有两个标志位分别表示阻塞 (`block`) 和未决 (`pending`) ，还有一个函数指针表示处理动作。信号产生时，内核在进程控制块中设置该信号的未决标志，直到信号递达才清除该标志。在上图中, `SIGHUP`信号未阻塞也未产生过，当它递达时执⾏行默认处理动作。 `SIGINT`信号产⽣生过，但正在被阻塞，所以暂时不能递达。虽然它的处理动作是忽略，但在没有解除阻塞之前不能忽略这个信号，因为进程仍有机会改变处理动作之后再解除阻塞。 `SIGQUIT`信号未产生过,一旦产生`SIGQUIT`信号将被阻塞，它的处理动作是用户自定义函数`sighandler`

</details>
</details>


# **多进程**

<b><details><summary>1. 进程状态</summary></b>
</details>

<b><details><summary>2. `fork` 系统调用</summary></b>
</details>

<b><details><summary>3. `exec` 系列系统调用</summary></b>
</details>

<b><details><summary>4. 处理僵尸进程</summary></b>
</details>

<b><details><summary>5. 管道</summary></b>
</details>

<b><details><summary>6. 信号量</summary></b>
</details>

<b><details><summary>7. 共享内存</summary></b>
</details>

<b><details><summary>8. 消息队列</summary></b>
</details>

<b><details><summary>9. IPC命令</summary></b>
</details>

<b><details><summary>10. 在进程间传递文件描述符</summary></b>
</details>


# **多线程**

<b><details><summary>1. 多线程概述</summary></b>
</details>

<b><details><summary>2. 创建线程和结束线程</summary></b>
</details>

<b><details><summary>3. 线程属性</summary></b>
</details>

<b><details><summary>4. POSIX信号量</summary></b>
</details>

<b><details><summary>5. 互斥锁</summary></b>
</details>

<b><details><summary>6. 条件变量</summary></b>
</details>

<b><details><summary>7. 多线程环境</summary></b>
</details>


# **I/O模型**
任何 IO 过程中，都包含两个步骤：第⼀步是等待，第⼆步是拷贝。而在实际的应用场景中，等待消耗的时间往往都远⾼于拷贝的时间。因此，要让 IO 更高效，最核⼼的办法就是让等待的时间尽量少

<b><details><summary>1. 相关知识点</summary></b>

- **同步与异步**：
  - 同步：在发出系统调用后，一直等到有结果了才会返回。无论调用是否成功，一定会返回结果。可以理解为，调用者主动等待调用结果，有结果了才会返回
  - 异步：在发出系统调用后，调用就直接返回了，等到有结果之后，再由被调用者通过状态、通知来通知调用者，或通过回调函数处理这个调用
- **阻塞与非阻塞**：
  - 阻塞：在调用结果返回之前，当前线程会被挂起，等到得到调用结果之后，才会返回
  - 非阻塞：无论是否能得到结果，线程都会返回

</details>

<b><details><summary>2. 五种 I/O 模型</summary></b>

- **阻塞 IO**：在内核将数据准备好之前，系统调⽤用会一直等待。所有套接字默认都是阻塞 IO

- **非阻塞 IO**：即使内核还未将数据准备好，系统调用也会返回，并且返回并且返回 EWOULDBLOCK 错误码。非阻塞 IO 往往需要程序员循环的方式反复尝试读写文件描述符，这个过程称为轮询。这对 CPU 来说是较大的浪费，一般只有特定场景下才使用

- **信号驱动 IO**：内核将数据准备好的时候，用`SIGIO`信号通知应用程序进行 IO 操作

- **多路转接 IO**：与阻塞 IO 类似，但是可以同时等待多个文件描述符的状态

- **异步 IO**：由内核在数据拷贝完成时，通知应用程序。与信号驱动 IO 不同的地方是，信号驱动 IO 是在数据准备好的时候，通知应用程序进行 IO 操作


</details>
</details>

<b><details><summary>3. 非阻塞IO</summary></b>
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
</details>

<b><details><summary>4. select</summary></b>

</details>

<b><details><summary>5. poll</summary></b>
</details>

<b><details><summary>6. epoll</summary></b>
</details>

<b><details><summary>7. 比较</summary></b>

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
