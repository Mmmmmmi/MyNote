# **Python3**
    自己学习过程中做的笔记，仅做记录之用

  - [**基本语法**](#基本语法)
  - [**基本数据类型**](#基本数据类型)

## **基本语法**

<b><details><summary>注释</summary></b>

- **单行注释以 ` # ` 开头**

```python
# Here are the comments
```

- **多行注释以 三个单引号（` ''' `） 或 三个双引号（`""""`） 开头和结尾，但首尾使用的应该相同**

```python
'''
This is a mulyiline comment
used in Python
'''

"""
This is a mulyiline comment
used in Python
"""

```

</details>

<b><details><summary>行与缩进</summary></b>

- **空行**，函数之间或类的方法之间用空行分隔，表示一段新的代码的开始。类和函数入口之间也用一行空行分隔，以突出函数入口的开始。

- **缩进**，`python` 使用缩进来表示代码块，缩进的空格数是可变的，但是同一个代码块的语句必须包含相同的缩进空格数

```python
if True :
    print("True")
else :
    print("False")
```
- **多行语句**，`python` 中如果一行语句过长，可以用反斜杠 (\) 实现多行语句，在 '()' `[]` '{}'中的多行语句，不需要反斜杠 (\)
```python
print(" hello \
        world \
        !")
```
- **同一行显示多条语句**，`python` 可以同一行中使用多条语句，语句之间使用`;` 分割

</details>

<b><details><summary>保留字</summary></b>

- **`Python` 的标准库提供了一个 `keyword` 模块，可以输出当前版本的所有关键字**

```python
import keyword
keyword.kwlist

# 结果
['False', 'None', 'True', 'and', 'as', 'assert', 'async',
'await', 'break', 'class', 'continue', 'def', 'del', 'elif',
'else', 'except', 'finally', 'for', 'from', 'global', 'if',
'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or',
'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
```
</details>

<b><details><summary>输入函数</summary></b>

- **input() 函数从控制台获得用户输入，获取的用户输入以字符串形式保存在<变量>中**

```python
# <变量> = input (<提示性文字>)
val  = input("please input")
```

</details>

<b><details><summary>输出函数</summary></b>

- **print() 用来输出字符信息，或以字符串形式输出变量**
- **print() 用 `%` 选择需要输出的变量**
- **print() 默认输出是换行的，如果要实现不换行需要在末尾加上 `end=""`**

```python
a = 5.2
print("a = %.2f"%a)
```

</details>

<b><details><summary>运算符</summary></b>

| 运算符                                                        | 描述                           |
| ------------------------------------------------------------ | ------------------------------|
| `[]` `[:]`                                                   | 下标，切片                      |
| `**`                                                         | 指数                           |
| `~` `+` `-`                                                  | 按位取反, 正负号                 |
| `*` `/` `%` `//`                                             | 乘，除，模，整除                 |
| `+` `-`                                                      | 加，减                         |
| `>>` `<<`                                                    | 右移，左移                     | 
| `&`                                                          | 按位与                         |
| `^` `\|`                                                     | 按位异或，按位或               |
| `<=` `<` `>` `>=`                                            | 小于等于，小于，大于，大于等于 |
| `==` `!=`                                                    | 等于，不等于                   |
| `is`  `is not`                                               | 身份运算符                     |
| `in` `not in`                                                | 成员运算符                     |
| `not` `or` `and`                                             | 逻辑运算符                     |
| `=``+=``-=``*=``/=``%=``//=``**=` `&=` `|=` `^=` `>>=` `<<=` | （复合）赋值运算符             |

</details>

<b><details><summary>import 与 from...import</summary></b>
- 在 `python` 中使用 `import` 或者 `from...import` 来导入相应的模块
- 将整个模块 `(somemodule)` 导入，格式为 `import somemodule` 
- 从某个模块中导入某个函数，格式为 `from somemodule import somefunction`
- 从某个模块中导入多个函数，格式为 `from somemodule import firstfunc, secondfunc, thirdfunc`
- 将某个模块中的全部函数导入，格式为 `from somemodule import *`
</details>

<b><details><summary>表达式</summary></b>

- **字符串操作**
    - 操作符 `+` 可以实现两个字符串的连接操作
    - 字符串可理解为字节序列，若长度为 `L`, 第一个字节索引为 `0` 或 `-L`，最后一个字节索引为 `L-1` 或 `-1`
    - 以区间形式获得字符串的子串，左闭右开
    ```python
    tIndex = "python"
    tIndex[4]
    #   'o'
    tIndex[-4]
    #   't'
    tIndex[1:-2]
    #   'yth'
    ```

- **赋值操作**
    - 普通赋值操作
    ```python
    f = 1
    ```

    - 同步赋值操作
    <变量 1>,...,<变量 N> = <表达式 1>,...<表达式 N>
    ```python
    # 交换两个数 x 和 y 的值
    # 普通         同步
    t = x    |
    x = y    |  x,y = y,x
    y = t    |

    ```

</details>

<b><details><summary>分支语句</summary></b>
```python
if <条件 1 成立> :
    <表达式 1>
elif <条件 2 成立> :
    <表达式 2>
......
else :
    <表达式 N>
```

</details>

<b><details><summary>循环语句</summary></b>

- **`while` 循环**
```python
# while <判断条件> :
#    <表达式>

# 循环执⾏行行三次 print counter = 0
counter = 0
while counter < 3 :
    print (count)
    counter += 1
# 运行结果
0
1
2
```

- **`while` 循环使用 else 语句**, 在 `while … else` 在条件语句为 `false` 时执行 `else` 的语句块，可以使用 `break` 来提前终止循环
```python

counter = 0
while counter < 3 :
    print("counter < 3")
    counter += 1
else :
    print("counter > 3")
    counter += 1
# 运行结果
counter < 3
counter > 3

```

- **`for` 循环**

```python
# for i in range (<计数值>) :
#    <表达式>

# 遍历字符串的每一个字符
a = "hello"
for c in a :
    print(c)
# 执行结果
h
e
l
o

# 遍历列表中的每一个元素
a = [1, 2, 3, 4]
for item in a :
    print(item)
# 执行结果
1
2
3
4

# 遍历字典中所有的 key-value
a = {'ip' : '127.0.0.1', 'port' : '80'}
for key in a :
    print(key, a[key])
# 执行结果
ip 127.0.0.1
port 80
```
- **`range` 函数**, 内建函数 `range` 能够生成一个数字组成的列表，方便进⾏ `for` 循环遍历，`range` 函数有三个参数。前两个参数分别表示了⼀个前闭后开的区间。第三个参数表示 `step`, 即每次迭代的步长
```python

# 遍历区间 [0, 5)
for i in range(5) :
    print(i)
# 运行结果
0
1
2
3
4

# 遍历区间 [0, 5), 也可以直接指定区间
for i in range(0, 5) :
    print(i)
# 运行结果
0
1
2
3
4

# 遍历区间 [0, 10) 中的偶数
for i in range(0, 10, 2) :
    print(i)
# 运行结果
0
2
4
6
8

# 也可以为负数， 遍历区间 [-10, -15)
for i in range(-10, -15, -1) :
    print(i)
# 运行结果
-10
-11
-12
-13
-14
```

</details>

<b><details><summary>函数模块</summary></b>

- **定义函数**
```python
# Python 中用 def 关键字来定义函数
def function() :
    print("function")
```
- **用模块管理函数**，`python` 中每个文件代表了一个模块(module)，在不同的模块中可以有同名的函数，在使用函数的时候通过 `import` 关键字导入指定的模块就可以区分到底要使用的是哪个模块中的 `foo` 函数
```python
# module1.py
def foo():
    print("module1.py")
# module2.py
def foo():
    print("module2.py")

# 不用导入时机的不同效果
# main.py
from module1 import foo
foo()   # module1.py
from module2 import foo
foo()   # module2.py

# main.py
from module1 import foo
from module2 import foo
foo()   #module2.py  调用的是最后导入的

# main.py
import module1 as m1 
import module2 as m2
m1.foo()    #module1.py
m2.foo()    #module2.py
```
- 如果导入的模块除了定义函数之外还中有可以执行代码，那么 `python` 解释器在导入这个模块时就会执行这些代码，事实上我们可能并不希望如此，因此如果在模块中编写了执行代码，最好是将这些执行代码放入如下所示的条件中，这样的话除非直接运行该模块， `if` 条件下的这些代码是不会执行的，因为只有直接执行的模块的名字才是 `__main__`
```python
# module.py
def foo():
    print("module")
# __name__ 是 python 中一个隐含的变量它代表了模块的名字
# 只有被 python 解释器直接执行的模块的名字才是 __main__
if __name__ == "__main__":
    print("__main__")

# test.py
import module
foo()   # 只会输出 “module”, 不会输出上面的 “__main__”
```
</details>

## **基本数据类型**

<b><details open><summary>数字</summary></b>

- `python3` 支持 `int`，`float`，`bool`，`complex（复数）`
- 在 `python3` 中，只有一种整数类型 `int`，表示为长整型，没有 `python2` 中的 `long`
- 内置的 `type()` 函数可以查询变量所指的对象类型，还可以用 `isinstance()` 来判断。两者的区别：
  - `type()` 不会认为子类是一种父类类型
  - `isinstance()` 会认为子类是一种父类类型

```python
a, b, c, d = 32, 5.5, True, 4+3j
print(type(a), type(b), type(c), type(d))
# <class 'int'> <class 'float'> <class 'bool'> <class 'complex'>
print(isinstance(a, int))
print(isinstance(b, float))
print(isinstance(c, bool))
print(isinstance(d, complex))
# True
# True
# True
# True
class A :
    pass
class B(A) :
    pass
print(isinstance(A(), A))
# True
print(type(A()) == A)
# True
print(isinstance(B(), A))
# True
print(type(B()) == A)
# False
```
- 当指定一个值时， `Number` 对象就会被创建

```python
var1 = 1
var2 = 2.2
var3 = 3
```
- 可以使用 `del` 语句删除一些对象的引用，`del` 可以删除单个或多个对象

```python
del var1
del var2, var3
``` 

- 数值运算
  
```python
5 / 2     # 除法，得到一个浮点数
# 2.5
5 // 2    # 除法，得到一个整数
# 2
5 ** 2    # 乘方
# 25
```

- 注意
  - `python` 可以同时为多个变量赋值，如 `a, b = 1, 2`
  - 一个变量可以通过赋值指向不同类型的对象
  - 在混合运算时，`python` 会把整型转化为浮点数


</details>

<b><details><summary>字符串</summary></b>

```python
# 单个或多个字符用单引号或者双引号包围起来
s1 = 'hello, world!'
s2 = "hello, world!"

# 以三个双引号或者单引号开头的字符可以折行
s3 = """
hello,
world!
"""
s4 = '''
hello,
world!
'''
# 可以在字符串中使用 \ 来表示转义
# 要表示 \ 需要写成 \\  要表示 ‘ 需要写成 \'
s1 = "\'\\"

# \ 后面跟一个八进制或者十六进制数来表示字符，也可以跟 Unicode 字符编码来表示字符
s1 = "\141\x63\u0065"

# 如果不希望字符串中的 \ 表示转义，可以通过在字符串最前面加上字母 r 来说明
s1 = r"\'hello, world!"

# 可以使用 + 进行拼接，使用 * 运算符重复字符串的内容，使用 in 和 not in 来判断一个字符串是否包含另外一个字符串（成员运算），也可以用[]和[:]运算符从字符串取出某个字符或某些字符（切片运算）

# 格式化输出字符串
a, b = 2, 10
print("%d + %d = %d" % (a, b, a * b))

# 也可以用字符串提供的方法实现输出
print('{0} * {1} = {2}'.format(a, b, a * b))

# python3.6 以后可以直接在字符串前面加上 f 
print(f"{a} * {b} = {a * b}")
```

</details>

<b><details><summary>列表</summary></b>

```python
# 定义
list1 = [1, 2, 3, 4, 5]

# * 代表元素的重复
list2 = ["M"] * 3 # ["M", "M", "M"]

# 计算列表长度（元素个数）
len(list1)

# 通过enumerate函数处理列表之后再遍历可以同时获得元素索引和值
for index, elem in enumerate(list1):
    print(index, elem)

# 操作 list 中的元素
list1 = [1, 2, 3, 4, 5]

# 添加
list1.append(200)
list1.insert(1, 1200)

# 排序，直接在列表对象上排序
list1.sort(reverse=True)

# 排序，sorted 函数返回列表排序后的拷贝不会影响传入的列表
list2 = sorted(list1, reverse=True)

# 通过 key 关键字参数指定根据字符串长度进行排序而不是默认的字母表排序
list3 = sorted(list1, key=len)
```

</details>

<b><details><summary>生成式和生成器</summary></b>

```python
# 可以使用列表的生成式语法来创建列表
f = [x for x in range(1, 10)]
# [1, 2, 3, 4, 5, 6, 7, 8, 9] 

f = [x + y for x in "ABC" for y in "123"]
# ['A1', 'A2', 'A3', 'B1', 'B2', 'B3', 'C1', 'C2', 'C3']

# 用列表的生成表达式语法创建列表容器
# 用这种语法创建列表之后元素已经准备就绪所以需要耗费较多的内存空间
f = [x ** 2 for x in range(1, 10)]
print(sys.getsizeof(f))
print(f)

# 通过下面的代码创建一个生成器对象
# 通过生成器可以获取到数据但它不占用额外的空间存储数据
# 每次需要数据的时候就通过内部的运算得到数据(需要花费额外的时间)
f = (x ** 2 for x in range(1, 10))
print(sys.getsizeof(f))  # 相比生成式生成器不占用存储数据的空间
print(f)
for val in f:
    print(val)

# 使用 yield 将普通函数改造成生成器函数
# fib 是一个生成斐波那契数列的生成器
def fib(n):
    a, b = 0, 1
    for _ in range(n):
        a, b = b, a + b
        yield a

def main():
    for val in fib(20):
        print(val)

if __name__ == '__main__':
    main()
```
</details>

<b><details><summary>元组</summary></b>

```python
# python中的元组与列表类似也是一种容器数据类型，可以用一个变量（对象）来存储多个数据，不同之处在于元组的元素不能修改。顾名思义，我们把多个元素组合到一起就形成了一个元组，所以它和列表一样可以保存多条数据

# 特点
# 1. 元组中的元素是无法修改的，事实上我们在项目中尤其是多线程环境（后面会讲到）中可能更喜欢使用的是那些不变对象（一方面因为对象状态不能修改，所以可以避免由此引起的不必要的程序错误，简单的说就是一个不变的对象要比可变的对象更加容易维护；另一方面因为没有任何一个线程能够修改不变对象的内部状态，一个不变对象自动就是线程安全的，这样就可以省掉处理同步化的开销。一个不变对象可以方便的被共享访问）。所以结论就是：如果不需要对元素进行添加、删除、修改的时候，可以考虑使用元组，当然如果一个方法要返回多个值，使用元组也是不错的选择
# 2. 元组在创建时间和占用的空间上面都优于列表。可以使用sys模块的getsizeof函数来检查存储同样的元素的元组和列表各自占用了多少内存空间

# 定义元组
t = ("h", "e", "l", "l", "o", 1, 2, True)

# 获取元组中元素
print(t[0])

# 遍历元组中元素
for member in t :
    print(member)

# 重新给元组赋值
t[0] = "111" # TypeError: 'tuple' object does not support item assignment


# 变量 t 重新引用了新的元组，原来的元组将会被垃圾回收
t = ("hello", 3, False)

# 将元组转换为列表
l1 = list(t)

# 将列表转化为元组
l2 = ["apple", "banana"]
t2 = tuple(l2)
print(t2)
```
</details>

<b><details><summary>集合</summary></b>

```python
# python 中的集合和数学上的集合是一致的，不允许有重复元素，而且可以进行交集、并集、差集等运算

# 创建集合
set1 = {1, 2, 3, 4}

# 创建集合的构造语法
set2 = set{range(1, 10)}
set3 = set{range(1, 0, 4, 3)}

# 创建集合的推导式语法
set4 = {num for num in range(1, 10) if num % 3 == 0 or num % 5 == 0}
# {9, 3, 5, 6}

# 向集合中添加删除元素
set4.add(4)
set4.update([2, 999])
set4.discard(5)
if 4 in set4 :
    set4.remove(4)
set4.pop()

# 集合的成员、交集、并集、差集等运算
set1 = {1, 2, 3, 4}
set1 = {3, 4, 5, 6}
print(set1 & set2)
# print(set1.intersection(set2))
print(set1 | set2)
# print(set1.union(set2))
print(set1 - set2)
# print(set1.difference(set2))
print(set1 ^ set2)
# print(set1.symmetric_difference(set2))
# 判断子集和超集
print(set2 <= set1)
# print(set2.issubset(set1))
print(set3 <= set1)
# print(set3.issubset(set1))
print(set1 >= set2)
# print(set1.issuperset(set2))
print(set1 >= set3)
# print(set1.issuperset(set3))
```
</details>

<b><details><summary>字典</summary></b>

```python
# 字典是另一种可变容器模型，python中的字典跟我们生活中使用的字典是一样一样的，它可以存储任意类型对象，与列表、集合不同的是，字典的每个元素都是由一个键和一个值组成的“键值对”，键和值通过冒号分开

# 创建字典
scores = {"Name" : "Will", "Age" : 18}

# 创建字典的构造器语法
items1 = dict(A = "a", B = "b", C = True, D = 1)

# 通过zip函数将两个序列压缩成字典
items2 = dict(zip(['a', 'b', 'c'], '123'))
# {'a': '1', 'b': '2', 'c': '3'}

# 通过键可以获取字典中对应的值
print(scores["Name"])
# get方法也是通过键获取对应的值，但是 get 方法可以设置默认值，如果指定键的值不存在时，返回该默认值
print(scores.get("Name"))
print(scores.get("N", "Not Found"))

# 遍历字典
for key in scores : 
    print(f"{key} : {scores[key]}")

# 更新字典中的元素
scores["Age"] = 20
scores.update(name = "M", Age = 20)

# 删除字典中的元素
scores.popitem()
scores.pop("Name", "Will")

# 清空字典
scores.clear()
```
</details>

<b><details><summary>变量和类型</summary></b>

- **类型转换**

  `int()`  ：将一个数值或字符串转换成整数，可以指定进制

  `float()`：将一个字符串转换成浮点数

  `str()`  ：将指定的对象转换成字符串形式，可以指定编码

  `chr()`  ：将整数转换成该编码对应的字符串（一个字符）

  `ord()`  ：将字符串(一个字符)转换成对应的编码(整数)

</details>