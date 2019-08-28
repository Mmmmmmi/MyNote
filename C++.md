# **C++**
- [**类和对象**](#类和对象)
- [**动态内存管理**](#动态内存管理)
- [**关键字**](#关键字)
- [**STL**](#stl)
- [**C++11**](#c11)
- [**编译及调试**](#编译及调试)

## **类和对象**

<b><details><summary>this 指针</summary></b>

#### **`this` 指针什么时间产生**

    this 指针在成员函数的开始前构造，在成员函数的结束后清除，生命周期和其他函数参数一样。当调用一个类的成员函数时，编译器将类的指针作为函数的 this 参数传递进去。
举个栗子：

```cpp
Student stu;
stu.print(3);
// 这时，编译器会将该句转换成
stu.print(&stu, 3);
```

#### **能不能在析构函数中 `delete this`**

    不能，因为 delete 一个指针，首先会调用构造函数，然后再释放空间，而析构函数中又会调用这一步，形成死循环。

举个栗子：
```cpp
#include <iostream>
using namespace std;
class test
{
public:
    ~test()
    {
        delete this;
        cout << "~test()" << endl;
    }
private:
};
int main()
{
    test t;
    return 0;
}
```

运行结果：![1554225043432](https://github.com/Mmmmmmi/MyNote/blob/master/resource/1554225043432.png)

</details>

<b><details><summary>类大小</summary></b>

```c++
#include <iostream>
using namespace std;
// 一个空类的大小为多少
// 在 VS2019 中测试的为 1
class test
{};
// 包含了一个成员变量的大小
// 与包含的成员类型，以及内存对齐有关
class test1
{
private:
    int a;
};
// 多了一个成员函数
// 成员函数并不会改变类的大小
class test2
{
public:
    void print()
    {
        cout << a << endl;
    }
private:
    int a;
};
// 若成员函数为虚函数
// 如果是虚函数，那么在类的前面需要加虚表指针
// 因此，多出的大小与虚表指针的大小有关
// 在 VS2019 32 位下，类大小为 8
// 在 VS2019 64 位下，类大小为 16
class test3
{
public:
    virtual void print()
    {
        cout << a << endl;
    }
private:
    int a;
};
// 若成员函数为静态成员函数
// 并不会影响类的大小
class test4
{
public:
    static void fun()
    {
        cout << "static fun" << endl;
    }
private:
    int a;
};
int main()
{
    cout << sizeof(test) << endl;
    cout << sizeof(test1) << endl;
    cout << sizeof(test2) << endl;
    cout << sizeof(test3) << endl;
    cout << sizeof(test4) << endl;
    return 0;
}
```

输出结果：![1554223919220](https://github.com/Mmmmmmi/MyNote/blob/master/resource/1554223919220.png)

</details>

<b><details><summary>重载</summary></b>

#### 不能重载的运算符
```cpp
 .*
 ::
 sizeof           // 这 5 个运算符不能重载
 ?:
 .
```
</details>

<b><details><summary>继承</summary></b>

#### **重写、重载、重定义的区别**

- **重载**，两个函数在同一个作用域下，并且函数名相同，参数列表不同。
- **重写（覆盖)**，两个函数分别在基类和派生类的作用域（作用域不同)，函数名 / 参数 / 返回值都必须相同（协变除外)，两个函数必须都为虚函数。
- **重定义（隐藏)**，两个函数分别在基类和派生类的作用域，函数名相同。两个基类和派生类的同名函数不构成重写就是重定义。

</details>

<b><details><summary>多态</summary></b>

#### **类的那些函数不能被定义为虚函数？**

- **构造函数**，因为虚表指针是在初始化列表阶段才初始化的，在调用构造函数时，虚表指针还未生成，因此无法调用。
- **静态成员函数**，静态成员函数中没有`this`指针，不能指向虚函数表，因此无法实现多态。
- **友元函数**，友元函数不属于类的成员函数，因此无法继承，也无法实现多态。
- **内联成员函数**，不一定，因为有可能直接展开没有地址。

</details>

## **动态内存管理**

<b><details><summary>malloc realloc calloc</summary></b>

</details>

<b><details><summary>free</summary></b>

</details>

<b><details><summary>new</summary></b>
new 会调用 operator new
```cpp

```

</details>

<b><details><summary>delete</summary></b></details>

<b><details><summary>定位 new</summary></b></details>

## **关键字**

<b><details><summary>static</summary></b>
</details>

<b><details><summary>const</summary></b>
</details>

<b><details><summary>explicit</summary></b>
</details>

<b><details><summary>inline</summary></b>
</details>

<b><details><summary>extern</summary></b>
</details>

<b><details><summary>decltype</summary></b>
</details>

<b><details><summary>volatile</summary></b>

- volatile 关键字是一种类型修饰符，用它声明的类型变量表示可以被某些编译器未知的因素（操作系统、硬件、其它线程等）更改。所以使用 volatile 告诉编译器不应对这样的对象进行优化。
- volatile 关键字声明的变量，每次访问时都必须从内存中取出值（没有被 volatile 修饰的变量，可能由于编译器的优化，从 CPU 寄存器中取值）
- const 可以是 volatile （如只读的状态寄存器）
- 指针可以是 volatile
- 适应场景：
  - 多线程编程中用到，比如线程安全的单例模式

</details>

## **STL**

<b><details><summary>容器</summary></b>

#### 序列式容器

- array
- string
- vector
- forward_list
- list
- deque

#### 关联式容器

红黑树结构

- map
- multimap
- set
- multiset

哈希结构

- unordered_map
- unordered_set
- unordered_multimap
- unordered_multiset

#### map与unordered_map区别

|                 | map                 | unordered_map                     |
| --------------- | ------------------- | :-------------------------------- |
| 相同：          |                     |                                   |
|                 | 都是存储键值对      | key不能重复                       |
| 不同：          |                     |                                   |
| 关于Key是否有序 | 有序                | 无序                              |
| 底层实现不同    | 红黑树              | hash表(开散列实现的hash桶)        |
| 查找效率不同    | O(log<sub>2</sub>N) | O(1) (通过hash函数得到hash桶入口) |
| 插入效率不同    |                     |                                   |
| 迭代器不同      | 双向的              | 单向的                            |
| 是否需要扩容    | 不需要扩容          | 需要扩容                          |





| 容器           | 底层数据结构      | 时间复杂度                                                   | 有无序 | 可不可重复 | 其他                                                         |
| -------------- | ----------------- | ------------------------------------------------------------ | :----- | ---------- | ------------------------------------------------------------ |
| array          | 数组              | 随机读改 O(1)                                                | 无序   | 可重复     | 支持快速随机访问                                             |
| vector         | 数组              | 随机读改、尾部插入、尾部删除 O(1)<br/>头部插入、头部删除 O(n) | 无序   | 可重复     | 支持快速随机访问                                             |
| list           | 双向链表          | 插入、删除 O(1)<br/>随机读改 O(n)                            | 无序   | 可重复     | 支持快速增删                                                 |
| deque          | 双端队列          | 头尾插入、头尾删除 O(1)                                      | 无序   | 可重复     | 一个中央控制器 + 多个缓冲区，支持首尾快速增删，支持随机访问  |
| stack          | deque / list      | 顶部插入、顶部删除 O(1)                                      | 无序   | 可重复     | deque 或 list 封闭头端开口，不用 vector 的原因应该是容量大小有限制，扩容耗时 |
| queue          | deque / list      | 尾部插入、头部删除 O(1)                                      | 无序   | 可重复     | deque 或 list 封闭头端开口，不用 vector 的原因应该是容量大小有限制，扩容耗时 |
| priority_queue | vector + max-heap | 插入、删除 O(log<sub>2</sub>n)                               | 有序   | 可重复     | vector容器+heap处理规则                                      |
| set            | 红黑树            | 插入、删除、查找 O(log<sub>2</sub>n)                         | 有序   | 不可重复   |                                                              |
| multiset       | 红黑树            | 插入、删除、查找 O(log<sub>2</sub>n)                         | 有序   | 可重复     |                                                              |
| map            | 红黑树            | 插入、删除、查找 O(log<sub>2</sub>n)                         | 有序   | 不可重复   |                                                              |
| multimap       | 红黑树            | 插入、删除、查找 O(log<sub>2</sub>n)                         | 有序   | 可重复     |                                                              |
| hash_set       | 哈希表            | 插入、删除、查找 O(1) 最差 O(n)                              | 无序   | 不可重复   |                                                              |
| hash_multiset  | 哈希表            | 插入、删除、查找 O(1) 最差 O(n)                              | 无序   | 可重复     |                                                              |
| hash_map       | 哈希表            | 插入、删除、查找 O(1) 最差 O(n)                              | 无序   | 不可重复   |                                                              |
| hash_multimap  | 哈希表            | 插入、删除、查找 O(1) 最差 O(n)                              | 无序   | 可重复     |                                                              |


</details>

<b><details><summary>迭代器</summary></b>
</details>

<b><details><summary>仿函数</summary></b>
</details>

<b><details><summary>适配器</summary></b>
</details>

<b><details><summary>算法</summary></b>
</details>

<b><details><summary>空间适配器</summary></b>
</details>

## **C++11**

<b><details><summary>列表初始化</summary></b>
</details>

<b><details><summary>范围 for</summary></b>
</details>

<b><details><summary>变量类型推导</summary></b>
</details>

<b><details><summary>类型转换</summary></b>
</details>

<b><details><summary>智能指针</summary></b>

- <b><details><summary>auto_ptr</summary></b>
  </details>

- <b><details><summary>unique_ptr</summary></b>
  </details>

- <b><details><summary>shared_ptr</summary></b>
  </details>

</details>

<b><details><summary>final 和 override</summary></b>
</details>

<b><details><summary>委派构造函数</summary></b>
</details>

<b><details><summary>右值引用</summary></b>
</details>

<b><details><summary>lambda 表达式</summary></b>
</details>

<b><details><summary>线程库</summary></b>
</details>

## **编译及调试**

<details><summary><b>静态库与动态库</b></summary>

- <details><summary><b>静态库（Static Library）</b></summary>

  **命名规则**，由三部分组成：`libxxx.a` 其中 `xxx` 为库的名字

  **创建步骤**
  ```c++
  //1. 源代码( c, cpp)，例如 test.cc
  //2. 生成对应的obj文件(.o)
  g++ -c test.cpp -o test.o
  //3. 打包
  ar rcs libtest.a test.o
  //ar  Linux 命令，建立或备存文件，或是从备存文件中抽取文件
  //ar 可以集合许多文件，成为单一的备存文件。在备存文件中，所有成员文件皆保有原来的属性与权限
  //r  将文件插入备存文件中。
  //c  建立备存文件
  //s  若备存文件中包含了对象模式，可利用此参数建立备存文件的符号表
  //4. 查看静态库内容
  nm libtest.a
  ```
  
  **使用方法**
  
  ```c++
  g++ main.cc -I ./ -L./ -ltest -o main
  //-L  指定库的路径
  //-l  指定库的名字(掐头去尾)
  //-I  指定头文件的路径
  //-o  指引生成文件的名字
  ```
  
  `Linux` 的 `gcc` 默认链接动态库，当动态库不存在时才会去链接静态库，若是需要强制指定静态库，需要加指定选项`-static`
  
    </details>
  
- <details><summary><b>动态库（共享库 Shared Library ）</b></summary>
  
  
  
  **命名规则**
  
  ```c++
  libxxx.so.x.y.z
  //xxx 动态库名
  //x 主版本号，不同主版本号的库之间不兼容，需要重新编译
  //y 次版本号，高版本号向后兼容低版本号
  //z 发布版本号，不对接口进行更改，完全兼容
  ```
  
  **路径**
  
  大部分包括 `Linux` 在内的开源系统遵循 `FHS（File Hierarchy Standard）`的标准，这标准规定了系统文件如何存放，包括各个目录结构、组织和作用。
  
  - `/lib` ：存放系统最关键和最基础的共享库，如动态链接器、C 语言运行库、数学库等
  - `/usr/lib` ：存放非系统运行时所需要的关键性的库，主要是开发库
  - `/usr/local/lib` ：存放跟操作系统本身并不十分相关的库，主要是一些第三方应用程序的库
  - 动态链接器会在 `/lib` 、`/usr/lib ` 和由 `/etc/ld.so.conf` 配置文件指定的，目录中查找共享库
  
  **环境变量**
  
  - `LD_LIBRARY_PATH`：临时改变某个应用程序的共享库查找路径，而不会影响其他应用程序
  - `LD_PRELOAD`：指定预先装载的一些共享库甚至是目标文件
  - `LD_DEBUG`：打开动态链接器的调试功能
  
  **创建步骤**
  
  ```c++
  //1. 源代码( c, cpp)，例如 test.cc
  //2. 生成对应的obj文件(.o)
  g++ -c test.cpp -fPIC -o test.o
  //3. 打包
  g++ -shared test.o -o libtest.a
  //4. 也可以2 3 一起执行
  g++ -shared -fPIC test.c -o libtest.so
  //-shared 该选项指定生成动态连接库（让连接器生成T类型的导出符号表，有时候也生成弱连接W类型的导出符号），不用该标志外部程序无法连接，相当于一个可执行文件
  //-fPIC：表示编译为位置独立的代码，不用此选项的话编译后的代码是位置相关的所以动态载入时是通过代码拷贝的方式来满足不同进程的需要，而不能达到真正代码段共享的目的
  //5. 也可用nm 查看，不过与静态库区别较大
  nm libtest.so
  ```
  
  **使用方法**
  
  ```c++
  g++ main.cc -I ./ -L./ -ltest -o main
  //-L  指定库的路径
  //-l  指定库的名字(掐头去尾)
  //-I  指定头文件的路径
  //-o  指引生成文件的名字
  //ldd main 可以查看可执行程序运行需要哪些库
  ```
  **加载方法**
  
  动态库在使用时，还需要加载，也就是添加到 `PATH` 中，否则就会出现库未找到的错误
  
  - 修改 `Path`
  
    临时加载
  
    -  `export LD_LIBRARY_PATH=动态库路径:$LD_LIBRARY_PATH`
  
    - `$` 是取值符， `:` 是拼接，用这个来覆盖原来的值
  
    永久加载
  
    - 用户级别，将上面的语句写入 `~/.bashrc` ，然后重启终端或者 `source ~/.bashrc`
  
    - 系统级别，将上面的语句写入 `/etc/profile` ，然后重启系统或者 `source /etc/profile`
  
  - 更新 `/etc/la.socache` 文件列表
  
    - 找到配置文件 `/etc/ld.so.conf`
    - 把动态库的绝对路径写入
    - 执行 `sudo ldconfig [-v]`
  - 通过调用 `dlopen, dlclose, dlsym` 函数
  
  </details>
</details>
