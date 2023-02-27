#day07
##JDBC
###什么是jdbc
	Java DataBase Connectivity:Java数据库连接,实际上jdbc是java中一套和数据库进行交互的api(application program interface)应用程序编辑接口.
###jdbc意义
为了便于连接各种数据库(mysql，oracle，db2等),为简化学习,sun提出jdbc接口,通过jdbc接口调用可操作任意数据库.
###如何使用jdbc
1. 创建maven工程
2. 下载相关jar包 mysql-connector-java 5.1.6
3. 代码   
  
        //1.注册驱动
        Class.forName("com.mysql.jdbc.Driver");
        //2.获取连接对象
        Connection conn=DriverManager.getConnection("jdbc:mysql://localhost:3306/db6","root","1234");
        System.out.println(conn);
        //3.创建sql执行对象
        Statement stat=conn.createStatement();
        //4.准备sql
        String sql="select count(*) from t_jdbc";
        //5.执行sql
        boolean b=stat.execute(sql);
        if(b){
            System.out.println("有结果集");
        }else{
            System.out.println("没有结果集");
        }
###execute
	此方法可以执行任意sql,返回值true代表有结果集(查询语句时会有结果集),false为没有结果集.通常情况下execute方法用于执行DDL(数据定义语言).
###executeUpdate
	此方法可以执行增删改操作,返回值代表生效的行数
###executeQuery
	此方法执行查询操作,返回值为ResultSet,里面保存查询到的所有结果
###ResultSet对象
	此对象里面装着查询到的结果数据,见到ResultSet就用while遍历
	next()方法会先判断是否有下一条数据,有返回true,没有返回false,如果返回true内部游标会向下移动.
###从ResultSet对象中获取数据
1. 通过字段的名称获取数据
2. 通过字段的位置获取数据 从1开始
###数据库字段类型和Java类型对比
	数据库                       Java
	int                         getInt
	varchar					    getString
	double					    getDouble
	datetime/timestamp		    getDate
###关闭资源
1. 关闭Connection,使用完之后的连接要及时关闭,避免浪费资源.
2. 关闭Statement,会占用内存空间,而且Statement对象有上限.
3. 关闭ResultSet,里面保存的是查询结果,用完就释放,节省空间.
- 关闭顺序:ResultSet->Statement->Connection
##maven
	maven项目管理工具,可以构建工程,使用坐标导入jar包

