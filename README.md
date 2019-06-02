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
from course left outer join(select name, course_id, grade  
　　　　　　　　　　　　from student natural join takes)as T on course.course_id = T.course_id;

### （6）查询教师名(name)、课程名称(title)、学期(semester)、年份(year)，在教师表(instructor)、教师教课表(teaches)、
课程表(course)上，要求没有教师上的课也需要输出。
name、title、semester、year
> select name, title, semester, year  
from course left outer join(select name, course_id, semester, year  
　　　　　　　　　　　　from instructor natural join teaches)as T on course.course_id = T.course_id;

### （7）查询只选修了一门课的学生的学生信息。
ID、name、dept_name、tot_cred
> select ID, name, dept_name, tot_cred  
from student natural join takes  
group by ID  
having count(course_id) = 1;

### （8）查询学生名、课程名、教学楼、教室、教室大小、学期、年份、等第，在学生表、选课表、教室安排、课程表、教室上。
name、title、building、room_number、capacity、semester、year、grade
> select name, title, building, room_number, capacity, semester, year, grade  
from student natural join takes natural join section natural join course natural join classroom;


### （9）查询选修了由‘Crick’老师教学的某一门课程的学生姓名（name）。
name
> select distinct S.name  
from ( select course_id  
　　　from instructor natural join teaches  
　　　where instructor.`name`='Crick')as T natural join (select name, course_id  
　　　　　　　　　　　　　　　　　　　　　　　　from takes natural join student) as S;

# 作业四
### 1、查询获得的总学分（tot_cred）比Comp. Sci.系每位学生的总学分都高的学生信息，显示学号、姓名、系别、总学分。
ID|name|dept_name|tot_cred
> select *  
from student  
where tot_cred > all(select tot_cred from student where dept_name = 'Comp. Sci.');

### 2、查询选修课程的数量与“Zhang”一样多的学生的学号、姓名、系别。
ID|name|dept_name
> select S.ID,S.name,S.dept_name  
from student as S  
where S.ID in ( select T.ID  
　　　　　　　from takes as T  
　　　　　　　group by T.ID  
　　　　　　　having count(*) in (select count(*)  
　　　　　　　　　　　　　　　from student natural join takes  
　　　　　　　　　　　　　　　where name = 'Zhang'  
　　　　　　　　　　　　　　　group by name)) and S.name != 'Zhang';
### 3、查询选修了Comp. Sci.系开设的全部课程的学生姓名（注：为验证查询正确性，你可能需要自己往数据库表中添加一些测试数据）
name
> select name  
from student  
where not exists( select *  
　　　　　　　　from course  
　　　　　　　　where dept_name='Comp. Sci.'  
　　　　　　　　and not exists( select *  
　　　　　　　　　　　　　　　from takes  
　　　　　　　　　　　　　　　where takes.ID = student.ID and takes.course_id = course.course_id));
===================================================================  
以下实验需要建立grade_points表，提供字母表示的成绩到绩点的对应关系，执行如下语句：   
CREATE TABLE `grade_points` (  
  `grade` varchar(2) NOT NULL,  
  `points` decimal(5,1) DEFAULT NULL,  
  PRIMARY KEY (`grade`)  
) ENGINE=InnoDB DEFAULT CHARSET=utf8;  
INSERT INTO `grade_points` VALUES ('A', '4.0');  
INSERT INTO `grade_points` VALUES ('A-', '3.7');  
INSERT INTO `grade_points` VALUES ('B', '3.0');  
INSERT INTO `grade_points` VALUES ('B+', '3.3');  
INSERT INTO `grade_points` VALUES ('B-', '2.7');  
INSERT INTO `grade_points` VALUES ('C', '2.0');  
INSERT INTO `grade_points` VALUES ('C+', '2.3');  
INSERT INTO `grade_points` VALUES ('C-', '1.5');  
INSERT INTO `grade_points` VALUES ('D', '1.3');  
INSERT INTO `grade_points` VALUES ('D-', '1.0');  
INSERT INTO `grade_points` VALUES ('F', '0.0');  

### 4、根据ID为12345的学生所修的所有课程，计算该生的加权平均绩点（加权平均绩点=各科成绩对应绩点*各科学分/各科学分总和），保留2位小数点（使用convert，下同）。
ID|avg_points
> select ID, convert(sum(points * credits) / sum(credits), decimal(5,2)) as avg_points  
from takes natural join grade_points natural join course  
where ID = 12345  
group by ID;  

### 5、列出每个学生的加权平均绩点，按从高到底排列，显示学号、姓名、加权平均绩点，保留2位小数点，按加权平均绩点逆序排列。
ID|name|avg_points
> select ID, name, convert(sum(points * credits) / sum(credits), decimal(5,2)) as avg_points  
from takes natural join grade_points natural join course natural join student  
group by ID  
order by avg_points desc;

### 6、列出每个课程在每学年、每学期、每个课程段的学生平均绩点，保留2位小数点，按平均绩点逆序排列。
course_id|sec_id|semester|year|course_ave_points
> select course_id, sec_id, semester, year, convert(sum(points * credits) / sum(credits), decimal(5,2)) as course_ave_points  
from takes natural join grade_points natural join course  
group by course_id, year, semester,sec_id  
order by course_ave_points desc;

=======================================================  
以下实验需要建立test_rollup表，执行如下语句：  
CREATE TABLE `test_rollup` (  
  `orderid` int(11) NOT NULL,  
  `orderdate` date NOT NULL,  
  `empid` int(11) NOT NULL,  
  `custid` varchar(10) NOT NULL,  
  `qty` int(11) NOT NULL,  
  PRIMARY KEY (`orderid`,`orderdate`)  
) ENGINE=InnoDB DEFAULT CHARSET=utf8;   
INSERT INTO `test_rollup` VALUES ('1', '2017-01-02', '3', 'A', '10');  
INSERT INTO `test_rollup` VALUES ('2', '2018-04-02', '2', 'B', '20');  
INSERT INTO `test_rollup` VALUES ('3', '2018-05-02', '1', 'A', '30');  
INSERT INTO `test_rollup` VALUES ('4', '2018-07-02', '3', 'D', '40');  
INSERT INTO `test_rollup` VALUES ('5', '2018-01-02', '4', 'A', '20');  
INSERT INTO `test_rollup` VALUES ('6', '2018-01-02', '3', 'B', '30');  
INSERT INTO `test_rollup` VALUES ('7', '2018-01-02', '1', 'C', '40');  
INSERT INTO `test_rollup` VALUES ('8', '2018-01-02', '2', 'A', '10');  
INSERT INTO `test_rollup` VALUES ('9', '2018-01-02', '3', 'B', '20');  

各个字段说明如下：  
orderid--销售流水号  
orderdate--销售日期  
empid--销售员编号  
custid--顾客编号  
qty--购买数量  
### 7、使用rollup查询各个年度（命名为year）的销售总额（命名为sum）
year|sum  
> select YEAR(orderdate) as year, sum(qty) as sum  
from test_rollup  
group by year  
with rollup;  

### 8、使用rollup统计各个销售员、各个顾客、各个年份的销售总数
> select empid, custid, YEAR(orderdate) as year, sum(qty) as sum  
from test_rollup  
group by empid, custid, year  
with rollup;  

# 作业五
### 1、创建一个视图，要求可替换已有同名视图，内容为name，title，grade，视图名为学生成绩，查询表为student，takes，course。在视图创建完成后，利用该视图查询name为Zhang的学生成绩。
> create or replace view 学生成绩 (name, title, grade)  
as select name, title, grade  
from student natural join takes natural join course;

>select * from 学生成绩 where name = 'Zhang';
### 2、创建一个视图，要求可替换已有同名试图，内容为ID，name，salary，视图名为“导师”，查询表为instructor，salary > 80000，要求向视图中插入数据时，salary必须均>80000。创建完视图后，分别插入如下两则数据，并查看instructor表和视图中相应变化。
（ID=12345，name=Zhang，salary=79999）  
（ID=23456，name=Zhang，salary=89999）
> create or replace view 导师 (ID, name, salary)  
as select ID, name, salary  
from instructor  
where salary > 80000  
with check option;

> insert into 导师 values(12345, 'Zhang', 79999);

> insert into 导师 values(23456, 'Zhang', 89999);

### 3、在takes表上定义一个BEFORE INSERT触发器，触发器名为“setnull”。当插入一条记录时，假设所插入的分数的值为' '（空格）则表明该分数发生缺失，所以定义此触发器在分数值条件满足时触发使用null值来代替' '。
> create trigger setnull before insert  
on takes for each row  
begin  
if(new.grade=' ')then  
set new.grade = null;  
end if;  
end;

### 4、在classroom表上创建BEFORE UPDATE触发器，创建名为“classroom_update_before_trigger”触发器，负责对classroom表的进行UPDATE修改检查，要求修改表中capacity的值小于10或大于500的，均保持原值不变。
> create trigger classroom_update_before_trigger before update  
on classroom for each row  
begin  
if(new.capacity < 10 || new.capacity > 500)then  
set new.capacity = old.capacity;  
end if;  
end;  

### 5、 在takes表上定义一个AFTER UPDATE触发器，触发器名为“credit_get”，当takes表中元组的属性grade被更新时，使得学生获得的总学分（学生表的tot_cred属性）更新。只有当属性grade从空值或者’F’被更新为代表课程已经完成的具体分数时，（具体分数是A-C-）触发器才会被激发。
> create trigger credit_get after update  
on takes for each row  
begin  
if(new.grade != 'F' and new.grade != '' and (old.grade = 'F' or new.grade = '' ))then  
update student  
set tot_cred = tot_cred + (select credits from course where course.course_id = new.course_id)  
where student.ID = new.ID;  
end if;  
end;

### 6、在section表上定义一个AFTER INSERT触发器，触发器名为“timeslot_check”，当对课程section表执行任何插入操作后触发器被启动，以确保插入元组的time_slot_id属性即上课时间段必须存在于time_slot表中。
> create trigger timeslot_check after insert  
on section for each row  
begin  
if(new.time_slot_id not in (select time_slot_id from time_slot))then  
delete from section  
where time_slot_id = new.time_slot_id;  
end if;  
end;

# 作业六
### 1、编写存储过程pr_course_avgPoints,列出每个课程在每学年、每学期、每个课程段的平均绩点，保留2位小数点，按平均绩点逆序排列,显示课程名、课程段、学期、年份、平均绩点。
title|sec_id|semester|year|course_avg_point
> create procedure pr_course_avgPoints()  
begin  
select title, sec_id,semester, year, round(avg(points), 2) as course_avg_point  
from course natural join takes natural join grade_points  
group by title, sec_id, semester, year  
order by avg(points) desc;  
end;

### 2、编写存储过程pr_high_budget,输出预算费高于80000元的系信息，显示系名、所在楼宇名，预算费以参数的形式输入。写出创建和运行存储过程的语句。
dept_name|building|budget
> create procedure pr_high_budget(in parameter int)  
begin  
select dept_name, building, budget  
from department  
where budget > parameter;  
end;  

> call pr_high_budget(80000);  

### 3、编写存储过程pr_hot_course,输出某年度选课人数最多的课程人数，年度作为输入参数，人数作为输出参数。写出创建和运行存储过程的语句。
> create procedure pr_hot_course(in in_year int,out out_count int)  
begin  
select count(course_id) into out_count  
from takes  
where year = in_year  
group by course_id  
having count(course_id) >= all  
(select count(course_id)  
from takes  
where year = in_year  
group by course_id);  
end;  

> call pr_hot_course(2009, @out_count);

### 4、编写函数func_rand_str，可随机生成长度为n（n<=255）的字符串，字符串中符号可包括a-z、A-Z、0-9,n作为输入参数。
> create function func_rand_str(n int) returns varchar(255)  
begin  
declare symbol varchar(62) default "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";   
declare result varchar(255) default "";  
declare i int default 0;  
while i < n do    
　　set result = CONCAT(result, SUBSTRING(symbol, FLOOR(1+RAND()*62), 1));  
　　set i = i + 1;  
end while;  
return result;  
end;

### 5、编写函数func_rand_telnum，可随机生成长度为11为的手机号，开头为130 131 132 133 134 135 136 137 138 139 186 187 189 151 157。
> create function func_rand_telnum() returns varchar(11)  
begin  
declare head char(3);  
declare heads varchar(60) default "130 131 132 133 134 135 136 137 138 139 186 187 189 151 157";  
declare pos int;  
declare digit varchar(10) default "0123456789";  
declare tail varchar(255) default "";  
declare result varchar(11);  
declare i int default 0;  
set pos = 1 + FLOOR(RAND() * 15) * 4;      
set head = trim(substring(heads, pos, 3));  
while i < 8 do  
　　set tail = CONCAT(tail, substring(digit, FLOOR(1 + RAND() * 10), 1));  
　　set i = i +1;  
end while;  
set result = trim(concat(head, tail));  
return result;  
end;

### 6、编写存储过程pr_inset_testCases，往test_user表中插入100条测试数据，其中username是函数func_rand_str生成的长度为8的字符串，telephone由函数func_rand_telnum生成，birthday为当前插入日期随机减去20-40年，sex均为M。请先去掉telphone的Unique约束。
> create procedure pr_inset_testCases()  
begin  
declare i int default 1;  
declare j int;  
alter table test_user drop index phone_unique;  
while i <= 100 do  
　　set j = FLOOR(20 + RAND()*21);  
　　insert into test_user(id, username, sex, telephone, birthday) values (i, func_rand_str(8), "M", func_rand_telnum(), DATE_SUB(NOW(), INTERVAL j year));  
　　set i = i + 1;  
end while;  
end;  

# 作业七
### 1、建立函数getPrereq_id，实现用迭代输出某个课程（作为函数输入）的所有先修课程号（包含本课程的课程号），用‘|’隔开。输入以下两条指令以查看效果。
INSERT INTO prereq VALUES('BIO-101', 'PHY-101');  
SELECT getPrereq_id('BIO-399');
> create function getPrereq_id(id varchar(20)) returns varchar(255)  
begin  
　　declare now_course varchar(20) default "";  
　　declare prereq_course varchar(20) default "";  
　　declare return_str varchar(255) default "";        
　　set now_course=id;  
　　set return_str=now_course;  
　　while now_course!=prereq_course do  
　　　　set prereq_course=now_course;  
　　　　select prereq_id into now_course from prereq where course_id=now_course;  
　　　　if now_course!=prereq_course then set return_str=concat(return_str,'|',now_course);  
　　　　end if;  
　　end while;  	
　　return return_str;  
end;

### 2、在university数据库中执行脚本filesystem.sql，建立表filesystem并导入测试数据，建立函数getFullpath，实现用迭代输出该路径下的完整路径。输入以下指令以查看效果。
SELECT getFullpath('bin/');
> create function getFullpath(dir varchar(20)) returns varchar(255)  
begin  
　　declare now_dir varchar(20) default "";  
　　declare last_dir varchar(20) default "";  
　　declare return_str varchar(255) default "";          
　　set now_dir=dir;  
　　set return_str=now_dir;  
　　while now_dir!=last_dir do  
　　　　set last_dir=now_dir;  
　　　　select ParName into now_dir from filesystem where DirName=now_dir limit 1;  
　　　　if now_dir!=last_dir then set return_str=concat(now_dir,return_str);  
　　　　end if;  
　　end while;  
　　return return_str;  
end;

### 3、请创建一个存储过程sp_updatesalary()，利用游标完成如下操作：为工资小于70000的老师增加20%收入，工资大于等于70000的老师增加15%的收入。
> create procedure sp_updatesalary()  
begin  
　　declare a varchar(5);  
　　declare b varchar(20);  
　　declare c varchar(20);  
　　declare d decimal(8,2);  
　　declare e decimal(8,2);  
　　declare cur1 cursor for select ID, name, dept_name, salary from instructor;  
　　declare exit handler for not found close cur1;  
　　open cur1;  
　　repeat  
　　　　fetch cur1 into a,b,c,d;  
　　　　if d < 70000 then update instructor set salary = salary * 1.2 where ID = a;  
　　　　else update instructor set salary = salary * 1.15 where ID = a;  
　　　　end if;  
　　until 0 end repeat;  
　　close cur1;  
end;

# 作业八
## 1、编写存储过程pr_budget，使用事务在表department上将一个系的budget转账到另外一个系的budget中。三个输入参数依次为：withdrawDept代表转出金额系的系名，depositDept代表转入金额系的系名，money代表转账金额。显示转账成功与否，1为失败，0为成功。
注：先将department表上的budget字段设置成无符号数，自行调用存储过程验证转账成功和失败。
> create procedure pr_budget(in withdrawDept varchar(20), in depositDept varchar(20), in money decimal(12,2), out state int)  
modifies sql data  
begin  
declare continue handler for 1264  
alter table instructor modify budget int unsigned;  
begin  
　　rollback;  
　　set state = 1;  
end;  
set state = 0;  
start transaction;  
update department set budget = budget + money where dept_name = depositDept;  
update department set budget = budget - money where dept_name = withdrawDept;  
commit;  
end


## 2、MySQL隔离级别验证实验
1）将instructor表中编号12121的名为Wu的教工的salary设置为10000元

2）同时开两个命令行界面，称为A和B，在A、B两个窗口都执行下面的语句，设定隔离级别为“读未提交”。  
set tx_isolation='read-uncommitted';  
可以执行select @@session.tx_isolation;验证设定是否已经成功。  

3）在A窗口执行如下语句：  
begin;  
select * from instructor;  
可以看到所有教工的信息，编号12121的名为Wu的教工的salary当前值应该为10000  

4）在B窗口执行如下语句：  
begin;  
update instructor set salary = salary - 500 where id= 12121;  
select * from instructor;  


5）在A窗口执行如下语句：  
select * from instructor;  
请回答：  
Q1：是否可以看到编号12121的名为Wu的教工的salary值被B窗口修改但未提交的结果，回答是/否。  
### A1：是
Q2：这种现象的名称是？  
### A2：脏读

6）在B窗口执行如下语句：  
rollback;  

7）在A窗口执行如下语句：  
update instructor set salary = salary - 500 where id= 12121;  
select * from instructor;  
Q：当前编号12121的名为Wu的教工的salary值为多少？  
### A：9500

8）在A窗口执行如下语句：  
rollback;  
select * from instructor;  

9）在A、B两个窗口都执行下面的语句，设定隔离级别为“读已提交”。  
set tx_isolation='read-committed';  
可以执行select @@session.tx_isolation;验证设定是否已经成功。  

10）在A窗口执行下面语句：  
begin;  
select * from instructor;  

11）在B窗口执行下面语句：  
begin;  
select * from instructor;  
update instructor set salary = salary - 500 where id= 12121;  

12）在A窗口执行如下语句：  
select * from instructor;  
请回答：  
Q1：当前编号12121的名为Wu的教工的salary值为多少？  
### A1：10000
Q2：是否可以看到编号12121的名为Wu的教工的salary值被B窗口修改但未提交的结果，回答是/否  
### A2：否

13）在B窗口执行下面语句：  
commit;  
select * from instructor;  

14）在A窗口执行如下语句：  
select * from instructor;  
commit;  
请回答：  
Q1：当前编号12121的名为Wu的教工的salary值为多少？  
### A1：9500
Q2：是否可以看到编号12121的名为Wu的教工的salary值被B窗口修改并已提交的结果，回答是/否  
### A2：是
Q3：A窗口当前两次的select * from instructor;查询结果是否一致？  
### A3：否
Q4：这种现象的名称是？  
### A4：不可重复读

15）在A、B两个窗口都执行下面的语句，设定隔离级别为“可重复读”。  
set tx_isolation='repeatable-read';  
可以执行select @@session.tx_isolation;验证设定是否已经成功。  

16）在A窗口执行下面语句：  
begin;  
select * from instructor;  

16）在B窗口执行下面语句：  
begin;  
select * from instructor;  
update instructor set salary = salary - 500 where id= 12121;  
commit;  
select * from instructor;  

17）在A窗口执行如下语句：  
select * from instructor;  
Q1：当前编号12121的名为Wu的教工的salary值为多少？
### A1：9500
Q2：是否可以看到编号12121的名为Wu的教工的salary值被B窗口修改并已提交的结果，回答是/否
### A2：否
Q3：A窗口当前两次的select * from instructor;查询结果是否一致？
### A3：是

18）在A窗口执行如下语句：  
update instructor set salary = salary - 500 where id= 12121;  
commit;  
select * from instructor;  
Q1：当前编号12121的名为Wu的教工的salary值为多少？  
### A1：8500


19）在A窗口执行如下语句：  
begin;  
select * from instructor;  

20)在B窗口执行下面语句：  
begin;  
insert into instructor values(99999,'test','Comp. Sci.',10000);  
commit;  
select * from instructor;  

21）在A窗口执行如下语句：  
select * from instructor;  
Q1：是否可以看到B窗口新插入并已提交的结果，回答是/否  
### A1：否

22）在A窗口执行如下语句：  
insert into instructor values(99999,'test','Comp. Sci.',10000);  
Q1：A窗口是否能插入此条数据，回答是/否
### A1：否
Q2：这种现象的名称是？
### A2：幻读

23）在A窗口执行如下语句：  
update instructor set salary = 6666 where id=99999;  
select * from instructor;  
commit;  
Q1：A窗口是否更新了一条自己会话中查不到的数据，回答是/否
### A1：是

# 作业九
### a)	查询学生信息表中所有学生信息，按照年龄升序排列
> select * from student order by BIRTHDAY desc;

### b)	查询课程信息表中课程的总数
> select count(CNO) from course;

### c)	查询课程信息表中剩余开课人数小于等于10的课程详细信息
> select * from course where LEFT_NUM <= 10;

### d)	查询课程信息表中课程编号为2014开头的课程详细信息
> select * from course where CNO like '2014%';

### e)	查询选修课程号为20140002的学生的学号与姓名
> select SNO, SNAME from sc natural join student where CNO = '20140002';

### f)	查询选修课程名为独孤九剑的学生姓名、年龄、课程名称、课程成绩
> select SNAME, AGE, CNAME, GRADE from student natural join sc natural join course where CNAME = '独孤九剑';

### g)	查询计算机与软件学院学生姓名为令狐冲的学生姓名、年龄、性别、出生日期、年龄、学院名、课程名称、课程成绩
> select SNAME, AGE, SEX, BIRTHDAY, DNAME, CNAME, GRADE  
from dept natural join student natural join sc natural join course   
where DNAME = '计算机与软件学院' and SNAME = '令狐冲';
