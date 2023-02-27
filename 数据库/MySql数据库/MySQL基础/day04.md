#day04
##分组查询 group by
- 分组查询通常和聚合函数结合使用
- 查询条件中每个XXX就以XXX作为分组条件
- **格式:** 

    	select deptno,avg(sal) from emp group by deptno;
1. 查询每个部门的最高工资
 
	    select deptno,max(sal) from emp group by deptno;
2. 查询每个分类下商品的库存总量

		select category_id,sum(num) from t_item group by category_id;
3. 查询每个部门有多少人

		select deptno,count(*) from emp group by deptno;
4. 查询每个部门工资大于2000的有多少人

		select deptno,count(*) from emp where sal>2000 group by deptno;
5. 查询每个分类下低于100的商品数量

		select category_id,count(*) from t_item where price<100 group by category_id;
6. 案例：查询emp表中每个部门的编号，人数，工资总和，最后根据人数进行升序排列，如果人数一致，根据工资总和降序排列。

		select deptno,count(*),sum(sal) from emp group by deptno order by count(*),sum(sal) desc;
		等效于:select deptno,count(*) c,sum(sal) s from emp group by deptno order by c,s desc;
7. 案例：查询工资在1000~3000之间的员工信息，每个部门的编号，平均工资，最低工资，最高工资，根据平均工资进行升序排列。

		select deptno,avg(sal) a,min(sal),max(sal) from emp where sal between 1000 and 3000 group by deptno order by a;
8. 案例：查询含有上级领导的员工，每个职业的人数，工资的总和，平均工资，最低工资，最后根据人数进行降序排列，如果人数一致，根据平均工资进行升序排列

		select job,count(*) c,sum(sal),avg(sal) a,min(sal) from emp
		where mgr is not null
		group by job
		order by c desc,a;
9. 查询每个主管,每个主管的手下人数

		select deptno,mgr,count(*) from emp 
		where mgr is not null 
		group by deptno,mgr;
10. 每年入职人数

		select extract(year from hiredate) year,count(*) from emp 
		group by year;
##having 有条件的分组统计
- where 后面只能对普通字段进行筛选
- having 写在group by后面,通常是和group by结合使用
- 普通字段的条件写在where后面,聚合函数条件写在having后面,having写在group by后面
- 查询每个部门的平均工资要求平均工资大于2000

		select deptno,avg(sal) a from emp 
		group by deptno
		having a>2000;
1. 查询所有分类所对应的库存总量,要求库存总量高于1000

		select category_id,sum(num) a from t_item
		group by category_id
		having a>1000;
2. 查询所有分类所对应的平均单价,平均单价低于100

		select category_id,avg(price) a from t_item
		group by category_id
		having a<100;
3. 查询每个部门中名字里面包含a的员工的平均工资,只显示平均工资高于2000的

		select deptno,avg(sal) a from emp
		group by deptno
		having a>2000;
- sql中各个关键字的顺序:

		select...from 表名 where...group by...having...order by...limit...

##子查询
1. select ename from emp where sal=(select max(sal) from emp);
2. 查询工资高于平均工资的员工姓名和工资

		select ename,sal from emp where sal>(select avg(sal) from emp);
3. 查询最后入职的员工信息

		select * from emp where hiredate=(select max(hiredate) from emp);
4. 查询出有商品的分类信息(有商品 指在商品表中出现过的分类id)

		select * from t_item_category where id in(select distinct category_id from t_item);
5. 查询工资高于20号部门里面最高工资的所有员工信息

		select * from emp where sal>(select max(sal) from emp where deptno=20);
6. 查询和jones一样工作的员工信息

		select * from emp where job=(select job from emp where ename='jones')and ename!='jones';
7. 查询部门平均工资最高的部门信息

		select * from dept where deptno in(
		select deptno from emp group by deptno having avg(sal)=(
		select avg(sal) 平均工资 from emp group by deptno order by 平均工资 desc limit 0,1));
		- 获取最高平均工资
		- 获取平均工资等于上面工资的部门的编号(该结果可能有多个)
		- 获取上面部门的信息
####什么是子查询?嵌套在sql语句里面的查询sql语句
####子查询可以有多层嵌套
####子查询可以写在的位置有:
1. 写在where后面作为查询条件的值 
2. 写在from后面当成一张新表,**必须起别名**

		select * from (select * from XXX) t1;
3. 可以写在创建表的时候

		create table newemp as(select ename,sal from emp); 
##关联查询
- 同时查询多张表的数据
- 1. 查询每一员工姓名和所对应的部门名称  

		select emp.ename,dept.dname from emp,dept where emp.deptno=dept.deptno;
- 2. 查询每个商品的标题,商品单价,商品分类名称

		select i.title,i.price,c.name from t_item i,t_item_category c where i.category_id=c.id;
- 3. 查询在new york工作的所有员工信息

		select e.*
		from emp e,dept d
		where e.deptno=d.deptno and d.loc='new york';

		select e.*
		from emp e join dept d on e.deptno=d.deptno and d.loc='new york';
##笛卡尔积
- 关联查询如果不写关联关系,则查询结果为两张表的乘积,这个乘积就是笛卡尔积,通常是一种错误的查询结果
##等值连接和内连接
- 等值连接和内连接都是关联查询的查询方式,效果相同.
- 等值连接格式:select * from A,B where A.x=B.x and A.y=abc;
- 内连接格式:select * from A join B on A.x=B.x where A.y=abc;可读性更好
##外连接
- 关联查询时只查询有关系的数据有时不能满足需求,如果需要查询所有数据需要使用外连接的查询方式。
- 左外连接:以join左边的表为主表,右边表只查询有关系的数据
- 右外链接:以join右边的表为主表,左边表只查询有关系的数据
- 1. 查询所有员工的名字和对应的部门名(使用左外)

		    select e.ename,d.deptno 
		    from emp e left join dept d on e.deptno=d.deptno
  2. 查询所有部门和对应的员工名(使用右外) 

			select e.ename,d.deptno 
			from emp e right join dept d on e.deptno=d.deptno









