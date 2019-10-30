# **Python3**
    自己学习过程中做的笔记，仅做记录之用

  - [**基本语法**](#基本语法)

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

- **缩进**，python 使用缩进来表示代码块，缩进的空格数是可变的，但是同一个代码块的语句必须包含相同的缩进空格数

```python
if True :
    print("True")
else :
    print("False")
```
- **多行语句**，python 中如果一行语句过长，可以用反斜杠 (\) 实现多行语句，在 '()' `[]` '{}'中的多行语句，不需要反斜杠 (\)
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

<b><details><summary>变量和类型</summary></b>

- **类型转换**

  `int()`  ：将一个数值或字符串转换成整数，可以指定进制

  `float()`：将一个字符串转换成浮点数

  `str()`  ：将指定的对象转换成字符串形式，可以指定编码

  `chr()`  ：将整数转换成该编码对应的字符串（一个字符）

  `ord()`  ：将字符串(一个字符)转换成对应的编码(整数)

</details>

<b><details><summary>字符串和常用数据结构</summary></b>

- **字符串**
- **列表**
- **生成式和生成器**
- **元组**
- **集合**
- **字典**



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


<b><details><summary>表达式</summary></b>

- **字符串操作**
    - 操作符 + 可以实现两个字符串的连接操作
    - 字符串可理解为字节序列，若长度为 L, 第一个字节索引为 0 或 -L，最后一个字节索引为 L-1 或 -1
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
</deatils>


