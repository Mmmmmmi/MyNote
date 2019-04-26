# **C++**
- [**类和对象**](#类和对象)
- [**关键字**](#关键字)
- [**STL**](#stl)
- [**C++11**](#c11)
- [**设计模式**](#设计模式)
## **类和对象**

###
<b><details><summary>this指针</summary></b>

#### **`this`指针什么时间产生**

this指针在成员函数的开始前构造，在成员函数的结束后清除，生命周期和其他函数参数一样。当调用一个类的成员函数时，编译器将类的指针作为函数的this参数传递进去。
举个栗子：

```
Student stu;
stu.print(3);
//这时，编译器会将该句转换成
stu.print(&stu, 3);
```

#### **能不能在析构函数中`delete this`**

不能，因为`delete`一个指针，首先会调用构造函数，然后再释放空间，而析构函数中又会调用这一步，形成死循环。

```C++
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

运行结果：![1554225043432](https://github.com/Mmmmmmi/MyNote/tree/master/resource/1554225043432.png)

</details>

###
<b><details><summary>类大小</summary></b>

```c++
#include <iostream>
using namespace std;
//一个空类的大小为多少
//在VS2019中测试的为1
class test
{};
//包含了一个成员变量的大小
//与包含的成员类型，以及内存对齐有关
class test1
{
private:
    int a;
};
//多了一个成员函数
//成员函数并不会改变类的大小
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
//若成员函数为虚函数
//如果是虚函数，那么在类的前面需要加虚表指针
//因此，多出的大小与虚表指针的大小有关
//在VS2019 32位下，类大小为8
//在VS2019 64位下，类大小为16
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
//若成员函数为静态成员函数
//并不会影响类的大小
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

输出结果：![1554223919220](https://github.com/Mmmmmmi/MyNote/tree/master/resource/1554223919220.png)

</details>

### **重写、重载、重定义的区别**

- **重载**，两个函数在同一个作用域下，并且函数名相同，参数列表不同。
- **重写(覆盖)**，两个函数分别在基类和派生类的作用域(作用域不同)，函数名/参数/返回值都必须相同(协变除外)，两个函数必须都为虚函数。
- **重定义(隐藏)**，两个函数分别在基类和派生类的作用域，函数名相同。两个基类和派生类的同名函数不构成重写就是重定义。

### **类的那些函数不能被定义为虚函数？**

- **构造函数**，因为虚表指针是在初始化列表阶段才初始化的，在调用构造函数时，虚表指针还未生成，因此无法调用。
- **静态成员函数**，静态成员函数中没有`this`指针，不能指向虚函数表，因此无法实现多态。
- **友元函数**，友元函数不属于类的成员函数，因此无法继承，也无法实现多态。
- **内联成员函数**，不一定，因为有可能直接展开没有地址。

</details>

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

### **智能指针**

<b><details><summary>auto_ptr<summary></b>
</details>

<b><details><summary>unique_ptr<summary></b>
</details>

<b><details><summary>shared_ptr<summary></b>
</details>

## **设计模式**

###
<b><details><summary>单例模式</summary></b>

#### **饿汉模式**

```c++
//饿汉模式
//程序启动时，就将对象创建好
class Singleton1
{
public:
    static Singleton1* getSingleton1()
    {
        _singleton = new Singleton1();
        return _singleton;
    }
    class Free
    {
    public:
        ~Free()
        {
            if (_singleton != nullptr) {
                std::cout << "delete _singleton" << std::endl;
                delete _singleton;
                _singleton = nullptr;
            }
        }
    };
    static Free free;
private:
    //单例模式，所以只允许存在一份
    //将构造函数定义为私有
    Singleton1()
    {
        std::cout << "Singleton1：" << this << std::endl;
    }
    //不允许存在拷贝构造以及复制运算符重载   
    Singleton1(const Singleton1&);
    Singleton1& operator=(const Singleton1&);

    static Singleton1* _singleton;
};
Singleton1* Singleton1::_singleton;
Singleton1::Free Singleton1::free;
```

#### **懒汉模式**

```c++
#include <mutex>
#include <thread>

//懒汉模式
class Singleton2
{
public:
    //创建
    static volatile Singleton2* getSingleton2()
    {
        //可能有编译器优化，指令重排的问题，也就是空间申请了，但是没有调用构造函数，
        //这样就可能全部阻塞到锁那里，因此再加一层判断。
        if (_p == nullptr) {
            //这样可能造成线程全部堵塞在这
            //如果为空就创建
            _m.lock();
            if (_p == nullptr) {
                _p = new Singleton2();
            }
            _m.unlock();
        }
        return _p;
    }
    //释放，不能写在析构函数中，因为delete 会调用析构函数 析构函数又会调用delete  死循环
    //也不能直接写一个释放函数，因为，如果让线程来调用，那么应该让最后一个使用的线程的释放，
    //但是你不知道那个线程是最后一个，因此，应该写一个嵌套类来释放
    class Free
    {
    public:
        ~Free()
        {
            if (_p != nullptr) {
                std::cout << "delete _p" << std::endl;
                delete _p;
                _p = nullptr;
            }
        }
    };
    static Free free;
private:
    //只能在类中调用构造函数，这样，加上控制条件后，只能创建一份了
    Singleton2()
    {
        std::cout << "Singleton2：" << this << std::endl;
    }
    Singleton2 (const Singleton2&);

    Singleton2& operator=(const Singleton2&);

    static volatile Singleton2* _p;
    static mutex _m;
    static Free _free;
};
volatile Singleton2* Singleton2::_p = nullptr;
mutex Singleton2::_m;
Singleton2::Free Singleton2::free;
```

</details>

###
<b><details><summary>观察者模式</summary></b>