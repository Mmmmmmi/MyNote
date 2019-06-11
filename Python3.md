# **Python3**
    自己学习过程中做的笔记，仅做记录之用


  - [**基本语法**](#基本语法)

## **基本语法**

<b><details><summary>注释</summary></b>

- 单行注释以 ` # ` 开头

```python
    # Here are the comments
```

- 多行注释以 ` ''' ` 开头和结尾

```python
    '''
    This is a mulyiline comment
    used in Python
    '''
```
</details>



<b><details><summary>输入函数</summary></b>
- input() 函数从控制台获得用户输入,获取的用户输入以字符串形式保存在<变量>中

```python
    # <变量> = input (<提示性文字>)
    val  = input("please input")
```
</details>

<b><details><summary>输出函数</summary></b>

- print() 用来输出字符信息，或以字符串形式输出变量
- print() 用 `%` 选择需要输出的变量
```python
    a = 5.2
    print("a = %.2f"%a)
```
</details>

<b><details><summary>表达式</summary></b>
- 字符串操作
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
- 赋值操作
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

```python
    # for i in range (<计数值>) :
    #    <表达式>

    # 输出10个hello
    for i in range (10) :
        print("hello")
```

</details>