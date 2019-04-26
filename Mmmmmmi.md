# **学习网站**

- github面试总结：<https://github.com/huihut/interview>    

# **C++**

## **单例模式问题**

### **饿汉模式**

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



### **懒汉模式**

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



## **STL六大组件的问题**

### **容器**

### **迭代器**

### **仿函数**

### **适配器**

### **算法**

### **空间配置器**

## **`this`指针问题**

### **`this`指针什么时间产生**

this指针在成员函数的开始前构造，在成员函数的结束后清除，生命周期和其他函数参数一样。当调用一个类的成员函数时，编译器将类的指针作为函数的this参数传递进去。
举个栗子：

```
Student stu;
stu.print(3);
//这时，编译器会将该句转换成
stu.print(&stu, 3);
```



### **能不能在析构函数中`delete this`**

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
运行结果：![1554225043432](https://github.com/pickled-fish/View/blob/master/Mmmmmmi/resource/1554225043432.png)

## **类的相关问题**

### **类大小问题**

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

输出结果：![1554223919220](https://github.com/pickled-fish/View/blob/master/Mmmmmmi/resource/1554223919220.png)
	

### **重写、重载、重定义的区别**

- **重载**，两个函数在同一个作用域下，并且函数名相同，参数列表不同。
- **重写(覆盖)**，两个函数分别在基类和派生类的作用域(作用域不同)，函数名/参数/返回值都必须相同(协变除外)，两个函数必须都为虚函数。
- **重定义(隐藏)**，两个函数分别在基类和派生类的作用域，函数名相同。两个基类和派生类的同名函数不构成重写就是重定义。

### **类的那些函数不能被定义为虚函数？**

- **构造函数**，因为虚表指针是在初始化列表阶段才初始化的，在调用构造函数时，虚表指针还未生成，因此无法调用。
- **静态成员函数**，静态成员函数中没有`this`指针，不能指向虚函数表，因此无法实现多态。
- **友元函数**，友元函数不属于类的成员函数，因此无法继承，也无法实现多态。
- **内联成员函数**，不一定，因为有可能直接展开没有地址。

## **关键字**

### **static**

### **const**

### **explicit**

### **inline**

### **extern**

### **decltype**

### **volatile**

- volatile 关键字是一种类型修饰符，用它声明的类型变量表示可以被某些编译器未知的因素（操作系统、硬件、其它线程等）更改。所以使用 volatile 告诉编译器不应对这样的对象进行优化。
- volatile 关键字声明的变量，每次访问时都必须从内存中取出值（没有被 volatile 修饰的变量，可能由于编译器的优化，从 CPU 寄存器中取值）
- const 可以是 volatile （如只读的状态寄存器）
- 指针可以是 volatile
- 适应场景：
  - 多线程编程中用到，比如线程安全的单例模式

## **智能指针问题**

### **auto_ptr**

### **unique_ptr**

### **shared_ptr**

### 	**线程安全问题**

# **数据库**

## **安装**

 - windows

   1. 压缩包下载地址：<https://downloads.mysql.com/archives/community/>

      ![down](https://github.com/pickled-fish/View/blob/master/Mmmmmmi/resource/MySQLDown.png)

   2. 下载完成后，将压缩包解压到解压到你想安装的路径

      ![](https://github.com/pickled-fish/View/blob/master/Mmmmmmi/resource/MySQLPATH.png)

   3. 将该目录下的bin添加到系统path系统变量中，右键我的电脑(此电脑)--高级系统设置--环境变量--下面的系统变量（S）中的path变量---编辑--新建。

      ![](https://github.com/pickled-fish/View/blob/master/Mmmmmmi/resource/binpath.png)

      ![](https://github.com/pickled-fish/View/blob/master/Mmmmmmi/resource/addpath.png)

   4. 以管理员运行CMD(右键左下角 Window PowerShell 管理员也行)，进入MySQL的bin目录下，输入```mysqld install``` 再输入``` mysqld --initialize-insecure```

      ![](https://github.com/pickled-fish/View/blob/master/Mmmmmmi/resource/install.png)

      ![](https://github.com/pickled-fish/View/blob/master/Mmmmmmi/resource/init.png)

   5. 启动MySQL服务，在任意目录下均可。输入``` net start mysql```

      ![](https://github.com/pickled-fish/View/blob/master/Mmmmmmi/resource/start.png)

   6. 初次安装，配置root密码，在打开的CMD界面，输入 ``` mysql -u root```，首次登陆无需密码，直接回车。

      ![](https://github.com/pickled-fish/View/blob/master/Mmmmmmi/resource/rootinit.png)

   7. 输入``` ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';```   ```new password``` 为新密码。输入完成后，退出(输入quit)。

      ![](https://github.com/pickled-fish/View/blob/master/Mmmmmmi/resource/newpasswd.png)

   8. 重新登陆```mysql -u root -p```，这次输入新的密码登陆即可。

 - Centos 7

   1. 添加Yum Repository

      ``` wget -i -c http://dev.mysql.com/get/mysql80-community-release-el7-2.noarch.rpm```

      ``` mysql57-community-release-el7-10.noarch.rpm``` 是版本号，具体版本可以到

      [MySQL官网]: https://dev.mysql.com/downloads/repo/yum/

      修改。

   2. 下载完成后，安装``` yum -y install mysql80-community-release-el7-2.noarch.rpm```

   3. 库安装完之后，开始安装```yum -y install mysql-community-server ```

   4. 启动数据库服务，``` systemctl start  mysqld.service```，查看状态 ```systemctl status mysqld.service```

   5. 查看默认生成的```root```密码，``` grep "password" /var/log/mysqld.log```

   6. 登陆数据库，``` mysql -uroot -p```，输入刚才的密码

   7. 修改密码，``` ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';```，``` new password``` 为新的密码

   8. 问题：可能由于依赖不足产生问题，可以尝试下面的命令移除对` mariadb-libs`：``` yum -y remove mariadb-libs ``` 

   

## **数据库事务的四大特性**

- **原子性（Atomicity）**，原子性是指事务包含的所有操作要么全部成功，要么全部失败回滚，因此事务的操作如果成功就必须要完全应用到数据库，如果操作失败则不能对数据库有任何影响。
- **一致性（Consistency）**，一致性是指事务必须使数据库从一个一致性状态变换到另一个一致性状态，也就是说一个事务执行之前和执行之后都必须处于一致性状态。拿转账来说，假设用户A和用户B两者的钱加起来一共是5000，那么不管A和B之间如何转账，转几次账，事务结束后两个用户的钱相加起来应该还得是5000，这就是事务的一致性。
- **隔离性（Isolation）**，隔离性是当多个用户并发访问数据库时，比如操作同一张表时，数据库为每一个用户开启的事务，不能被其他事务的操作所干扰，多个并发事务之间要相互隔离。即要达到这么一种效果：对于任意两个并发的事务T1和T2，在事务T1看来，T2要么在T1开始之前就已经结束，要么在T1结束之后才开始，这样每个事务都感觉不到有其他事务在并发地执行。
- **持久性（Durability）**，持久性是指一个事务一旦被提交了，那么对数据库中的数据的改变就是永久性的，即便是在数据库系统遇到故障的情况下也不会丢失提交事务的操作。例如我们在使用JDBC操作数据库时，在提交事务方法后，提示用户事务操作完成，当我们程序执行完成直到看到提示后，就可以认定事务已经正确提交，即使这时候数据库出现了问题，也必须要将我们的事务完全执行完成，否则就会造成我们看到提示事务处理完毕，但是数据库因为故障而没有执行事务的重大错误。

# **Linux**

# **计算机网络**

## **TCP的网络拥堵在应用层怎么判断**

1. 通过丢包率来判断
2. 通过网络到达的延迟来判断

## **DNS服务器用什么协议来实现**
1. UDP协议
