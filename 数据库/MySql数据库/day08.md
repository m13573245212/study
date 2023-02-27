#day08
##Properties属性配置对象
<https://www.cnblogs.com/bakari/p/3562244.html>  
- properties:程序员可以把工程中的某些数据以配置文件的形式进行保存,此对象就是处理*.properties文件的对象,数据是以键值对的形式进行保存的.
- 如何使用:
 1. 创建my.properties配置文件
 2. 在DBUtils中把参数改为从jdbc.properties获取
###DBUtils第二次封装
	把连接数据库的信息存放到配置文件中,这样换数据库的话只需要改配置文件即可,把读取到的数据放入静态块中,保证读取数据的代码只执行一次.
##数据库连接池 DBCP
	DataBase Connection pool
防止每一次与数据库的交互都必须伴随着建立连接与断开连接,防止过度损耗资源,连接池设置最大连接数量,实现连接的复用,降低开关连接的次数.
##PreparedStatement
- PreparedStatement是Statement的子接口
- 作用:
1. 我们不需要继续拼接那个该死的字符串,代码可读性高
2. 带有预编译的效果,可以避免sql注入,因为预编译时sql语句逻辑固化,不会被修改

- 创建用户表

	create table user(id int,username varchar(10),password varchar(10));
	insert into user values(1,'libai','1234'),(2,'liubei','admin');






























