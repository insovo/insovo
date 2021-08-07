# MySQL

## 1、查找最晚入职员工的所有信息

```sql
create table employees(
	emp_no int(11) not null,
  	birth_date date not null,
    first_name varchar(14) not null,
    last_name varchar(16) not null,
    gender char(1) not null,
    hire_date date not null,
    primary key(emp_no));
由于date数据类型只能精确到天，即可能当天存在一个或多个数据，该题使用limit无法获取所有的最晚当天入职的员工信息
select * from employees where hire_date = ( select max( hire_date ) from employees );
```
## 2、查找入职员工时间排名倒数第三的员工所有信息
```sql
create table employees(
	emp_no int(11) not null,
	birth_date date not null,
	first_name varchar(14) not null,
	last_name varchar(16) not null,
	gender char(1) not null,
	hire_date date not null,
	primary key( emp_no )
);
与第一题一样，区别在于需要进行去重和排序后获得排名信息
select * from employees where hire_date = ( select distinct hire_date from employees order by hire_date desc limit 2,1 );
```
## 3、查找各个部门当前（to_date = "9999-01-01"）领导当前薪水详情以及对应部门编号dept_no
```sql
create table dept_manager(
	dept_no char(4) not null,
	emp_no int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key(emp_no,dept_no)
);
create table salaries(
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no,from_date)
);
简单的多表查询
select salaries.emp_no , salary , salaries.from_date , salaries.to_date , dept_no from salaries , dept_manager where salaries.emp_no = dept_manager.emp_no and salaries.to_date = "9999-01-01" and dept_manager.to_date = "9999-01-01";
```
## 4、查找所有已经分配部门员工的last_name 和 first_name
```sql
create table dept_emp(
	emp_no int(11) not null,
	dept_no char(4) not null,
	from_date date not nll,
	to_date date not null,
	primary key( emp_no , dept_no )
);
create table employees(
	emp_no int(11) not null,
	birth_date date not null,
	first_name varchar(14) not null,
	last_name varchar(16) not null,
	gender char(1) not null,
	primary key( emp_no )
);
inner join 用法
select last_mame , first_name , dept_no from employees inner join dept_emp de on employees.emp_no = de.emp_no;
```

## 5、查找所有员工的last_name和first_name以及对应的部门编号dept_no，也包括没有分配具体部门的员工
```sql
create table dept_emp(
	emp_no int(11) not null,
	dempt_no char(4) not null,
	from_date date not null,
	to_date date not null,
	primary key(emp_no,dept_no)
);
create table employees(
	emp_no int(11) not null,
	birth_date date not null,
	first_name 1varchar(14) not null,
	last_namr varchar(16) not null,
	gender char(1) not null,
	hire_date date not null,
	primary key(emp_no)
);
left join用法
select last_name,first_name,dept_no from employees e left join dept_emp d on e.emp_no=d.emp_no;
```
## 6、查找所有员工入职时候的薪水情况，给出emp_no以及salary，并按照emp_no进行排序
```sql
create table employees(
	emp_no int(11) not null,
	birth_date not null,
	first_name varchar(14) not null,
    last_name varchar(16) not null,
    gender char(1) not null,
    hire_date date not not null,
    primary key(emp_no)
);
create table salaries(
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key(emp_no,from_date)
);
两表的简单连接，然后对数据进行排序
select e.emp_no,s.salary from employees e left join salaries s on e.emp_no = s.emp_no where e.hire_date = s.hire_date order by e.emp_no desc;
```
## 7、查找薪水涨幅次数超过15次的员工号emp_no以及其对应的涨幅次数t
```sql
create table salaries()
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key(emp_no,from_date)
;
分组后过滤
select emp_no , count(emp_no) t from salaries group by emp_no having count(emp_no) > 15;
```
## 8、找出所有员工当前（to_date='9999-01-01'）具体的薪水salary情况，对于相同的薪水只显示一次，并按照逆序显示
```sql
create table salaries(
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no,from_date)
);
distinct的作用
select distinct salary from salaries where to_date = '9999-01-01' order by salary desc;
```
## 9、获取所有部门当前manager的当前薪水情况，给出dept_no以及salary，当前表示to_date='9999-01-01'
```sql
create table dept_manager(
		dept_no char(4) not null,
		emp_no int(11) not null,
		from_date date not null,
		to_date date not null,
		primary key (emp_no,dept_no)
);
create table salaries (
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no,from_date)
);
连接后过滤
select dm.dept_no , dm.emp_no , s.salary from dept_manager dm left join salaries s on dm.emp_no = s.emp_no where s.to_date = '9999-01-01' and dm.to_date = '9999-01-01'
```
## 10、获取所有非manager的员工emp_no
```SQL
create table dept_manager(
	dept_no char(4) not null,
	emp_no int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no,dept_no)
);
create table employees (
	emp_no int(11) not nll,
	birth_date date not null,
	first_name varchar(14) not null,
	last_namr varchar(16) not null,
	gender char(1) not null,
	hire_date date not null,
	primary key(emp_no)
);
将属于manger的所有员工过滤
select e.emp_no from employees e where e.emp_no not in (select emp_no from dept_manager);
```
## 11、获取所有员工当前的manager，如果当前的manager是自己的话结果不显示，当前表示to_date='999-01-01'。结果第一列给出当前员工的emp_no，第二列给出其manager对应的manager_no
```sql
create table dept_emp (
	emp_no int(11) not null,
	dept_no int(4) not null,
	from_date date not not null,
	to_date date not null
	primary key(emp_no,dept_no)
);
create table dept_manager(
	dept_no char(4) not null,
	emp_no int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no,dept_no)
);
select de.emp_no,dm.emp_no manager_no from dept_emp de left join dept_manager dm on de.dept_mo = dm.dept_no where de.to_date = '9999-01-01' and dm.to_date = '9999-01-01' and de.emp_no != dm.emp_no ;
```
## 12、获取所有部门中当前员工薪水最高的相关信息，给出dept_no,emp_no以及对应的salary
```sql
create table dept_emp (
	emp_no int(11) not null,
	dept_no char(4) not null,
	from_date date not null,
	to_date date not null,
	primary key(emp_no,dept_no)
);
create table salaries (
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key( emp_no , from_date )
);
select dept_no , s.emp_no , max(salary) salary from dept_emp de left join salaries s on de.emp_no = s.emp_no where de.to_date = '9999-01-01' group by dept_no having s.salary = max( salary );
```
## 13、从title表获取按照title进行分组，每组个数大于等于2给出title以及对应的数目t
```sql
create table if not exists ''titles''(
	emp_no int(11) not null,
	title varchar(50) not null,
	from_date date not null,
	to_date date not null
);
select title , count(emp_no) t from titles group by title having count(emp_no) >= 2;
```
## 14、从title表获取按照title进行分组，每组个数大于等于2，给出title以及对应的数目t；注意对重复的emp_no进行忽略
```sql
create table if not exists 'titles'(
	emp_no int(11) not null,
	title varchar(50) not null,
	from_date date not null,
	to_date date not null
);
select title , count(distinct emp_no) t from titles group by title having t >= 2;
```
## 15、查找employees表所有emp_mo为奇数，且last_name不为Mary的信息，并按照hire_date逆序排序
```sql
create table employees (
	emp_no int(11) not null,
	birth_date date not null,
	first_name varchar(14) not null,
	last_namr varchar(16) not null,
	gender char(1) not null,
	hire_date date not null,
	primary key (emp_no)
);
select * from employees where last_name != 'Mary' and emp_no %2 = 1 order by hire_date desc ;
```
## 16、统计当前各个title类型对应的员工当前（to_date = '9999-01-01'）薪水对应的平均工资，结果给出title以及平均工资avg
```sql
create table salaries(
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no,from_date)
);
create ttable if exists 'titles' (
	emp_no int(11) not null,
	title varchar(50) not null,
	from_date date not null,
	to_date date not null
);
select title , avg(salary) avg from titles t left join salaries s on t.emp_no = s.emp_no where t.to_date = '9999-01-01' and s.to_date = '9999-01-01' group by title;
```
## 17、获取当前（to_date='9999-01-01'）薪水第二多的员工的emp_no及其对应的薪水salary
```sql
create table salaries (
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key(emp_no,from_date)
);
select emp_no , salary from salaries where to_date = '9999-01-01' order by salary desc limit 1,1;
```
## 18、查找当前薪水(to_date='9999-01-01')排名第二多的员工编号emp_no、薪水salary、last_name以及first_name，不准使用order by
```sql
create table employees(
	emp_no int(11) not null,
	birth_date date not null,
	first_name varchat(14) not null,
	last_name varchar(16) not null,
	gender char(1) not null,
	hire_date date not null,
	primary key (emp_no)
);
create table salaries(
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null
	primary key (emp_no,from_date)
);
1、先获得去除了最大值得到数据t1
2、再获得t1中的最大值
select s.emp_no , salary , last_name , first_name from salaries s left join emplyees e on e.emp_no = s.emp_no where s.salary = (select max(salary) from salaries where to_date = '9999-01-01' and salary != (select max(salary) from salaries));
```
## 19、查找所有员工的last_name和first_name以及对应的dept_name，也包括暂时没有分配部门的员工
```sql
create table departments(
	dept_no char(4) not null,
	dept_name varchar(40) not null,
	primary key (dept_no)
);
create table dept_emp(
	emp_no int(11) not null,
	dept_no char(4) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no,dept_no)
);
create table employees(
	emp_no int(11) not null,
	birth_date date not null,
	first_namr varchar(14) not null,
	last_namr varchar(16) not null,
	gender char(1) not null,
	hire_date date not null,
	primary key (emp_no)
);
三表关联
select e.last_name , e.first_name , d.dept_name from employees e left join dept_emp de on e.emp_no = de.emp_no left join departments d on de.dept_no = d.dept_no;
```
## 20、查找员工编号emp_no为10001其自入职以来的薪水salary涨幅值growth
```sql
create table salaries(
	emp_no int(11) not null,
	salary int (11) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no,from_date)
);
简单的减法操作
select max(salary) - min(salary) growth from salaries where emp_no = 10001;
```
## 21、查找所有员工自入职以来的薪水涨幅情况，给出员工编号emp_no以及对应的薪水涨幅growth，并按照growth进行升序
```sql
create table employees(
	emp_no int(11) not null,
	birth_date date not null,
	first_name varchar(14) not null,
	last_name varchar(16) not null,
	gender char(1) not null,
	hire_date date not null,
	primary key (emp_no)
);
create table salaries(
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no,from_date)
);
1、获取当前的工资表放入临时表中
--目前的工资
(select emp_no,salary from salaries where to_date = '9999-01-01') end
2、获得第一次入职时候的工资，放入临时表start中
--最开始的工资
(select s.emp_no,salary from salaries s left join employees e on s.emp_no where s.from_date = e.hire_date) start
3、连接临时表，使用end工资-start临时工资即可
start end.emp_no , (emd.salary - start.salary) growth from (select emp_no , salary from salaries where to_date = '9999-01-01') end left join (select s.emp_no , salary from salaries s inner join employees e on s.emp_no = e.emp_no where s.from_date = e.hire_date) start on start.emp_no = end.emp_no order by growth;
```
## 22、统计各个部门对应员工涨幅的次数总和，给出部门编码dept_no、部门名称dept_name以及次数sum
```sql
create table departments(
	dept_no char(4) not null,
	dept_name varchar(40) not nill,
	primary key (dept_no)
);
create table dept_emp(
	emp_no int(11) not null,
	dept_no char(4) not null,
	from_date date not null,
	to_date date not null
	primary key (emp_no,dept_no)
);
create table salaries (
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no,from_date)
);

1、计算每个员工的涨幅次数总和s
--涨幅次数总和
(select emp_no , count(salary) sum from salaries s group by emp_no) s

2、拼接员工和部门信息info
(select emp_no , d.dept_no , dept_name from dept_emp de left join departments d on de.dept = d.dept_no) info

3、拼接info和s表，对dept_no进行分组，计算每个部门对应的sum总和
select dept_no , dept_name , sum(sum) sum from (select emp_no , d.dept_no , dept_name from dept_emp de left join departments d on de.dept_no = d.dept_no) info inner join (select emp_no , count(salary) sum from salaries s group by emp_no) s on info.emp_no = s.emp_no = s.emp_no group by dept_no;
```
## 23、对所有员工的当前(to_date = '999-01-01')薪水按照1-N进行排名，相同salary并列且按照emp_no升序排序
```sql
create table salaries(
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no , from_date)
);
本题无法通过MySQL进行查询，但是测试通过了sqlite，也就是牛客网提交通过。
1、获得两个临时表t1,t2
(select * from salaries s where to_date = '9999-01-01')t
1
(select * from salaries s where to_date = '9999-01-01')t2
2、用第一个临时表的t1.salary去和t2.salary进行比较，从而可以获得所有t1.salary小于t2.salary的数据集，正是如此，我们可以对用户进行分组，统计每个用户对应的salary小于其他用户的数据条数，以此排名，使用count时去重，因为可能存在相同排名的人。
select t1.emp_no , count(distinct t2.salary) from t1 , t2 where t2.salary >= t1.salary group by t1.emp_no;
--------------------------------------------------------
select t1.emp_no , t1.salary ， count(distinct t2.salary) rank from (select * from salaries s where to_date = '9999-01-01')t1 ,(select * from salaries where to_date = '9999-01-01')t2 where t2.salary >= t1.salary group by t1.emp_no order by rank;
```
## 24、获取所有非manager员工当前的薪水情况，给出dept_no，emp_no以及salary，当前表示to_date = '9999-01-01'
```sql
create table dept_emp(
	emp_no int(11) not null,
	dept_no char(4) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no , dept_no)
);
create table dept_manager(
	dept_no char(4) not null,
	emp_no int(11) not null,
	from_date date not null,
	to_date not null,
	primary key (dept_no,emp_no)
);
create table employees (
	emp_no int(11) not null,
	birth_date date not null,
	first_name varchar(14) not null,
	last_name varchar(16) not null,
	gender char(1) not null,
	hire_date date not null,
	primary key (emp_no)
);
create table salaries(
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key(emp_no , from_date)
);
1、获取所有manager的no
select emp_no from dept_manager
2、获取所有非manager的信息表emp
select * from employees where emp_no not in (select emp_no from dept_manager)
3、拼接emp,salaries,dept_emp表，获取答案结果集
select dept_no , s.emp_no,salary from (select * from employees where emp_no not in (select emp_no from dept_manager))emp,salaries s.dept_emp de where emp.emp_no and s.to_date = '9999-01-01' and emp.emp_no = de.emp_no;
```
## 25、获取员工当前的薪水比其manager当前薪水还高的相关信息，当前表示to_date = '9999=01-01'
- 结果第一列给出员工的emp_no
- 第二列给出其manager的manager_no
- 第三列给出该员工当前的薪水emp_salary
- 第四列给出该员工对应的manager当前的薪水manager_salary
```sql
create table dept_emp(
	emp_no int(11) not null,
	dept_no char(3) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no , dept_no)
);
create table dept_manager(
	dept_no char(4) not null,
	emp_no int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no , dept_no)
);
create table salaries (
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no , from_date)
);
1、获取所有manager的工资信息t2
select dm.emp_no , manager_no , salary manager_salary ,dept_no from dept_manager dm ,salaries s where dm.emp_no = s.emp_no and s.to_date = '9999-01-01'
2、获取所有普通员工的工资信息t1
select de.emp_no , salary from dept_emp de,salaries s where de.emp_no not in (select dept_manager.emp_no from dept_manager) and de.emp_no = s.emp_no and s.to_date = '9999-01-01'
3、拼接t1,t2,dept_emp,通过条件过滤
select t1.emp_no,t2.manager_no,t1.salary emp_salary,t2.manager_salary from (select de.emp_no,salary from dept_emp de,salaries s where de.emp_np not in (select dept_manager.emp_no from dept_manager) and de.emp_no = s.emp_no and s.to_date = '9999-01-01')t1,(select dm.emp_no manager_no,salary manager_salary,dept_no from dept_manager dm,salaries s where dm.emp_no = s.emp_no and s.to_date = '9999-01-01')t2,dept_emp de where t1.emp_no = de .emp_no and de.dept_no = t2.dept_no and t1.salary > manager_salary;
```
## 26、汇总各个部门当前员工的title类型的分配数目，结果给出部门编号dept_no、dept_namr、其当前员工所有的title以及该类型title对应的数目count
```sql
create table department(
	dept_no chR(4) not null,
	dept_name varchar(40) not null,
	primary key (dept_no)
);
create table dept_emp(
	emp_no int(11) not null,
	dept_no char(4) not null,
	from_date date not null,
	to_date date not null,
	primary key (emp_no,dept_no)
);
create table if not exists(
	emp_no int(11) not null,
	title varchar(50) not null,
	from_date date not null,
	to_date date not null
);
1、当前员工对应的title
select de.emp_no , dept_no,title from dept_emp de,titles t where de.emp_no = t.emp_no and t.to_date = '9999-01-01'and de.to_date = '9999-01-01'；
2、当前员工对应的dept信息
select emp_no , d.dept_no,title,dept_name from(select de.emp_no,dept_no,title from dept_emp de,titles t where de.rmp_no = t.emp_no and t.to_date = '9999-01-01' and de.to_date = '9999-01-01')t1,departments d where t1.dept_no = d.dept_no;
3、罗列title对应的count，对dept_no和title进行分组
select d.dept_no , d.dept_name ,title,count(emp_no) count from (select emp_no,d.dept_no,title,dept_name from (select de.emp_no,dept_no,title from dept_emp de,titles t where de.emp_no = t.emp_no and t.to_date = '9999-01-01' and de.to_date = '9999-01-01')t1 , departments d where t1.dept_no = d.dept_no)t1,departments d where t1.dept_no = d.dept_no = d.dept_no group by t1.dept_no , title;
```
## 27、给出每个员工每年薪水涨幅超过5000的员工编号emp_no、薪水变更开始日期from_date以及薪水涨幅值salary_growth，并按照salary_growth逆序排列
- 提示：在sqlite中获取datetime时间对应的年份函数为strftime('%Y',to_date)
```sql
create table salaries(
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key(emp_no,from_date)
);
select s2.emp_no , s2.from_date , (s2.salary - s1.salary) as salary_growth
from salaries as s1 , salaries as s2
where s1.emp_no = s2.emp_no
and salary_growth > 5000
and (strftime('%Y',s2.to_date) - strftime( '%Y,s1.to_date' ) = 1 or strftime( '%Y',s2.from_date ) - strftime( '%Y',s1.from_date ) = 1 )
order by salary_growth desc;
```
## 28、查找描述信息中包括robot的电影对应的分类名称以及电影数目，而且还需要该分类对应的电影数目>=5部
### film表
|字段|说明|
|:----|:----|
|film_id|电影id|
|title|电影名称|
|description|电影描述信息|

```sql
create table if not exists film(
	film_id smallint(5) not null default '0',
	title varchar(255) not null,
	description text,
	primary key(film_id)
);
```
### category表
|字段|说明|
|:---|:---|
|category_id|电影分类id|
|name|电影分类名称|
|last_name|电影分类最后更新时间|
```sql
create table category(
	category_id tinyint(3) not null,
	name varchar(25) not null, last_update timestamp,
	primary key (category_id)
);
```
### film_category表
|字段|说明|
|:-|:-|
|film_id|电影id|
|category_id|电影分类id|
|last_update|电影id和分类id对应关系的最后更新时间|
```sql
create table film_category(
	film_id smallint(5) not null,
	category_id tinyint(3) not null, last_update timestamp
);
```
-- 查找描述信息中包括robot的电影对应的分类名称以及电影数目，而且还需要该分类对应电影数量>=5部
---
```sql
1、获取分类电影数量大于等于5的所有分类ac
select fc.category_id , count(f.film_id)ac 
from film f left join film_category fc 
on f.film_id = dc.film_id 
group by category_id 
having count(f.film_id) >= 5;
2、连接ac,film,film_category,category四表
3、过滤
select name , count(fc.film_id) from 
(select fc.category_id , count(f.film_id)ac from film f left join film_category fc on f.film_id = fc.film_id group by category_id having count(f.film_id) >= 5)ac,
film f, film_category fc, category c
where f.film_id = fc.film_id and fc.category_id = c.category_id and c.category_id = ac.category_id and description like '%robot%';
```
---
## 29、使用join查询方式找出没有分类的电影id及名称
### film表
|字段|说明|
|:----|:----|
|film_id|电影id|
|title|电影名称|
|description|电影描述信息|

```sql
create table if not exists film(
	film_id smallint(5) not null default '0',
	title varchar(255) not null,
	description text,
	primary key(film_id)
);
```
### category表
|字段|说明|
|:---|:---|
|category_id|电影分类id|
|name|电影分类名称|
|last_name|电影分类最后更新时间|
```sql
create table category(
	category_id tinyint(3) not null,
	name varchar(25) not null, last_update timestamp,
	primary key (category_id)
);
```
### film_category表
|字段|说明|
|:-|:-|
|film_id|电影id|
|category_id|电影分类id|
|last_update|电影id和分类id对应关系的最后更新时间|
```sql
create table film_category(
	film_id smallint(5) not null,
	category_id tinyint(3) not null, last_update timestamp
);
```
-- 使用join查询方式找出没有分类的电影id以及名称
```sql
1、左连接
筛选category_id为空的项
select f.film_id,title from film f left join film_category fc on f.film_id = fc.film_id where fc.category_id is null;
```
---
## 30、使用子查询的方式找出属于action分类的所有电影对应的title，description
### film表
|字段|说明|
|:----|:----|
|film_id|电影id|
|title|电影名称|
|description|电影描述信息|

```sql
create table if not exists film(
	film_id smallint(5) not null default '0',
	title varchar(255) not null,
	description text,
	primary key(film_id)
);
```
### category表
|字段|说明|
|:---|:---|
|category_id|电影分类id|
|name|电影分类名称|
|last_name|电影分类最后更新时间|
```sql
create table category(
	category_id tinyint(3) not null,
	name varchar(25) not null, last_update timestamp,
	primary key (category_id)
);
```
### film_category表
|字段|说明|
|:-|:-|
|film_id|电影id|
|category_id|电影分类id|
|last_update|电影id和分类id对应关系的最后更新时间|
```sql
create table film_category(
	film_id smallint(5) not null,
	category_id tinyint(3) not null, last_update timestamp
);
```
### 答案

-- 使用子查询的方式找出属于action分类的所有电影对应的title，description

```sql
exists子查询操作
select title,description from film f where exists (select * from film_category fc , category c where fc.category_id = c.category_id and c.name = 'Action' and f.film_id = fc.film_id );
```

## 31、获取select * from employees对应的执行计划

```sql
explain select * from employees
explain关键字用于对SQL执行的结果进行分析
```



## 32、将employees表的所有员工的last_name和first_name拼接起来作为Name

```sql
create table employees(
	birth_date date not null,
    first_name varchar(14) not null,
    last_name varchar(16) not null,
    gender char(1) not null,
    hire_date date not null,
    primary key(emp_no)
);
```

### 答案

```sqlite
1、sqlite
select last_name || "" || first_name Name from employees
```

```mysql
2、mysql
select concat(last_name, "", first_name) Name from employees
```

## 33、创建一个actor表，包含如下列信息

| 列表     | 类型        | 是否为NULL | 含义   |
|:- |:-|:- |:- |
| actor_id | smallint(5) | not null   | 主键id |
| first_name | varchar(45) | not null | 名字 |
| last_name | varchar(45) | not null | 姓氏 |
| last_update | timestamp | not null | 最后跟新时间-默认是系统时间 |

### 答案

```mysql
create table actor(
	actor_id smallint(5) not null primary key,
    first_name varchar(45) not null,
    last_name varchar(45) not null,
    last_update tiemstamp not null default (datetime('now', 'localtime'))
)
```

```mysql
create table actor(
	actor_id smallint(5) not null primary key comment '主键id',
    first_name varchar(45) not null comment '名字',
    last_name varchar(45) not null comment '姓氏',
    last_update timestamp not null comment '最后更新时间，默认是系统当前时间'
)
```

## 34、批量插入数据

对表actor批量插入如下数据：

```mysql
create table if not exists actor(
	actor_id smallint(5) not null primary key,
    first_name varchar(45) not null,
    last_name varchar(45) not null,
    last_update timestamp not null default (datetime('now', 'localtime'))
)
```

| actor_id | first_name | last_name | last_update         |
| -------- | ---------- | --------- | ------------------- |
| 1        | PENELOPE   | GUINESS   | 2006-02-15 12:34:33 |
| 2        | NICK       | WAHLBERG  | 2006-0215 12:34:33  |

### 答案

```MYSQ
insert into actor values(1, 'PENELOPE', 'GUINESS', '2006-02-15 12:34:33'), (2,'NICK', 'WAHLBERG', '2006-02-15 12:34:33');
```

## 35、对表actor批量插入如下数据，如果数据已经存在，请忽略，不使用replace操作

```mysql
create table if not exists actor(
	actor_id smallint(5) not null primary key,
    first_name varchar(45) not null,
    last_name carchar(45) not null,
    last_update timestamp not null default (datetime('now', 'localtime'))
)
```

| actor_id | first_name | last_name | last_update |
| :------- | :--------- | :-------- | :---------- |
|  '3'|'ED'   |'CHASE'  |'2006-02-15 12:34:33'             |

### 答案

```sqlite
insert or ignore into actor values (3, 'ED', 'CHASE', '2006-02-15 12:34:33');
```

```MYSQL
insert ignore into actor values (3, 'ED', 'CHASE', '2006-02-15 12:34:33')
```

## 创建一个actor_name表

对于如下actor表，其对应数据为：

| actor_id | first_name | last_name | last_update         |
| -------- | ---------- | --------- | ------------------- |
| 1        | PENELOPE   | GUINESS   | 2006-02-15 12:34:33 |
| 2        | NICK       | WAHLBERG  | 2006-02-15 12:34:33 |

创建一个actor_name表，将actor表中所有first_name以及last_name导入该表。actor_name表结构如下：

| 列表       | 类型        | 是否为NULL | 含义 |
| ---------- | ----------- | ---------- | ---- |
| first_name | varchar(45) | nou null   | 名字 |
| last_name  | varvhar(45) | not null   | 姓氏 |

### 答案：

```MYSQL
create table actor_name(
	first_name varchar(45) not null,
    last_name varchar(45) not null
);
insert into actor_name select first_name, last_name from actor;
```

## 37、对first_name创建唯一索引uniq_idx_firstname

针对如下表actor结构创建索引：

```mysql
create table if not exists actor(
	actor_id smallint(5) not null primary key,
    first_name varchar(45) not null,
    last_name varchar(45) not null,
    last_update timestamp not null default (datetime('now', 'localtime'))
);
```

对first_name创建唯一索引uniq_idx_firstname，对last_name创建普通索引idx_lastname

```mysql
create unique index uniq_idx_first_name on actor(first_name);
create index idx_last_name on actor(last_name);
对于SQLlite和MySQL答案通用，但是对于MySQL仍然可以使用alter来进行索引的检索。
alter table actor add unique index uniq_idx_firstname(first_name), add index idx_lastname(last_name)
```

## 38、针对actor表创建视图actor_name_view,只包含first_name和last_name两列，并对这两列重新命名，first_name为first_name_v, last_name修改为last_name_v:

```mysq;
create table if not exists actor(
	actor_id smallint(5) not null primary key,
	first_name varchar(45) not null,
	last_name varchar(45) not null,
	last_update timestamp not null default(datetime('now', 'localtime'))
);
```

### 答案

```mysql
create view actor_name_view as
	select first_name first_name_v, last_name last_name_v from actor;
```

## 39、针对上边的salaries表emp_no字段创建idx_emp_no， 查询emp_no为10005， 使用强制索引

```mysq
create table salaries(
	emp_no int(11) not null,
	salary int(11) not null,
	from_date date not null,
	to_date date not null,
	primary key(emp_no, from_date)
);
create index idx_emp_no on salaries(emp_no);
```

### 答案

```sqlite
1、sqlite
select * from salaries indexed by idx_emp_no where emp_no = 10005;
```

```mysql
2、mysql
select * from salaries force index(idx_emp_no) where emp_no = 10005;
```

## 40、存在actor表，包含如下信息，现在在last_update后边新增一列名字为create_date,类型为datetime，not null，默认值为‘0000-00-00 00:00:00’

### 答案

```sqlit
1、sqlite
alter table actor add column create_date datetime not null default '0000-00-00 00:00:00';
```

```mysq
2、mysql
alter table actor add column create_date datetime not null after last_name;
```

## 41、构造一个触发器audit_log,在向employees_test表中插入一条数据的时候，触发插入相关的数据到audit中。

```mysql
create table employees_test(
	id int primary key not null,
    name text not null,
    age int not null,
    address char(50),
    salary real
);
create table audit(
	emp_no int not null,
    name text not null
);
```

### 答案

```mysql
create trigger audit_log
	after insert
	on employees_test
	for each row
begin
	insert into audit values(new.id, new.name);
end;
```

## 42、删除emp_no 重复的记录，只保留最小的id对应的记录。

```mysql
create table if not exists titles_test(
	id int not null primary key,
    emp_no int not null,
    title varchar(50) not null,
    from_date date not null,
    to_date date default null
);

insert into titles_test values
('1', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('2', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('3', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('4', '10004', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('5', '10005', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('6', '10006', 'Staff', '1996-08-03', '9999-01-01'),
('7', '10007', 'Senior Engineer', '1995-12-03', '9999-01-01')；
```

### 答案

```mysql
delete from title_test where id not in (select min(t.id)from titles_test t group by t.emp_no);
1、获取去重后的最小id临时表
select min(t.id) from titles_test t group by t.emp_no
2、将不在临时中的所有数据删除
```

## 43、将所有to_date为9999-01-01全部更新为NULL,且from_date更新为2001-01-01

```mysql
create table if not exists titles_test(
	id int not null primary key,
    emp_no int not null,
    title varchar(50) not null,
    from_date date not null,
    to_date date default null
);

insert into titles_test values
('1', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('2', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('3', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('4', '10004', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('5', '10005', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('6', '10006', 'Staff', '1996-08-03', '9999-01-01'),
('7', '10007', 'Senior Engineer', '1995-12-03', '9999-01-01')；
```

### 答案

```mysql
update titles_test set to_date = null, from_date = '2001-01-01' where to_date = '9999-01-01'
```

## 44、将id=5以及emp_no=10001的行数据替换成id=5以及emp_no=10005，其他数据保持不变，使用replace实现

```mysql
create table if not exists titles_test(
	id int not null primary key,
    emp_no int not null,
    title varchar(50) not null,
    from_date date not null,
    to_date date default null
);

insert into titles_test values
('1', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('2', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('3', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('4', '10004', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('5', '10005', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('6', '10006', 'Staff', '1996-08-03', '9999-01-01'),
('7', '10007', 'Senior Engineer', '1995-12-03', '9999-01-01')；
```

### 答案

```mysql
replace into titles_test values('5', '10005', 'Senior Enginner', '1986-06-06', '9999-01-01');
replace
into检测主键或者唯一索引冲突，冲突则替换，否则退化成 insert into 操作
```

## 45、将titles_test表名修改为titles_2017

```mysql
create table if not exists titles_test(
	id int not null primary key,
    emp_no int not null,
    title varchar(50) not null,
    from_date date not null,
    to_date date default null
);

insert into titles_test values
('1', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('2', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('3', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('4', '10004', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('5', '10005', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('6', '10006', 'Staff', '1996-08-03', '9999-01-01'),
('7', '10007', 'Senior Engineer', '1995-12-03', '9999-01-01')；
```

### 答案

```sqlite
1、sqlite
alter table titles_test rename to titles_2017
```

```mysql
2、mysql
alter table titles_test rename titles_2017
```

## 46、在audit表上创建外键约束，其emp_no对应employees_test表的主键id

```mysql
create table employees_test(
	id int primary key not null,
    name text not null,
    age int not null,
    address char(50),
    salary real
);

create table audit(
	emp_no int not null,
    create_date datetime not null
);
```

### 答案

```mysql
alter table audit add foreign key (emp_no) references employees_test (id);
本题在OJ上存在严格的格式限制
```

## 47、存在如下视图，如何获取emp_v和employees相同的数据

```mysql
create view emp_v as select * from employees where emp_no > 10005;
```

```mysql
create table employees(
	emp_no int not null,
    birth_date date not null,
    first_name varchar(14) not null,
    last_name varchar(16) not null,
    gender char(1) not null,
    hire_date date not null,
    primary key(emp_no)
);
```

### 答案

```mysql
select e.* from employees e, emp_v ev where e.emp_no = ev.emp_no;
视图的数据源于employees，只需要确保 employees.emp_no = emp_v.emp_no 即可
```

## 48、将所有获取奖金的员工当前的薪水增加10%

```mysql
create table emp_bonus(
	emp_no int not null,
    recevied datetime not null,
    btype smallint not null
);

create table salaries(
	emp_no int not null,
    salary int not null,
    from_date date not null,
    to_date date not null,
    primary key(emp_no, from_date)
);
```

### 答案

```mysql
批量更新
update salaries set salary = salary*1.1 where emp_no in (select emp_no from emp_bonus);
```

## 49、针对库中所有表生成select count(*)对应的SQL语句

```mysql
create table employyes(
	emp_no int not null,
    birth_date date not null,
    first_name varchar(14) not null,
    last_name varchar(16) not null,
    gender char(1) not null,
    hire_date date not null,
    primary key (emp_no)
);

create table emp_bonus(
	emp_no int not null,
    recevied datetime not null,
    btype smallint not null
);

create table dept_emp(
	emp_no int not null,
    dept_no char(4) not null,
    from_date date not null,
    to_date date not null,
    primary key (emp_no, dept_no)
);

create table dept_manager(
	dept_no char(4) not null,
    emp_no int not null,
    from_date date not null,
    to_date date not null,
    primary key(emp_no, from_date)
);
```

### 答案

```sqlit
1、sqlite
select "select count(*) from" || namr || ";" cnts from sqlite_master where type = "table";
```

```mysql
2、mysql
select concat('select count(*) from', table_name, ';') cnts from(select table table_name from information_schema.tables where table_name = employees)t;
```

## 50、将employees表中所有员工的last_name和first_name通过(‘)连接起来。

```mysql
create table employees(
	emp_no int not null,
    birth_date date not null,
    first_name varchar(14) not null,
    last_name varchar(16) not null,
    gender char(1) not null,
    hire_date date not null,
    primary key(emp_no)
);
```

## 答案

```sqlite
1、sqlite
select last_name || "'" || first_name name from employees;
```

```mysql
2、mysql
select concat(last_name, '\'', first_name) name from employees;
```

## 51、查找字符串‘10,A,B’中逗号‘,’出现的次数cnt

```mysql
select length('10, A, B') - length(replace('10, A, B ',',',''))cnt;
利用字符串替换函数，将子串替换为空。（源串长度-替换后的长度）/子串长度=子串出现次数
```

## 52、获取employees中的first_name，查询按照first_name最后两个字母，按照升序排序

```mysql
create table employees(
	emp_no int not null,
    birth_date date not null,
    first_name varchar(14) not null,
    last_name varchar(16) not null,
    gender char(1) not null,
    hire_date date not null,
    primary key(emp_no)
);
```

### 答案

```mysql
select first_name from employees order by substr(first_name, -2) asc;
字符串函数substr的使用
```

## 53、按照dept_no进行汇总，属于同一个部门的emp_no按照逗号进行连接，结果给出dept_no以及连接出的结果employees

```mysql
create table dept_emp(
	emp_no int not null,
    dept_no char(4) not null,
    from_date date not null,
    to_date date not null,
    primary key(emp_no, dept_no)
);
```

### 答案

```mysql
select dept_no, group_concat(emp_no) from dept_emp group by dept_no;
聚合函数 group_concat 的使用
```

## 54、查找排除当前最大、最小salary之后的员工的平均工资avg_salary

```mysql
create table salaries(
	emp_no int not null,
    salary int not null,
    from_date date not null,
    to_date date not null,
    primary key(emp_no, from_date)
);
```

### 答案

```mysql
select avg(salary) avg_salary from salaries s where to_date = '9999-01-01' and salary not in (select min(salary)from salaries where s.to_date = '9999-01-01')and salary not in (select max(salary) from salaries where s.to_date = '9999-01-01')
利用 min 和 max 函数获得最大值和最小值，而后用 not in 将最小值去除，求平均值
1、获得salary最大值maxRecord最小值minRecord记录（逻辑上需要按照当前时间的salary进行筛选）
select max(salary) from salaries where s.to_date = '9999-01-01';
select min(salary) from salaries where s.to_date = '9999-01-01';
2、去除最大值最小值记录t1
select * from salaries where to_date = '9999-01-01' and salary not in maxRecord and salary not in minRecord;
3、对上面的临时表求平均值即可
select avg(salary) from t1
```

## 55、分页查询employees表，每5行一页，返回第2页的数据

```mysql
create table employees(
	emp_no int not null,
    birth_date date not null,
    first_name varchar(14) not null,
    last_name varchar(16) not null,
    gender char(1) not null,
    hire_date date not null,
    primary key(emp_no)
);
```

### 答案

```mysql
select * from employees limit 5,5;
limit关键字的使用：
limit a,b
a：从第a条开始，默认0开始
b：返回b条
```

## 56、获取所有员工的emp_no、部门编号dept_no以及对应的bonus类型btype和recevied，没有分配具体的员工不显示

```mysql
create table dept_emp(
	emp_no int not null,
    dept_no char(4) not null,
    from_date date not null,
    to_date date not null,
    primary key(emp_no, dept_no)
);

create table dept_manager(
	dept_no char(4) not null,
    emp_no int not null,
    from_date date not null,
    to_date date not null,
    primary key (emp_no, dept_no)
);

create table employees(
	emp_no int not null,
    birth_date date not null,
    first_name varchar(14) not null,
    last_name varchar(16) not null,
    gender char(1) not null,
    hire_date date not null,
    primary key (emp_no)
);

create table salaries(
	emp_no int not null,
    salary int not null,
    from_date date not null,
    to_date date not null,
    primary key(emp_no, from_date)
);

create table emp_bonus(
	emp_no int not null,
    recevied datetime not null,
    btype smallint not null
);
```

### 答案

```mysql
1、获得所有员工对应的emp_no和dept_no
select e.emp_no, dept_no from employees e, dept_manager dm where e.emp_no = dm.emp_no;
2、要求btype和recevied字段没有分配具体的员工，这两个字段不显示，明示join操作
```

## 57、使用含有关键字exists查找未分配具体部门的员工的所有信息

```mysql
create table employees(
	emp_no int not null,
    birth_date date not null,
    first_name varchar(14) not null,
    last_name varchar(16) not null,
    gender char(1) not null,
    hire_date date not null,
    primary key(emp_no)
);

create table dept_emp(
	emp_no int not null,
    dept_no char(4) not null,
    from_date date not null,
    to_date date not null,
    primary key(emp_no, dept_no)
);
```

### 答案

```mysql
select * from employees e1 where not exists (select * from dept_emp de where e1.emp_no = de.emp_no);
子查询
```

## 58、获取employees中的行数据，且这些行也存在emp_v中；注意不能使用intersect关键字。

```mysql
create view emp_v as select * from eployees where emp_no > 10005;

create table rmployees(
	emp_no int not null,
    birth_date date not null,
    firth_name varchar(14) not null,
    last_name varchar(16) not null,
    gender char(1) not null,
    hire_date date not null,
    primary key (emp_no)
);
```

### 答案

```mysql
select * from emp_v;
出题不严谨，题本意应当是筛选出employees表中，存在于emp_v视图中的相同行
1、可以投机取巧，由于emp_v视图全部来源于employees，所以直接查询即可
select * from emp_v;
2、按照题目原意
select e.* from employees e, emp_v ev where e.emp_no = ev.emp_no;
```

## 59、获取有奖金员工的相关信息

给出emp_no、first_name、last_name、奖金类型btype、对应的薪水情况salary以及奖金金额bonus。bonus类型btype为1其奖金为薪水salary的10%，btype为2的其奖金为薪水的20%，其他类型奖金为薪水的30%。当期薪水表示to_date=‘9999-01-01’

```mysql
create table employees(
	emp_no int not null,
    birth_date date not null,
    first_name varchar(14) not null,
    last_name varchar(16) not null,
    gender char(1) not null,
    hire_date date not null,
    primary key (emp_no)
);

create table dept_emp(
	emp_no int not null,
    dept_no char(4) not null,
    from_date date not null,
    to_date date not null,
    primary key(emp_no,dept_no)
);

create table emp_bonus(
	emp_no int not null,
    recevied datetime not null,
    btype smallint not null
);

create table salaries(
	emp_no int not null,
    salary int not null,
    from_date date not null,
    to_date date not null,
    primary key (emp_no, from_date)
);
```

```mysql
一般答案：
select eb.emp_no, e.first_name, e.last_name, eb.btype, s.salary, (s.salary*btype*0.1) bonus from emp_bonus eb, employees e, salaries s where eb.emp_no = e.emp_no and eb.emp_no = s.emp_no and s.to_date = '9999-01-01';
利用条件判断法：
select eb.emp_no,
	e.first_name,
	e.last_name,
	eb.btype,
	s.salary,
	(case btype
    	when 1 then s.salary*0.1
     	when 2 then s.salary*0.2
     	when 3 then s.salary*0.3
     	end
    )bonus from 
emp_bonus eb,
employees e,
salaries s 
where eb.emp_no = e.emp_no
  and eb.emp_no = s.emp_no
  and s.to_date = '9999-01-01';
简单的拼接表筛选可以解决问题，但是考点是case-when语句
语法规则：
case colume
	when condition then result
	when condition then result
	when condition then result
	else result
end
```

## 60、按照salary的累计和running_total,其中running_total为前两个员工的salary累计和，其他一次类推，具体Demo展示如下：

```mysql
create table salaries(
	emp_no int not null,
    salary int not null,
    from_date date not null,
    to_date date not null,
    primary key (emp_no, from_date)
);
```

### 答案

```mysql
select s2.emp_no, s2.salary, sum(s1,salary) running_total from (select emp_no, salary from salaries where to_date = '9999-01-01')s1 inner join (select emp_no, salary from salaries where to_date = '9999-01-01')s2 on s1.emp_no < s2.emp_no group by s2.emp_no;
1、获取当前员工的工资情况s1
select emp_no, salary from salaries where to_date = '9999-01-01';
2、连接当前员工信息s2，筛选出emp_no小于当前用户的所有员工信息
s1 inner join(select emp_no, salary from salaries where to_date = '9999-01-01')s2 on s1.emp_no <= s2.emp_no;
3、根据emp_no进行分组，对salary列进行求和
select s2.emp_no, s2.salary, sum(s1.salary)running_total from s1 inner join s2 on s1.emp_no <= s2.emp_no group by s2.emp_no;
```

## 61、对于employees表中，给出奇数行的first_name

```mysql
create table employees(
	emp_no int not null,
    birth_date date not null,
    first_name varchar(14) not null,
    last_name varchar(16) not null,
    gender char(1) not null,
    hire_date date not null,
    primary key (emp_no)
);
```

### 答案

```mysql
select e1.first_name from employees e1 where(select count(*) from employees e2 where e1.first_name <= e2.first_name) % 2 = 1;
对于MySQL版本来说，可以添加一列临时数据来进行奇偶排序，而后筛选出奇数行
select first_name from(select (@i := @i + 1)sn, employees.* from employees, (select @i := 0)a)t where sn % 2 = 1;
先排号码，而后取奇数
外层获取奇数行
select e1.first_name from employees e1 where (条件) % 2 = 1
count获得排序号码
select count(*) from employees e2 where e1.first_name <= e2.dirst_name;
```



















