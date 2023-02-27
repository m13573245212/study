#day01
##IO流文件存储的弊端
1. 效率低(开发效率和执行效率低)
2. 数据的增删改查非常麻烦
3. 只能保存小量数据
4. 只能存储文本数据
##什么是DB?
DataBase:数据库,一个文件集合,本质是一个文件系统,数据按照特定格式存储到文件中,使用sql语言对数据进行增删改查.
##什么是DBMS?
DataBaseManagementSystem:数据库管理系统,管理数据库文件的软件,指一种操作和管理数据库的大型软件,用于建立,使用和维护数据库,对数据进行统一的管理和控制,用户通过DBMS访问数据库中的数据
##数据库的分类
1. 关系型数据库:经过数学理论验证,可以将现实生活中存在的各种关系,保存到数据库中,在此数据库中,以表的形式保存数据之间的关系.
2. 非关系型数据库:主要为了解决特定的应用场景,如:缓存,高并发访问等,存储数据的方式用多种,redis是常见的非关系型数据库,以键值对的形式保存.
##连接数据库
- 打开终端或命令行 在终端中输入以下命令:
mysql -uroot -p然后敲回车,然后再敲回车  
- 退出指令: exit;
##什么sql?
Stuctured Query Language: 结构化查询语言,使用sql语言和数据库服务器进行交互,通过sql告诉数据库服务器对数据进行什么操作.
##SQL规范
1. 以;(分号)结尾
2. 关键字之间有空格,通常只有一个,但多个也可以
3. 可以存在换行
4. 数据库名称和表名称区分大小写
##数据库相关的SQL
每一个工程对应一个数据库m,存储数据需要先创建一个数据库,然后在数据库中创建表  
1. 查看所有数据库
	show databases;  
2. 创建数据库
格式:creat database 数据库名称  
	create database db1;	    
指定字符集:create database 数据库名称  
character set gbk;   
create database db2 character set gbk;  
3. 查看指定数据库详情
格式:show create database 数据库名称  
show create database db1;  
4. 删除数据库
格式:drop database 数据库名称  
drop database db2;  
5. 使用数据库
格式:use 数据库名称  
use db1;	
##和表相关的sql
什么是表?  
1. 创建表  
格式:creat table表名(字段1名 字段1类型,字段2名 字段2类型,........)   
create table stu(id int,name varchar(10),age int,chinese int,math int,english int);  
创建表sql语句的执行过程:在终端中写完sql语句后敲回车终端会把sql通过网络传输到DBMS(mysql),DBMS对sql语句进行解析,然后对数据库中的数据进行操作.   
2. 查看所有表  
show tables;  
3. 查看指定表的详情 和 表的字段信息  
	show create table hero;  

	查看表的字段 格式:desc 表名;  
4. 创建表,指定引擎和字符集   
create table t1(id int,name varchar(5)) engine=myism charset=gbk;  
5. 删除表  
drop table t1;  
###表的引擎
1. innodb:支持数据库的高级操作,包括:事务,外键等.
2. myisam:仅支持数据的增删改查操作.
###表的修改
	use db1;
	create table person(id int,name varchar(10));
1. 修改表的名称  
格式:rename table 原名 to 新名;  
rename table person to t_person;
2. 修改表的引擎和字符集  
格式:alter table 表名 engine=myisam charset=gbk;  
alter table t_person engine=myisam charset=gbk;
3. 添加表的字段  
**在最后添加**-格式:alter table 表名 add 字段名 字段类型;  
alter table t_person add age int;  
**在前面添加**-格式:alter table 表名 add 字段名 字段类型 first;   
alter table t_person add chinese int first;  
**在某个字段后添加**-格式:alter table t_person add math int after id;
4. 删除字段  
格式:alter table 表名 drop 字段名;  
alter table t_person drop chinese;  
5. 修改字段的名称和类型  
格式:alter table 表名 change 原字段名 新字段名 字段类型  
alter table t_person change age myage int;  
6. 修改字段的类型和位置  
格式:alter table 表名 modify 字段名 字段类型 first(放在最前面)/after xxx;  
alter table t_person modify myage int after id;
##数据相关的SQL
1. 插入数据  
	**全表插入**:要求插入的数据的数量和顺序要和表的字段的数量顺序一致,格式:insert into 表名 values(值1,值2,值3,值4......);  
	insert into t_stu values(1,'zhangsan',23);  
	指定字段插入格式：insert into 表名(字段1,字段2...) values(值1,值2...);  
	insert into t_stu(id,name) values(2,'lisi');  
	**批量插入**:insert into t_stu values(3,'悟空',23),(4,'123',12);  
	insert into t_stu(id,name) values(1,'刘备'),(2,'关羽'),(3,'貂蝉');

2. 查询数据  
select * from t_stu;  
select name,age from t_stu;  
select ename from t_emp where dept='三国部';//可以加限制条件  
3. 删除数据
delete from t_stu where name='八戒';  
delete from t_stu where age is null;
4. 修改数据
update t_stu set name='张三' where id=1；
update t_stu set name='卷帘大将',age=200 where id=5;
##windows电脑出现命令行中无法插入中文数据的解决方案
在命令行中先登录mysql,然后执行set names gbk;通知MySQL数据库服务器,客户端(命令行)的编码格式为gbk;



