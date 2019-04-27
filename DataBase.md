# **数据库**
   **学习环境为 MySQL**
## **安装**

- windows

  1. 压缩包下载地址：<https://downloads.mysql.com/archives/community/>
     ![down](https://github.com/Mmmmmmi/MyNote/blob/master/resource/MySQLDown.png)
  2. 下载完成后，将压缩包解压到解压到想安装的路径
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
- Centos 7
  1. 添加 Yum Repository
     ` wget -i -c http://dev.mysql.com/get/mysql80-community-release-el7-2.noarch.rpm`
     ` mysql57-community-release-el7-10.noarch.rpm` 是版本号，具体版本可以到
     [MySQL 官网』: https://dev.mysql.com/downloads/repo/yum/
     修改。
  2. 下载完成后，安装` yum -y install mysql80-community-release-el7-2.noarch.rpm`
  3. 库安装完之后，开始安装`yum -y install mysql-community-server `
  4. 启动数据库服务，` systemctl start  mysqld.service`，查看状态 `systemctl status mysqld.service`
  5. 查看默认生成的`root`密码，` grep "password" /var/log/mysqld.log`
  6. 登陆数据库，` mysql -uroot -p`，输入刚才的密码
  7. 修改密码，` ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';`，` new password` 为新的密码
  8. 问题：可能由于依赖不足产生问题，可以尝试下面的命令移除对` mariadb-libs`：` yum -y remove mariadb-libs `

## **数据库操作**

### **基本操作**

**连接服务器**

```sql
mysql -h 127.0.0.1 -P 3306  -u root -p
```

**初次登陆修改密码**
```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';
```
**创建数据库**
```
create database helloworld; 
```
**使用数据库**
```
use helloworld; 
```
**创建数据库表**
```
create table student(     id int,     name varchar(32),     gender varchar(2) ); 
```
**表中插入数据**
```
insert into student (id, name, gender) values (1, '张三', '男'); insert into student (id, name, gender) values (2, '李四', '女'); insert into student (id, name, gender) values (3, '王五', '男'); 
```
**批量插入数据**
```
insert into student (id, name, gender) values (3, '王五', '男'),(4, '王麻子', '男')); 
```
**查询表中的数据**
```
select * from student; 
```
**MySQL 存储引擎**

**查看引擎**
```
show engines; 
```
### **SQL 分类**

1. 数据库定义语言 (Data Define Language，DDL)

- 定义数据库的模式、外模式、和内模式
- 定义模式 / 内模式和外模式 / 模式二级映像
- 定义有关的约束条件维护数据的存储结果
```
create drop alter
```
2. 数据库操纵语言 (Data Manipulation Language，DML)

- 实现对数据库的基本操作，包括检索、更新（包括插入、修改和删除) 等
```
insert delete update 
```
3. 数据库控制语言 (Data Control Language，DCL)

- 负责管理数据库权限以及事务
```
grant revoke commit
```
## **数据库事务的四大特性**

- **原子性（Atomicity）**，原子性是指事务包含的所有操作要么全部成功，要么全部失败回滚，因此事务的操作如果成功就必须要完全应用到数据库，如果操作失败则不能对数据库有任何影响。
- **一致性（Consistency）**，一致性是指事务必须使数据库从一个一致性状态变换到另一个一致性状态，也就是说一个事务执行之前和执行之后都必须处于一致性状态。拿转账来说，假设用户 A 和用户 B 两者的钱加起来一共是 5000，那么不管 A 和 B 之间如何转账，转几次账，事务结束后两个用户的钱相加起来应该还得是 5000，这就是事务的一致性。
- **隔离性（Isolation）**，隔离性是当多个用户并发访问数据库时，比如操作同一张表时，数据库为每一个用户开启的事务，不能被其他事务的操作所干扰，多个并发事务之间要相互隔离。即要达到这么一种效果：对于任意两个并发的事务 T1 和 T2，在事务 T1 看来，T2 要么在 T1 开始之前就已经结束，要么在 T1 结束之后才开始，这样每个事务都感觉不到有其他事务在并发地执行。
- **持久性（Durability）**，持久性是指一个事务一旦被提交了，那么对数据库中的数据的改变就是永久性的，即便是在数据库系统遇到故障的情况下也不会丢失提交事务的操作。例如我们在使用 JDBC 操作数据库时，在提交事务方法后，提示用户事务操作完成，当我们程序执行完成直到看到提示后，就可以认定事务已经正确提交，即使这时候数据库出现了问题，也必须要将我们的事务完全执行完成，否则就会造成我们看到提示事务处理完毕，但是数据库因为故障而没有执行事务的重大错误。