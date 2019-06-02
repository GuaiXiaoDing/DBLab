# 作业一
## 1、数据更新
### （1）向教室表（classroom）中插入如下数据：
        教学楼（building）为Art，房间（room_number）为608，空间为400。
> insert into classroom values ('Art', '608', '400');
### （2）向教室表中（classroom）中插入如下数据，要求能替换上一题数据：
        教学楼（building）为Art，房间（room_number）为608，空间为600。
> replace into classroom values ('Art', '608', '600');
### （3）将部门（department）表中部门名（dept_name）为'Comp. Sci.'（注意空格）的一行中，部门名修改为English，教学楼（building）、预算（budget）修改为NULL；修改成功后再将其改回原来的值。（写两条SQL语句）
> update department set dept_name = 'English', building = NULL, budget = NULL where dept_name = 'Comp. Sci.';
> update department set dept_name = 'Comp. Sci.', building = 'Taylor', budget = '100000' where dept_name = 'English';
### （4）在教师表（instructor）中，将工资低于60000（包括60000）的老师工资增加20%。 
> update instructor set salary = salary * 1.2 where salary <= 60000;
## 2、简单查询
### （1）查询学生表（student）中第2-6条记录。
ID|name|dept_name|tot_cred
> select * from student limit 1, 5;
### （2）查询学生表（student）中部门名称（dept_name）含有“sci”(不限大小写)的所有记录。
ID|name|dept_name|tot_cred
> Select * from student where dept_name like '%sci%';
### （3）查询部门表（department）中所有不重复的教学楼（building）:
building
> select distinct building from department;
### （4）查询教室表（classroom）中不在Watson这栋教学楼（building）的记录（两种方法，提示：NOT）。
building|room_number|capacity
> select * from classroom where building != 'Watson';
> select * from classroom  
where building not in (select building  
　　　　　　　　　　from classroom  
　　　　　　　　　　where building = 'Watson');  
### （5）查询部门表（department）中所有预算（budget）在[80000，100000]的记录（两种方法，要求将查询结果的表头显示为中文）。
部门名|教学楼|预算
> select dept_name as 部门名, building as 教学楼, budget as 预算 　
from department 　
where budget >= 80000 and budget <= 100000;
> select dept_name as 部门名, building as 教学楼, budget as 预算  
from department  
where budget in (select budget  
　　　　　　　　from department  
　　　　　　　　where budget >= 80000 and budget <= 100000);
### （6）查询教师表（instructor）中部门名（dept_name）为Comp. Sci.的教师姓名（name）、部门名称（dept_name）、薪水（salary）,按薪水降序排列。
name|dept_name|salary
> select name, dept_name, salary  
from instructor  
where dept_name = 'Comp. Sci.'  
order by salary desc;        
### （7）查询课程表（course）中部门名（dept_name）在Biology、Comp. Sci.、Music这三者之一的课程编号（course_id）、课程名（title）和部门名。
course_id|title|dept_name
> select course_id, title, dept_name  
from course  
where dept_name = 'Biology' or dept_name = 'Comp. Sci.' or dept_name = 'Music';
# 作业二
## 1、数据类型实验
### （1）创建表test_user，字段信息如下，写出创建表的SQL语句。
id，类型为int，自增，非空，主键  
username，类型为varchar(30)，非空  
sex，类型为char(2)，可空  
phone，类型为varchar(10)，可空  
birthday，类型为date，可空  
> create table test_user (  
  id int not null primary key auto_increment,  
  username varchar(30) not null,  
  sex char(2),  
  phone varchar(10),  
  birthday date  
)
### （2）将表test_user中sex字段修改为取值只能是“F”或“M”，默认为F
> alter table test_user modify sex enum('F','M') default 'F';
### （3）将表test_user中的phone字段修改为telephone字段，且数据类型修改为varchar(20)
> alter table test_user change phone telephone varchar(20);
### （4）为test_user表中的telephone字段添加唯一性约束，约束名为phone_unique
> alter table test_user add constraint phone_unique unique(telephone);
### （5）执行下面的创建表语句：
CREATE TABLE test_float (  
  id int(11) NOT NULL DEFAULT '0',  
  col float DEFAULT NULL,  
  col2 decimal(5,2) DEFAULT NULL,  
  col3 decimal(10,2) DEFAULT NULL,  
  col4 decimal(20,2) DEFAULT NULL  
) ENGINE=InnoDB DEFAULT CHARSET=utf8  
执行下面的插入语句：  
insert into test_float values(1,123456.123456,123.45,12345678.123456,1234567890123456.78)  
执行下面的查询语句：  
select * from test_float  
问哪个/些列/字段数据与插入时候不同？为什么？
> 不同的列：col,col3  
原因：float类型最多能存6位，所以123456.123456变为123456；decimal(10,2)表示存两位小数，所以插入12345678.12
## 2、基本查询
### （1）列出Comp.Sci.系开设的具有3个学分的课程名称。
title
> select title from course where dept_name='Comp. Sci.' and credits=3;
### （2）列出所有没有选修过课程的学生信息，显示学生编号、姓名、系名、总学分。
ID|name|dept_name|tot_cred
> select * from student where ID not in (select ID from takes);
### （3）列出教师的最高工资，此列命名为max_salary。
max_salary
> select salary as max_salary  
from instructor  
where salary>=all(select salary from instructor);
### （4）列出在2009年秋季和2010年春季同时开课的所有课程的集合，显示课程编号和课程名称。
course_id|title
> select * from (select course_id,title  
　　　　　　from section natural join course  
　　　　　　where year = 2010 and semester='Spring')as S  
　　　　　　natural join (select course_id,title  
　　　　　　　　　　　from section natural join course  
　　　　　　　　　　　where year=2009 and semester='Fall')as T;
### （5）列出教师平均工资超过42000美元的系，显示系名和平均工资（命名为avg_salary），平均工资保留2位小数点。
dept_name|avg_salary
> select dept_name, format(avg(salary),2)as avg_salary  
from instructor  
group by dept_name  
having avg(salary) > 42000;
### （6）从2009年春季（Spring）开设的所有课程段中，列出最多的选课人数，显示选课人数（命名为enrollment）。
enrollment
> select count(ID) as enrollment  
from section natural join takes  
where year = 2009 and semester = 'Spring'  
group by course_id
having count(ID)>=all(select count(ID)  
　　　　　　　　　　　　　　　　　　　from section natural join takes  
　　　　　　　　　　　　　　　　　　　where year = 2009 and semester = 'Spring'  
　　　　　　　　　　　　　　　　　　　group by course_id  
　　　　　　　　　　　　　　　　　　　having count(ID));
# 作业三
### （1）查询教师表(instructor)中存在的同一学期(年份和学期均需相等)教授(teaches)过超过一门课程的教师信息。
ID、name、dept_name、salary
> select ID, name, dept_name, salary  
from instructor  
where ID in ( select ID  
　　　　　　from ( select ID, count(course_id) as count_course_id   
　　　　　　　　　from teaches  
　　　　　　　　　group by semester, year, ID)as T  
　　　　　　where T.count_course_id > 1);
### （2）查询课程表(course)中满足部门(department)预算(budget)超过85000的课程ID(course_id)、课程名(title)、
部门名称(dept_name)。
course_id、title、dept_name
> select course_id, title, dept_name  
from course natural join department  
where budget > 85000;
### （3）查询部门表(department)中满足教师(instructor)平均工资(salary)高于80000的部门名称(dept_name)、教学楼(building)。
dept_name、building
> select dept_name, building  
from instructor natural join department  
group by dept_name  
having avg(salary) > 80000;
### （4）查询出学生姓名(name)为Tanaka的学生的学生ID(ID)、学生姓名(name)、部门名称(dept_name)、课程ID(course_id)、
等第(grade)。
ID、name、dept_name、course_id、grade
> select ID, name, dept_name, course_id, grade  
from student natural join takes  
where name = 'Tanaka';
### （5）查询学生姓名(name)、课程名(title)、等第(grade)，在学生表(student)、选课表(takes)、课程表(course)上，要求没人
选的课程名也需要输出（外连接）。
name、title、grade
> select name, title, grade  
from course left outer join (select name, course_id, grade  
　　　　　　　　　　　　　　　　from student natural join takes)as T on course.course_id = T.course_id;
### （6）查询教师名(name)、课程名称(title)、学期(semester)、年份(year)，在教师表(instructor)、教师教课表(teaches)、
课程表(course)上，要求没有教师上的课也需要输出。
name、title、semester、year

### （7）查询只选修了一门课的学生的学生信息。
ID、name、dept_name、tot_cred

### （8）查询学生名、课程名、教学楼、教室、教室大小、学期、年份、等第，在学生表、选课表、教室安排、课程表、教室上。
name、title、building、room_number、capacity、semester、year、grade

### （9）查询选修了由‘Crick’老师教学的某一门课程的学生姓名（name）。
name
