#day03
##没有条件的查询
select * from 表名  
select 字段1,字段2... from 表名
##条件查询
###列值为null 和 不为null
1. 查询没有上级领导的员工编号,姓名,工资

		select empno,ename,sal from emp where mgr is Null;
2. 查询没有上级领导的员工编号,姓名,领导编号

		select empno,ename,mgr from emp where mgr is not null;
###别名
	select empno as '员工编号',ename as '姓名' from emp;
	select empno '员工编号',ename '姓名' from emp;
	select empno 员工编号,ename 姓名 from emp;
###去重 distinct
- 查询emp表中所有员工的职位

		select distinct job from emp;
###比较远算符 >,<,=,>=,<=,!=(<>)
###and和or
- and等效java的 &&
- or 等效java的 ||
###in
- 如果查询字段的值为多个的时候使用in关键字

		select * from emp where age in(25,28,30,22); 
##between x and y
- 在某两个数值之间,包含and两边的数值

		select * from emp where sal between 500 and 1000;
##like
- _:代表单个未知字符
- %:代表多个未知字符
- 举例:
	- 以a开头, a%
	- 以a结尾, %a
	- 第二个字符是a, _a%
	- 包含a, %a%
	- 倒数第三个字符是a, %a
	- 第二个和最后一个是a, _a%a  
	
			select * from emp where ename like 'k%';  
			select * from emp where title like '%记事本%';
			select * from emp where title like '%记事本%' and price<100;
			select * from t_item where title like '%得力%' and price between 50 and 200;
			select * from t_item where title not like '%得力%';
			select * from t_item where price not between 50 and 200;
##查询结果排序 order by
- 格式:order by 字段名
- 默认升序,指定升序是:asc 降序:desc
1. 查询员工的名称和工资 按照工资降序排序

		select ename,sal from emp order by sal desc;
2. 查询单价在100以下的商品名称和价格,按照价格降序排序
	
		select * from t_item where price<100 order by price desc;
- 多字段排序,当第一个字段有相同值时,第二个字段排序开始

		select * from emp order by deptno desc,sal;
###limit 分页查询
- limit 跳过条数,查询条数
1. 查询商品表中第2页数据每页5条

		select * from t_item limit 5,5;
		select * from t_item order by price limit 6,3;先排序完成再显示
		select * from t_item oder by sal desc limit 0,1;
###数值计算 +,-,*,/,%(mod())
	select price,num,price*num from t_item;
- %和mod都是取余的作用

		7%2 等效 mod(7,2)
###日期相关函数
1. 获取当前日期+时间 now()
		
		select now();
2. 获取当前日期

		select curdate();
3. 获取当前时间

		select curtime();
4. 从日期和时间中提取日期

		select date(now());
5. 从日期和时间中提取时间

		select time(now());
		select date(created_time) from t_item;
		(created_time是表中的一个字段)
6. 提取年、月、日、时、分、秒

		- select extract(year from now());
		- select extract(month from now());
		- select extract(day from now());
		- select extract(hour from now());
		- select extract(minute from now());
		- select extract(second from now());
		(now()可以使用字段代替)
		- select extract(year from hiredate) from emp;
7. 日期格式化 date_format()
	- 格式:date_format(时间,格式)
	- %Y 4位年 2018
	- %y 2位年 18
	- %m 2位月 05
	- %c 1位月 5
	- %d 日
	- %H 24小时
	- %h 12小时
	- %i 分
	- %s 秒
	- 案例:

		select date_format(now(),'%Y年%m月%d日 %H时%i分%s秒');
		select date_format(created_time,'%Y年%m月%d日 %H时%i分%s秒')from t_item;
8. 把不规则的日期格式转成标准格式
	- 格式:str_to_date(日期字符串,格式)
	
			select str_to_date('25号12月2015年','%d号%m月%Y年');
###ifnull()
- 格式:age=ifnull(x,y) 判断x是否为null,如果是则age=y,如果不是则age=x;
- 案例:把员工表 没有奖金的 奖金修改为0;

		update emp set comm=ifnull(comm,0);
##聚合函数
- 对多行数据进行合并统计
- 求和 sum(字段名) 比如:求工资总和 sum(sal);
- 平均值 avg(字段名)
- 最大值 max(字段名)
- 最小值 min(字段名)
- 统计数量 count(*) 

		select count(*) from person where age<50;(*可以用字段代替,但结果不变因为统计的是数量)
##字符串相关函数
1. concat(a,b);字符串连接函数

		select concat('a','b');
		select ename,concat(sal,'元') from emp;
2. char_length(str) 获取字符串的长度

		select ename,char_length(ename) from emp;
3. instr(str,subStr)获取subStr在str中的位置

		select instr('nba','a');
4. locate(subStr,str)获取subStr在str中的位置

		select locate('a','nba');
5. insert(str,start,length,newstr)插入字符串
		
		select insert('abcdefg',3,2,'m');从第三个字符开始数两个,换成‘m’
6. lower(str) 转小写

		select lower('NBa');
7. upper(str) 转大写

		select upper('nBa');
8. trim(str) 去两端空白

		select trim('  abc   ');
9. left(str,length) 从左边截取多少个字符

		select left('abcdefg',3);
10. right(str,length) 从右边截取多少个字符

		select right('abcdefg',3);
11. substring(str,index,length)截取字符串

		select substring('abcdefghijk',2,3);
12. replace(str,old,new)替换字符串

		select replace('我爱苍老师','我','你们');
13. repeat(str,count)重复

		select repeat('加油',2);
14. reverse(str) 反转

		select reverse('我爱苍老师');
##数学相关函数
1. 向下取整 floor(num)

		select floor(3.23);
2. 四舍五入 round(num);

		select round(2.7);
		round(3.1415926,2);指定位数的四舍五入,位数可以定义为负数
3. truncate(num,m);非四舍五入

		select truncate(3.1715926,1);
4. 随机数rand() 0-1

		select floor(rand()*6);

