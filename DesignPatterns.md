
# **DesignPatterns**

# 单例模式

<b><details><summary>饿汉模式</summary></b>

```cpp
// 饿汉模式
// 程序启动时，就将对象创建好
class Singleton1
{
public:
    static Singleton1* getSingleton1()
    {
        return _singleton;
    }
    //Free类在进程结束时自动销毁，在它的析构函数中 delete 掉了 _singleton
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
    // 单例模式，所以只允许存在一份
    // 将构造函数定义为私有
    Singleton1()
    {
        std::cout << "Singleton1：" << this << std::endl;
    }
    // 不允许存在拷贝构造以及复制运算符重载
    Singleton1(const Singleton1&);
    Singleton1& operator=(const Singleton1&);
    static Singleton1* _singleton;
};
Singleton1* Singleton1::_singleton = new Singleton1();
Singleton1::Free Singleton1::free;
```

#### **懒汉模式**

```cpp
#include <mutex>

// 懒汉模式
class Singleton2
{
public:
    // 创建
    static volatile Singleton2* getSingleton2()
    {
        // 可能有编译器优化，指令重排的问题，也就是空间申请了，但是没有调用构造函数，
        // 这样就可能全部阻塞到锁那里，因此再加一层判断。
        if (_p == nullptr) {
            // 这样可能造成线程全部堵塞在这
            // 如果为空就创建
            _m.lock();
            if (_p == nullptr) {
                _p = new Singleton2();
            }
            _m.unlock();
        }
        return _p;
    }
    // 释放，不能写在析构函数中，因为 delete 会调用析构函数 析构函数又会调用 delete  死循环
    // 也不能直接写一个释放函数，因为，如果让线程来调用，那么应该让最后一个使用的线程的释放，
    // 但是你不知道那个线程是最后一个，因此，应该写一个嵌套类来释放
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
private:
    // 只能在类中调用构造函数，这样，加上控制条件后，只能创建一份了
    Singleton2()
    {
        std::cout << "Singleton2：" << this << std::endl;
    }
    Singleton2 (const Singleton2&);
    Singleton2& operator=(const Singleton2&);
    static volatile Singleton2* _p;
    static std::mutex _m;
    static Free _free;
};
volatile Singleton2* Singleton2::_p = nullptr;
std::mutex Singleton2::_m;
Singleton2::Free Singleton2::_free;
```

</details>

<b><details><summary>观察者模式</summary></b>
</details>