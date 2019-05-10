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

</details>

<b><details><summary>多线程</summary></b>

</details>


## **高级 I/O**
    任何 IO 过程中，都包含两个步骤。 第⼀步是等待，第⼆步是拷贝。而在实际的应用场景中，等待消耗的时间往往都远⾼于拷贝的时间。因此，要让 IO 更高效，最核⼼的办法就是让等待的时间尽量少。
<b><details><summary>五种 I/O 模型</summary></b>

- 阻塞 IO：在内核将数据准备好之前，系统调⽤用会一直等待。所有套接字默认都是阻塞 IO。

- 非阻塞 IO：即使内核还未将数据准备好，系统调用也会返回，并且返回并且返回 EWOULDBLOCK 错误码。非阻塞 IO 往往需要程序员循环的方式反复尝试读写文件描述符，这个过程称为轮询。这对 CPU 来说是较大的浪费，一般只有特定场景下才使用。

- 信号驱动 IO：内核将数据准备好的时候，用`SIGIO`信号通知应用程序进行 IO 操作。

- 多路转接 IO：与阻塞 IO 类似，但是可以同时等待多个文件描述符的状态。

- 异步 IO：由内核在数据拷贝完成时，通知应用程序。与信号驱动 IO 不同的地方是，信号驱动 IO 是在数据准备好的时候，通知应用程序进行 IO 操作。

- 同步与异步：
  - 同步：在发出系统调用后，一直等到有结果了才会返回。无论调用是否成功，一定会返回结果。可以理解为，调用者主动等待调用结果，有结果了才会返回。

  - 异步：在发出系统调用后，调用就直接返回了，等到有结果之后，再由被调用者通过状态、通知来通知调用者，或通过回调函数处理这个调用。

- 阻塞与非阻塞：
  - 阻塞：在调用结果返回之前，当前线程会被挂起，等到得到调用结果之后，才会返回

  - 非阻塞：无论是否能得到结果，线程都会返回。

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

<b><details><summary>epoll</summary></b
</details>


<b><details><summary>比较</summary></b
</details>