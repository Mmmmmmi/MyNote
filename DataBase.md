# **数据库**
   [**MySQL**](#MySQL)

   [**Redis**](#Redis)

## [**MySQL**](https://www.mysql.com/)
### **安装**

- <b><details><summary>**Windows**</summary></b>

  1. 压缩包下载地址：<https://downloads.mysql.com/archives/community/>
     ![down](https://github.com/Mmmmmmi/MyNote/blob/master/resource/MySQLDown.png)
  2. 下载完成后，将压缩包解压到解压到想安装的路径。
     ![](https://github.com/Mmmmmmi/MyNote/blob/master/resource/MySQLPATH.png)
  3. 将该目录下的 bin 添加到系统 path 系统变量中，右键我的电脑（此电脑)-- 高级系统设置 -- 环境变量 -- 下面的系统变量（S）中的 path 变量 --- 编辑 -- 新建。
     ![](https://github.com/Mmmmmmi/MyNote/blob/master/resource/binpath.png)
     ![](https://github.com/Mmmmmmi/MyNote/blob/master/resource/addpath.png)
  4. 以管理员运行 CMD（右键左下角 Window PowerShell 管理员也行)，进入 MySQL 的 bin 目录下，输入`mysqld install` 再输入` mysqld --initialize-insecure`
     ![](https://github.com/Mmmmmmi/MyNote/blob/master/resource/install.png)
     ![](https://github.com/Mmmmmmi/MyNote/blob/master/resource/init.png)
  5. 启动 MySQL 服务，在任意目录下均可。输入` net start mysql`
     ![](https://github.com/Mmmmmmi/MyNote/blob/master/resource/start.png)
  6. 初次安装，配置 root 密码，在打开的 CMD 界面，输入 ` mysql -u root`，首次登陆无需密码，直接回车。
     ![](https://github.com/Mmmmmmi/MyNote/blob/master/resource/rootinit.png)
  7. 输入` ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';`   `new password` 为新密码。输入完成后，退出（输入 quit)。
     ![](https://github.com/Mmmmmmi/MyNote/blob/master/resource/newpasswd.png)
  8. 重新登陆`mysql -u root -p`，这次输入新的密码登陆即可。

</details>

- <b><details><summary>**Centos 7**</summary></b>

  1. 添加 Yum Repository
     ` wget -i -c http://dev.mysql.com/get/mysql80-community-release-el7-2.noarch.rpm`
     ` mysql57-community-release-el7-10.noarch.rpm` 是版本号，具体版本可以到
     [MySQL 官网』: https://dev.mysql.com/downloads/repo/yum/
     修改。
  2. 下载完成后，安装` yum -y install mysql80-community-release-el7-2.noarch.rpm`
  3. 库安装完之后，开始安装`yum -y install mysql-community-server `
  4. 启动数据库服务，` systemctl start  mysqld.service`，查看状态 `systemctl status mysqld.service`
  5. 查看默认生成的`root`密码，` grep "password" /var/log/mysqld.log`
  6. 登陆数据库，` mysql -uroot -p`，输入刚才的密码。
  7. 修改密码，` ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';`，` new password` 为新的密码。
  8. 问题：可能由于依赖不足产生问题，可以尝试下面的命令移除对` mariadb-libs`：` yum -y remove mariadb-libs `

   </details>

### **数据库相关知识**

- <b><details><summary>**SQL 分类** </summary></b>

   **数据库定义语言 (Data Define Language，DDL)**
   - 定义数据库的模式、外模式、和内模式
   - 定义模式 / 内模式和外模式 / 模式二级映像
   - 定义有关的约束条件维护数据的存储结果
   ```
   create drop alter
   ```

   **数据库操纵语言 (Data Manipulation Language，DML)**
   - 实现对数据库的基本操作，包括检索、更新（包括插入、修改和删除) 等
   ```
   insert delete update
   ```

   **数据库控制语言 (Data Control Language，DCL)**
   - 负责管理数据库权限以及事务
   ```
   grant revoke commit
   ```

   </details>

- <b><details><summary>**基本操作** </summary></b>

   ```sql
   # 连接服务器
   mysql -h 127.0.0.1 -P 3306  -u root -p

   # 初次登陆修改密码
   ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';

   # 创建数据库
   create database helloworld;

   # 选择数据库
   use helloworld;

   # 查看引擎
   show engines;

   # 创建数据库表
   create table student(id int,name varchar(32),gender varchar(2));

   # 表中插入数据
   insert into student (id, name, gender) values (1, '张三', '男'); insert into student (id, name, gender) values (2, '李四', '女'); insert into student (id, name, gender) values (3, '王五', '男');

   # 批量插入数据
   insert into student (id, name, gender) values (3, '王五', '男'),(4, '王麻子', '男'));

   # 查询表中的数据
   select * from student;

   # 连接相关操作

   - INNER JOIN（内连接,或等值连接）: 获取两个表中字段匹配关系的记录。

   - LEFT JOIN（左连接）: 获取左表所有记录，即使右表没有对应匹配的记录。左连接

   - RIGHT JOIN（右连接）: 与 LEFT JOIN 相反，用于获取右表所有记录，即使左表没有对应匹配的记录。

   # MySQL 分组

   - GROUP BY ：可以根据一个或者多个列对结果集分组，在分组的列上可以使用 COUNT ， SUM ， AVG 等函数
   SELECT *
   FROM Student
   GROUP BY Student_Name;

   # MySQL 排序

   - ORDER BY ：如果需要对读取的数据进行排序，可以使用 MySQL 的 ORDER BY 子句来设定按哪个字段哪种方式来进行排序，再返回搜索结果。
   - 可以使用任何字段来作为排序的条件，从而返回排序后的查询结果。
   - 可以设定多个字段来排序。
   - 可以使用 ASC 或 DESC 关键字来设置查询结果是按升序或降序排列。 默认情况下，它是按升序排列。
   - 可以添加 WHERE...LIKE 子句来设置条件。
   SELECT TMP1, TMP2
   FROM TableName;
   ORDER BY TMP! [ASC [DESC][默认 ASC]]]


   ```
   </details>

- <b><details><summary>**MySQL 相关函数** </summary></b>
   ```sql

   # not in

   # <>

   # distinct

   # IF(expr, v1, v2)   如果表达式 expr 成立, 返回结果 v1, 否则返回 v2
   SELECT IF(1 > 0, "True", "False"); -------> True
   SELECT IF(0 > 1, "True", "False"); -------> False

   # IFNULL(v1, v2)   如果 v1 的值不为 NULL，则返回 v1 ，否则返回 v2
   select IFNULL(NULL, "Hello World");  ----->  Hello World

   ```
   </details>


- <b><details><summary>**事务** </summary></b>

  - **原子性（Atomicity）**，原子性是指事务包含的所有操作要么全部成功，要么全部失败回滚，因此事务的操作如果成功就必须要完全应用到数据库，如果操作失败则不能对数据库有任何影响。
  - **一致性（Consistency）**，一致性是指事务必须使数据库从一个一致性状态变换到另一个一致性状态，也就是说一个事务执行之前和执行之后都必须处于一致性状态。拿转账来说，假设用户 A 和用户 B 两者的钱加起来一共是 5000，那么不管 A 和 B 之间如何转账，转几次账，事务结束后两个用户的钱相加起来应该还得是 5000，这就是事务的一致性。
  - **隔离性（Isolation）**，隔离性是当多个用户并发访问数据库时，比如操作同一张表时，数据库为每一个用户开启的事务，不能被其他事务的操作所干扰，多个并发事务之间要相互隔离。即要达到这么一种效果：对于任意两个并发的事务 T1 和 T2，在事务 T1 看来，T2 要么在 T1 开始之前就已经结束，要么在 T1 结束之后才开始，这样每个事务都感觉不到有其他事务在并发地执行。
  - **持久性（Durability）**，持久性是指一个事务一旦被提交了，那么对数据库中的数据的改变就是永久性的，即便是在数据库系统遇到故障的情况下也不会丢失提交事务的操作。例如我们在使用 JDBC 操作数据库时，在提交事务方法后，提示用户事务操作完成，当我们程序执行完成直到看到提示后，就可以认定事务已经正确提交，即使这时候数据库出现了问题，也必须要将我们的事务完全执行完成，否则就会造成我们看到提示事务处理完毕，但是数据库因为故障而没有执行事务的重大错误。

</details>

- <b><details><summary>**C 语言调用接口** </summary></b>

   ```c++
   sudo yum install mysql-devel -y 	//安装开发包
   ```
</details>



## [**Redis**](https://redis.io/)

### **安装**
- <b><details><summary>**Windows** </summary></b>

   - 在GitHub上下载[压缩包](https://github.com/microsoftarchive/redis/releases)，这里使用的是压缩包的安装方法。
   - 解压到指定文件夹，将该文件夹添加到环境变量。
   - 打开powershell 输入`redis-server.exe`，显示运行即成功。如果没有添加环境变量，则需要从终端中进入文件夹，然后启动。
   
 </details>

- <b><details><summary>**Linux** </summary></b>

  - **Centos 7**

      其他版本的Centos系统，参考[官网](https://redis.io/download)。
   ```c
   sudo yum install redis
   systemctl start redis  //centos7
   service redis start    //centos6及以下
   systemctl status redis //查看启动状态
   ```

</details>

### **配置**
- <b><details><summary>**开启远程连接** </summary></b>

   - **windows**

      进入安装目录下，找到`redis.windows.conf`，打开，找到`bind 127.0.0.1` 修改为 `# bind 127.0.0.1` ，保存退出
   - **Linux**
   ```c
   sudo vim /etc/redis.conf
   //找到 bind 127.0.0.1 修改为下面
   # bind 127.0.0.1   //保存退出
   //重启服务
   service redis restart
   //在终端输入
   ps -ef | grep redis
   //输出
   redis 16274 1 0 09:56 ?  00:00:01 /usr/bin/redis-server *:6379
   // * 表示允许所有的ip连接到本服务上
   ```

</details>