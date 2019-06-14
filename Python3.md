# **Python3**
    自己学习过程中做的笔记，仅做记录之用


  - [**基本语法**](#基本语法)

## **基本语法**


<b><details><summary>注释</summary></b>

- **单行注释以 ` # ` 开头**

```python
# Here are the comments
```

- **多行注释以 ` ''' ` 或 `""""` 开头和结尾,但首尾使用的应该相同**

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

- **缩进**，python使用缩进来表示代码块, 缩进的空格数是可变的, 但是同一个代码块的语句必须包含相同的缩进空格数

```python
if True :
    print("True")
else :
    print("False")
```
- **多行语句**，python中如果一行语句过长，可以用反斜杠(\)实现多行语句，在 '()' `[]` '{}'中的多行语句，不需要反斜杠(\)
```python
print(" hello \
        world \
        !")
```

</details>

<b><details><summary>保留字</summary></b>
- **Python 的标准库提供了一个 keyword 模块，可以输出当前版本的所有关键字**
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

- **input() 函数从控制台获得用户输入,获取的用户输入以字符串形式保存在<变量>中**

```python
# <变量> = input (<提示性文字>)
val  = input("please input")
```

</details>

<b><details><summary>输出函数</summary></b>

- **print() 用来输出字符信息，或以字符串形式输出变量**
- **print() 用 `%` 选择需要输出的变量**

```python
a = 5.2
print("a = %.2f"%a)
```

</details>

<b><details><summary>表达式</summary></b>
- **字符串操作**
    - 操作符 + 可以实现两个字符串的连接操作
    - 字符串可理解为字节序列,若长度为L,第一个字节索引为0或-L，最后一个字节索引为L-1或-1
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
    <变量1>,...,<变量N> = <表达式1>,...<表达式N>
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
if <条件1成立> :
    <表达式1>
elif <条件2成立> :
    <表达式2>
......
else :
    <表达式N>
```

</details>

<b><details><summary>循环语句</summary></b>

- **while循环**
```python
# while <判断条件> :
#    <表达式>

# 循环执⾏行行三次print counter = 0
counter = 0
while counter < 3 :
    print (count)
    counter += 1
# 运行结果
0
1
2
```

- **while 循环使用 else 语句**, 在 while … else 在条件语句为 false 时执行 else 的语句块
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
counter < 3
counter < 3
counter > 3

```

- **for循环**

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

# 遍历字典中所有的key-value
a = {'ip' : '127.0.0.1', 'port' : '80'}
for key in a :
    print(key, a[key])
# 执行结果
ip 127.0.0.1
port 80
```
- **range函数**, 内建函数range能够生成一个数字组成的列表, 方便进⾏for循环遍历, range函数有三个参数. 前两个参数分别表示了⼀个前闭后开的区间. 第三个参数表示step, 即每次迭代的步长
```python

# 遍历区间[0, 5)
for i in range(5) :
    print(i)
# 运行结果
0
1
2
3
4

# 遍历区间[0, 5), 也可以直接指定区间
for i in range(0, 5) :
    print(i)
# 运行结果
0
1
2
3
4

# 遍历区间[0, 10)中的偶数
for i in range(0, 10, 2) :
    print(i)
# 运行结果
0
2
4
6
8

# 也可以为负数， 遍历区间[-10, -15)
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
