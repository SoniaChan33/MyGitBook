# MySQL learning

## 引入

### 1.数据库基本概念

#### 1.1 数据

所谓数据（Data）是指对客观事物进行描述并可以鉴别的符号，这些符号是可识别的、抽象的。它不仅仅指狭义上的数字，而是有多种表现形式：字母、文字、文本、图形、音频、视频等。现在计算机存储和处理的数据范围十分广泛，而描述这些数据的符号也变得越来越复杂了。

#### 1.2 数据库

数据库（Database，DB）指的是以一定格式存放、能够实现多个用户共享、与应用程序彼此独立的数据集合。

#### 1.3 数据库管理系统

数据库管理系统（Database Management System，DBMS）是用来定义和管理数据的软件。如何科学的组织和存储数据，如何高效的获取和维护数据，如何保证数据的安全性和完整性，这些都需要靠数据库管理系统完成。目前，比较流行的数据库管理系统有：Oracle、MySQL、SQL Server、DB2等。

#### 1.4数据库应用程序

数据库应用程序（Database Application System，DBAS）是在数据库管理系统基础上，使用数据库管理系统的语法，开发的直接面对最终用户的应用程序，如学生管理系统、人事管理系统、图书管理系统等。

#### 1.5 数据库管理员

数据库管理员（Database Administrator，DBA）是指对数据库管理系统进行操作的人员，其主要负责数据库的运营和维护。

#### 1.6 最终用户

最终用户（User）指的是数据库应用程序的使用者。用户面向的是数据库应用程序（通过应用程序操作数据），并不会直接与数据库打交道。

#### 1.7 数据库系统

数据库系统（Database System，DBS）一般是由数据库、数据库管理系统、数据库应用程序、数据库管理员和最终用户构成。其中DBMS是数据库系统的基础和核心

### 2. 数据库表列类型

#### 2.1 整数类型

![image-20210630201735014](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210630201735.png)

1. MySQL支持选择在该类型关键字后面的括号内指定整数值的**显示宽度**\(例如，INT\(4\)\)。显示宽度并不限制可以在列内保存的值的范围，也不限制超过列的指定宽度的值的显示
2. 主键自增：不使用序列，通过**auto\_increment**，要求是**整数类型**
3. 支持选择在该类型关键字后面的括号内指定整数值的显示宽度

![image-20210630201859642](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210630201859.png)

## 表的完整性约束

### 1. 非外键约束

| **约束条件** | **约束描述** |
| :--- | :--- |
| PRIMARY KEY | 主键约束，约束字段的值可唯一地标识对应的记录 |
| NOT NULL | 非空约束，约束字段的值不能为空 |
| UNIQUE | 唯一约束，约束字段的值是唯一的 |
| CHECK | 检查约束，限制某个字段的取值范围 |
| DEFAULT | 默认值约束，约束字段的默认值 |
| AUTO\_INCREMENT | 自动增加约束，约束字段的值自动递增 |
| FOREIGN KEY | 外键约束，约束表与表之间的关系 |

**实例一：建立一张用来存储学生信息的表**

```text
/*
建立一张用来存储学生信息的表
字段包含学号、姓名、性别，年龄、入学日期、班级，email等信息
约束：
建立一张用来存储学生信息的表
字段包含学号、姓名、性别，年龄、入学日期、班级，email等信息
【1】学号是主键 = 不能为空 +  唯一 ，主键的作用：可以通过主键查到唯一的一条记录【2】如果主键是整数类型，那么需要自增
【3】姓名不能为空
【4】Email唯一
【5】性别默认值是男
【6】性别只能是男女
【7】年龄只能在18-50之间
*/
-- 创建数据库表：
create table t_student(
        sno int(6) primary key auto_increment, -- 主键自增
        sname varchar(5) not null, 
        sex char(1) default '男' check(sex='男' || sex='女'),
        age int(3) check(age>=18 and age<=50),
        enterdate date,
        classname varchar(10),
        email varchar(15) unique -- 唯一
);
-- 查看数据：
select * from t_student;
​
```

![image-20210701085258650](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701085258.png)

* 添加数据：

  ```text
  -- 添加数据：
  --  1048 - Column 'sname' cannot be null 不能为null
  -- 3819 - Check constraint 't_student_chk_1' is violated. 违反检查约束
  insert into t_student values (1,'张三','男',21,'2023-9-1','java01班','zs@126.com');
  -- 1062 - Duplicate entry '1' for key 't_student.PRIMARY' 主键重复
  -- > 1062 - Duplicate entry 'ls@126.com' for key 't_student.email' 违反唯一约束
  insert into t_student values (2,'李四','男',21,'2023-9-1','java01班','ls@126.com');
  insert into t_student values (3,'露露','男',21,'2023-9-1','java01班','ls@126.com');
  -- 如果主键没有设定值，或者用null.default都可以完成主键自增的效果
  insert into t_student (sname,enterdate) values ('菲菲','2029-4-5');
  insert into t_student values (null,'小明','男',21,'2023-9-1','java01班','xm@126.com');
  insert into t_student values (default,'小刚','男',21,'2023-9-1','java01班','xg@126.com');
  -- 如果sql报错，可能主键就浪费了，后续插入的主键是不连号的，我们主键也不要求连号的
  insert into t_student values (null,'小明','男',21,'2023-9-1','java01班','oo@126.com');
  ```

  **常见错误：**

   --》 1048 - Column 'sname' cannot be null **不能为null**

   --》 819 - Check constraint 't\_student\_chk\_1' is violated. **违反检查约束**

   -- 》1062 - Duplicate（重复的） entry '1' for key 't\_student.PRIMARY' **主键重复**

   -- 》1062 - Duplicate entry '[ls@126.com](mailto:ls@126.com)' for key 't\_student.email' **违反唯一约束**

  * 使用自增的时候用**null和default**都是可以
  * 如果sql报错，可能**主键**就浪费了，后续插入的主键是不连号的，主键也不要求连号的

**- 列级约束和表级约束**

\(1\) **表级约束**：可以约束表中任意一个或多个字段。与列定义相互独立，不包含在列定义中；与定义用‘，’分隔；必须指出要约束的列的名称；

\(2\) **列级约束**：包含在列定义中，直接跟在该列的其它定义之后 ，用空格分隔；不必指定列名；

**实例二**

```text
-- 删除表：
drop table t_student;
-- 创建数据库表：
create table t_student(
        sno int(6) auto_increment, 
        sname varchar(5) not null, 
        sex char(1) default '男',
        age int(3),
        enterdate date,
        classname varchar(10),
        email varchar(15),
    -- 表级约束：
        constraint pk_stu primary key (sno),  -- pk_stu 主键约束的名字
        constraint ck_stu_sex check (sex = '男' || sex = '女'),
        constraint ck_stu_age check (age >= 18 and age <= 50),
        constraint uq_stu_email unique (email)
);
-- 添加数据：
insert into t_student values (1,'张三','男',21,'2023-9-1','java01班','zs@126.com');
-- > 3819 - Check constraint 'ck_stu_sex' is violated.
-- > 3819 - Check constraint 'ck_stu_age' is violated.
-- > 1062 - Duplicate entry 'zs@126.com' for key 't_student.uq_stu_email'
insert into t_student values (3,'李四','男',21,'2023-9-1','java01班','zs@126.com');
-- 查看数据：
select * from t_student;
```

**- 创建表之后添加约束**

**实例三**

```text
-- 删除表：
drop table t_student;
-- 创建数据库表：
create table t_student(
        sno int(6), 
        sname varchar(5) not null, 
        sex char(1) default '男',
        age int(3),
        enterdate date,
        classname varchar(10),
        email varchar(15)
);
-- > 1075 - Incorrect table definition; there can be only one auto column and it must be defined as a key
-- 错误的解决办法：就是auto_increment去掉
-- 在创建表以后添加约束：
alter table t_student add constraint pk_stu primary key (sno) ; -- 主键约束
alter table t_student modify sno int(6) auto_increment; -- 修改自增条件
alter table t_student add constraint ck_stu_sex check (sex = '男' || sex = '女');
alter table t_student add constraint ck_stu_age check (age >= 18 and age <= 50);
alter table t_student add constraint uq_stu_email unique (email);
-- 查看表结构：
desc t_student;
​
```

验证约束添加成功：

![image-20210701105405972](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701105406.png)

### 2. 外键约束

* 外键约束（FOREIGN KEY，缩写FK）是用来实现数据库表的参照完整性的。**外键约束可以使两张表紧密的结合起来，特别是针对修改或者删除的级联操作时，会保证数据的完整性。**
* 外键是指表中某个字段的值依赖于另一张表中某个字段的值，而被依赖的字段必须具有主键约束或者唯一约束。被依赖的表我们通常称之为父表或者主表，设置外键约束的表称为子表或者从表。
* 只有**表级约束**没有列级约束

**实例四**

主表（父表）：班级表 - 班级编号 - 主键

 从表（子表）：学生表 - 班级编号 - 外键

1. 创建父表---班级表：
2. * cno\(主键、自增\)；cname（不为空）；room；

     ```text
     -- 先删除表，先删除从表再删除父表
     drop table t_student;
     drop table t_class;
     -- 先创建父表：班级表：
     create table t_class(
             cno int(4) primary key auto_increment,
             cname varchar(10) not null,
             room char(4)
     )
     ```
3. 创建从表---学生表：
   * sno\(主键、自增\)；sname（不为空）；classno（取值参考t\_class表中的cno字段，不要求字段名字完全重复，但是类型长度定义 尽量要求相同。\);
   * **添加外键约束**：

     ```text
     -- 添加学生表，添加外键约束：
     create table t_student(
             sno int(6) primary key auto_increment, 
             sname varchar(5) not null, 
             classno int(4),-- 取值参考t_class表中的cno字段，不要求字段名字完全重复，但是类型长度定义 尽量要求相同。
         -- 外键约束：
             constraint fk_stu_classno foreign key (classno) references t_class (cno)
    
     );
     ```

   * **删除外键约束**

     ```text
     -- 删除外键约束
     alter table EMP
         drop foreign key FK_DEPTNO
     ```
4. 添加数据：
5. ```text
   -- 可以一次性添加多条记录：
   insert into t_class values (null,'java001','r803'),(null,'java002','r416'),(null,'大数据001','r103');
   insert into t_student values (null,'张三',1),(null,'李四',1),(null,'王五',2),(null,'朱六',3);
   ```
6. 查询班级表：

   ```text
   -- 查看班级表和学生表：
   select * from t_class;
   select * from t_student;
   ```

7. **添加外键的效果**：

   * 删除班级会出错

   ```text
   -- 尝试删除班级
   delete from t_class where cno = 1;
   ```

   ![image-20210701111051536](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701111051.png)

**外键策略：**

直接删除班级会错误，有时候必须删，可以加入外键策略

**策略一：no action 不允许操作**

```text
-- 策略1：no action 不允许操作
-- 通过操作sql来完成：
-- 先把班级2的学生对应的班级 改为null 
update t_student set classno = null where classno = 2;
-- 然后再删除班级2：
delete from t_class where cno = 2;
```

结果：

![image-20210701112307207](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701112307.png)

![image-20210701112344539](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701112344.png)

**策略二：使用cascade级联操作**

操作主表的时候影响从表的外键

* 先删除外键约束

  ```text
  -- 策略2：cascade 级联操作：操作主表的时候影响从表的外键信息：
  alter table t_student drop foreign key fk_stu_classno;
  ```

* 重新添加外键约束（增加一个级联操作）

  ```text
  -- 重新添加外键约束：
  alter table t_student add constraint fk_stu_classno foreign key (classno) references t_class (cno) on update cascade on delete cascade;
  ```

* 试着更新和删除操作：

  ```text
  -- 试试更新：
  update t_class set cno = 5 where cno = 3;
  -- 试试删除：
  delete from t_class where cno = 5;
  ```

  结果

  ![image-20210701113031631](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701113031.png)

**策略三：set null 置空操作**

* 先删除之前的外键约束

  ```text
  -- 先删除之前的外键约束：
  alter table t_student drop foreign key fk_stu_classno;
  ```

* 重新添加外键约束

  ```text
  -- 重新添加外键约束：
  alter table t_student add constraint fk_stu_classno foreign key (classno) references t_class (cno) on update set null on delete set null;
  ```

* 试着更新操作

  ```text
  -- 更新
  update t_class set cno = 5 whwere con = 1;
  ```

  结果：

  ![image-20210701142451962](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701142452.png)

  ![image-20210701142510409](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701142510.png)

注：策略2 级联操作 和 策略2 的 删除操作 可以混着使用

```text
alter table t_student add constraint fk_stu_classno foreign key (classno) references t_class (cno) on update cascade on delete set null ;
```

应用场合：

-- （1）朋友圈删除，点赞。留言都删除 -- 级联操作 -- （2）解散班级，对应的学生 置为班级为null就可以了，-- set null

## DQL查询操作\*

### 1. 常见表的准备

#### 部门表\(dept\)

```text
create table DEPT(  
  DEPTNO int(2) not null,  
  DNAME  VARCHAR(14),  
  LOC    VARCHAR(13)  
);  
alter table DEPT 
  add constraint PK_DEPT primary key (DEPTNO); 
```

#### 员工表（emp\)

```text
create table EMP  
(  
  EMPNO    int(4) primary key,  
  ENAME    VARCHAR(10),  
  JOB      VARCHAR(9),  
  MGR      int(4),  
  HIREDATE DATE,  
  SAL      double(7,2),  
  COMM     double(7,2),  
  DEPTNO   int(2)  
);  
alter table EMP  
  add constraint FK_DEPTNO foreign key (DEPTNO)  
  references DEPT (DEPTNO);  
```

#### 薪资登记表\(salgrade\)

```text
create table SALGRADE  
(  
  GRADE int primary key,  
  LOSAL double(7,2),  
  HISAL double(7,2)  
);  
```

#### 奖金表\(bonus\)

```text
create table BONUS  
(  
  ENAME VARCHAR(10),  
  JOB   VARCHAR(9),  
  SAL   double(7,2),  
  COMM  double(7,2)  
);  
```

#### 操作

```text
-- DEPT
insert into DEPT (DEPTNO, DNAME, LOC)  
values (10, 'ACCOUNTING', 'NEW YORK');  
insert into DEPT (DEPTNO, DNAME, LOC)  
values (20, 'RESEARCH', 'DALLAS');  
insert into DEPT (DEPTNO, DNAME, LOC)  
values (30, 'SALES', 'CHICAGO');  
insert into DEPT (DEPTNO, DNAME, LOC)  
values (40, 'OPERATIONS', 'BOSTON');  
-- EMP
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7369, 'SMITH', 'CLERK', 7902, '1980-12-17', 800, null, 20);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7499, 'ALLEN', 'SALESMAN', 7698, '1981-02-20', 1600, 300, 30);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7521, 'WARD', 'SALESMAN', 7698, '1981-02-22', 1250, 500, 30);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7566, 'JONES', 'MANAGER', 7839, '1981-04-02', 2975, null, 20);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7654, 'MARTIN', 'SALESMAN', 7698, '1981-09-28', 1250, 1400, 30);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7698, 'BLAKE', 'MANAGER', 7839, '1981-05-01', 2850, null, 30);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7782, 'CLARK', 'MANAGER', 7839, '1981-06-09', 2450, null, 10);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7788, 'SCOTT', 'ANALYST', 7566, '1987-04-19', 3000, null, 20);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7839, 'KING', 'PRESIDENT', null, '1981-11-17', 5000, null, 10);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7844, 'TURNER', 'SALESMAN', 7698, '1981-09-08', 1500, 0, 30);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7876, 'ADAMS', 'CLERK', 7788, '1987-05-23', 1100, null, 20);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7900, 'JAMES', 'CLERK', 7698, '1981-12-03', 950, null, 30);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7902, 'FORD', 'ANALYST', 7566, '1981-12-03', 3000, null, 20);  
insert into EMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)  
values (7934, 'MILLER', 'CLERK', 7782, '1982-01-23', 1300, null, 10);  
-- SALGRADE
insert into SALGRADE (GRADE, LOSAL, HISAL)  
values (1, 700, 1200);  
insert into SALGRADE (GRADE, LOSAL, HISAL)  
values (2, 1201, 1400);  
insert into SALGRADE (GRADE, LOSAL, HISAL)  
values (3, 1401, 2000);  
insert into SALGRADE (GRADE, LOSAL, HISAL)  
values (4, 2001, 3000);  
insert into SALGRADE (GRADE, LOSAL, HISAL)  
values (5, 3001, 9999);  

-- 查看表：
select * from dept; 
-- 部门表：dept:department 部分 ，loc - location 位置
select * from emp;
-- 员工表：emp:employee 员工   ,mgr :manager上级领导编号，hiredate 入职日期  firedate 解雇日期 ，common：补助
-- deptno 外键 参考  dept - deptno字段
-- mgr 外键  参考  自身表emp - empno  产生了自关联
select * from salgrade;
-- losal - lowsal
-- hisal - highsal
select * from bonus;
```

#### 结果\(表格形式\)

![image-20210701143713377](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701143713.png)

![image-20210701143726512](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701143726.png)

![image-20210701143737874](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701143737.png)

![image-20210701143749663](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701143749.png)

### 2. 单表查询

#### 2.1 简单的SQL查询

```text
-- 对emp表查询：
select * from emp; -- *代表所有数据
-- 显示部分列：
select empno,ename,sal from emp;
-- 显示部分行：where子句
select * from emp where sal > 2000;
-- 显示部分列，部分行：
select empno,ename,job,mgr from emp where sal > 2000;
-- 起别名：
select empno 员工编号,ename 姓名,sal 工资 from emp; -- as 省略，''或者""省略了
-- as alias 别名
select empno as 员工编号,ename as 姓名,sal as 工资 from emp;
select empno as '员工编号',ename as "姓名",sal as 工资 from emp;
-- > 1064 - You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '编号,ename as "姓 名",sal as 工资 from emp' at line 1
-- 错误原因：在别名中有特殊符号的时候，''或者""不可以省略不写
select empno as 员工 编号,ename as "姓 名",sal as 工资 from emp;
-- 算术运算符：
select empno,ename,sal,sal+1000 as '涨薪后',deptno from emp where sal < 2500;
select empno,ename,sal,comm,sal+comm from emp;  -- ？？？后面再说
-- 去重操作：
select job from emp;
select distinct job from emp;
select job,deptno from emp;
select distinct job,deptno from emp; -- 对后面的所有列组合 去重 ，而不是单独的某一列去重
-- 排序：
select * from emp order by sal; -- 默认情况下是按照升序排列的
select * from emp order by sal asc; -- asc 升序，可以默认不写
select * from emp order by sal desc; -- desc 降序
select * from emp order by sal asc ,deptno desc; -- 在工资升序的情况下，deptno按照降序排列
```

#### 2.2 where子句

将过滤条件放在where子句的后面，可以筛选/过滤出我们想要的符合条件的数据

* 加关系运算符

  ```text
  -- where 子句 + 关系运算符
  select * from emp where deptno = 10;
  select * from emp where deptno > 10;
  select * from emp where deptno >= 10;
  select * from emp where deptno < 10;
  select * from emp where deptno <= 10;
  select * from emp where deptno <> 10;
  select * from emp where deptno != 10;
  select * from emp where job = 'CLERK'; 
  select * from emp where job = 'clerk'; -- 默认情况下不区分大小写 
  select * from emp where binary job = 'clerk'; -- binary区分大小写
  select * from emp where hiredate < '1981-12-25';
  ```

* 加逻辑运算符

  ```text
  -- where 子句 + 逻辑运算符：and 
  select * from emp where sal > 1500 and sal < 3000;  -- (1500,3000)
  select * from emp where sal > 1500 && sal < 3000; 
  select * from emp where sal > 1500 and sal < 3000 order by sal;
  select * from emp where sal between 1500 and 3000; -- [1500,3000]
  -- where 子句 + 逻辑运算符：or
  select * from emp where deptno = 10 or deptno = 20;
  select * from emp where deptno = 10 || deptno = 20;
  select * from emp where deptno in (10,20);
  select * from emp where job in ('MANAGER','CLERK','ANALYST');
  ```

* 加模糊查询

  ```text
  -- where子句 + 模糊查询：
  -- 查询名字中带A的员工  -- %代表任意多个字符 0,1,2，.....
  select * from emp where ename like '%A%' ;
  -- -任意一个字符
  select * from emp where ename like '__A%' ;
  ```

* 关于null的判断

  ```text
  -- 关于null的判断：
  select * from emp where comm is null;
  select * from emp where comm is not null;
  ```

* 小括号的使用：因为不同运算符的优先级别不同，加括号为了可读性

  ```text
  -- 小括号的使用  ：因为不同的运算符的优先级别不同，加括号为了可读性
  select * from emp where job = 'SALESMAN' or job = 'CLERK' and sal >=1500; -- 先and再or  and > or
  select * from emp where job = 'SALESMAN' or (job = 'CLERK' and sal >=1500); 
  select * from emp where (job = 'SALESMAN' or job = 'CLERK') and sal >=1500;
  ```

#### 2.3 函数

_**除了多行函数（max,min,count,sum,avg），都是单行函数**_

**- 单行函数**

**1. 字符串函数**

| **函数** | **描述** |
| :--- | :--- |
| CONCAT\(str1, str2, ···, strn\) | 将str1、str2···strn拼接成一个新的字符串 |
| INSERT\(str, index, n, newstr\) | 将字符串str从第index位置开始的n个字符替换成字符串newstr |
| LENGTH\(str\) | 获取字符串str的长度 |
| LOWER\(str\) | 将字符串str中的每个字符转换为小写 |
| UPPER\(str\) | 将字符串str中的每个字符转换为大写 |
| LEFT\(str, n\) | 获取字符串str最左边的n个字符 |
| RIGHT\(str, n\) | 获取字符串str最右边的n个字符 |
| LPAD\(str, n, pad\) | 使用字符串pad在str的最左边进行填充，直到长度为n个字符为止 |
| RPAD\(str, n, pad\) | 使用字符串pad在str的最右边进行填充，直到长度为n个字符为止 |
| LTRIM\(str\) | 去除字符串str左侧的空格 |
| RTRIM\(str\) | 去除字符串str右侧的空格 |
| TRIM\(str\) | 去除字符串str左右两侧的空格 |
| REPLACE\(str,oldstr,newstr\) | 用字符串newstr替换字符串str中所有的子字符串oldstr |
| REVERSE\(str\) | 将字符串str中的字符逆序 |
| STRCMP\(str1, str2\) | 比较字符串str1和str2的大小 |
| SUBSTRING\(str,index,n\) | 获取从字符串str的index位置开始的n个字符 |

```text
-- 1.字符串函数
select ename,length(ename),substring(ename,2,3) from emp;
-- substring字符串截取，2:从字符下标为2开始，3：截取长度3    （下标从1开始）
```

显示结果：

![image-20210630210115889](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210630210116.png)

**2. 数值函数**

| **函数** | **描述** |
| :--- | :--- |
| ABS\(num\) | 返回num的绝对值 |
| CEIL\(num\) | 返回大于num的最小整数（向上取整） |
| FLOOR\(num\) | 返回小于num的最大整数（向下取整） |
| MOD\(num1, num2\) | 返回num1/num2的余数（取模） |
| PI\(\) | 返回圆周率的值 |
| POW\(num,n\)/POWER\(num, n\) | 返回num的n次方 |
| RAND\(num\) | 返回0~1之间的随机数 |
| ROUND\(num, n\) | 返回x四舍五入后的值，该值保留到小数点后n位 |
| TRUNCATE\(num, n\) | 返回num被舍去至小数点后n位的值 |

```text
-- 2.数值函数
select abs(-5),ceil(5.3),floor(5.9),round(3.14) from dual; -- dual实际就是一个伪表
select abs(-5) 绝对值,ceil(5.3) 向上取整,floor(5.9) 向下取整,round(3.14) 四舍五入;  
-- 如果没有where条件的话，from dual可以省略不写
select ceil(sal) from emp;
select 10/3,10%3,mod(10,3) ;
```

显示结果：

![image-20210630210456370](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210630210456.png)

**3. 日期与时间函数**

| **函数** | **描述** |
| :--- | :--- |
| CURDATE\(\) | 返回当前日期 |
| CURTIME\(\) | 返回当前时间 |
| NOW\(\) | 返回当前日期和时间 |
| SYSDATE\(\) | 返回该函数执行时的日期和时间 |
| DAYOFYEAR\(date\) | 返回日期date为一年中的第几天 |
| WEEK\(date\)/WEEKOFYEAR\(date\) | 返回日期date为一年中的第几周 |
| DATE\_FORMAT\(date, format\) | 返回按字符串format格式化后的日期date |
| DATE\_ADD\(date, INTERVAL expr unit\) /ADDDATE\(date, INTERVAL expr unit\) | 返回date加上一个时间间隔后的新时间值 |
| DATE\_SUB\(date, INTERVAL expr unit\) /SUBDATE\(date, INTERVAL expr unit\) | 返回date减去一个时间间隔后的新时间值 |
| DATEDIFF\(date1, date2\) | 返回起始日期date1与结束日期date2之间的间隔天数 |

```text
-- 3.日期与时间函数 
select * from emp;
select curdate(),curtime() ; -- curdate()年月日 curtime()时分秒
select now(),sysdate(),sleep(3),now(),sysdate() from dual; -- now(),sysdate() 年月日时分秒
insert into emp values (9999,'lili','SALASMAN',7698,now(),1000,null,30);
-- now()可以表示年月日时分秒，但是插入数据的时候还是要参照表的结构的
desc emp;
```

![image-20210630223758495](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210630223758.png)

![image-20210630223819827](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210630223819.png)

![image-20210630223829883](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210630223829.png)

**4. 流程函数**

![image-20210630224323874](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210630224323.png)

```text
-- 4.流程函数
-- if相关
select empno,ename,sal,if(sal>=2500,'高薪','底薪') as '薪资等级' from emp; -- if-else 双分支结构
select empno,ename,sal,comm,sal+ifnull(comm,0) from emp; -- 如果comm是null，那么取值为0 -- 单分支
select nullif(1,1),nullif(1,2) from dual; --  如果value1等于value2，则返回null，否则返回value1  
-- case相关：
-- case等值判断
select empno,ename,job,
case job 
 when 'CLERK' then '店员'
 when 'SALESMAN'  then '销售'
 when 'MANAGER' then '经理'
 else '其他'
end '岗位',
sal from emp;
-- case区间判断:
select empno,ename,sal,
case 
 when sal<=1000 then 'A'
 when sal<=2000 then 'B'
 when sal<=3000 then 'C'
 else 'D'
end '工资等级',
deptno from emp;
from emp;
```

**5. JSON函数**

![image-20210630224430595](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210630224430.png)

**6. 其他函数**

![image-20210630224453894](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210630224453.png)

```text
 -- 6.其他函数
select database(),user(),version() from dual;
```

**- 多行函数**

对一组数据进行运算，针对一组数据（多行记录）只返回一个结果，也称**分组函数**

![image-20210630224611608](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210630224611.png)

```text

-- 多行函数：
select max(sal),min(sal),count(sal),sum(sal),sum(sal)/count(sal),avg(sal) from emp;
select * from emp;
-- 多行函数自动忽略null值
select max(comm),min(comm),count(comm),sum(comm),sum(comm)/count(comm),avg(comm) from emp;
-- max(),min(),count()针对所有类型   sum(),avg() 只针对数值型类型有效
select max(ename),min(ename),count(ename),sum(ename),avg(ename) from emp;


-- count --计数   
-- 统计表的记录数：方式1：
select * from emp;
select count(ename) from emp;
select count(*) from emp;
-- 统计表的记录数：方式2
select 1 from dual;
select 1 from emp;
select count(1) from emp;
```

#### 2.4 分组与筛选

**group by 分组**

```text
select * from emp;
-- 统计各个部门的平均工资 
select deptno,avg(sal) from emp; -- 字段和多行函数不可以同时使用
select deptno,avg(sal) from emp group by deptno; -- 字段和多行函数不可以同时使用,除非这个字段属于分组
select deptno,avg(sal) from emp group by deptno order by deptno desc;
-- 统计各个岗位的平均工资 统计各个岗位的
select job,avg(sal) from emp group by job;
select job,lower(job),avg(sal) from emp group by job;
```

**having 分组后筛选**

```text
-- 统计各个部门的平均工资 ,只显示平均工资2000以上的  - 分组以后进行二次筛选 having
select deptno,avg(sal) from emp group by deptno having avg(sal) > 2000;
select deptno,avg(sal) 平均工资 from emp group by deptno having 平均工资 > 2000;
select deptno,avg(sal) 平均工资 from emp group by deptno having 平均工资 > 2000 order by deptno desc;
```

where在分组前进行过滤的，having在分组后进行后滤。

对比实例：

```text
-- 对比实例：统计各个岗位的平均工资,除了MANAGER
-- 方法1：
select job,avg(sal) from emp where job != 'MANAGER' group by job;
-- 方法2：
select job,avg(sal) from emp group by job having job != 'MANAGER' ;
-- where在分组前进行过滤的，having在分组后进行后滤。
```

#### 总结

**\[1\] select语句总结**

```text
select column, group_function(column) 
from table 
[where condition] 
[group by  group_by_expression] 
[having group_condition] 
[order by column]; 
```

注意：顺序固定，不可以改变顺序

**\[2\] select语句的执行顺序**

from --- where --- group by --- select --- having --- order by

**\[3\] 单表查询练习**

```text
-- 单表查询练习：
-- 列出工资最小值小于2000的职位
select job,min(sal)
from emp
group by job
having min(sal) < 2000;
-- 列出平均工资大于1200元的部门和工作搭配组合
select deptno,job,avg(sal)
from emp
group by deptno,job
having avg(sal) > 1200
order by avg(sal);
-- 统计[人数小于4的]部门的平均工资。 
select deptno,count(ename),avg(sal) -- 这里可用 count(1)
from emp
group by deptno
having count(ename) < 4;

-- 统计各部门的最高工资，排除最高工资小于3000的部门。
select deptno,max(sal)
from emp
group by deptno
having max(sal) >= 3000;
```

### 3. 多表查询

#### 3.1 交叉连接 cross join

```text
select *
from emp
cross join dept;
```

#### 3.2 自然连接 natural

```text
select * 
from emp
natural join dept;
```

优点：自动匹配所有的同名列 ，同名列只显示一次，简单

缺点：查询字段的时候，没有指定字段对应的数据库表，效率低

**解决——指定表名**

```text
select emp.empno,emp.enaem,emp.sal,dept.dname,dept.loc,dept.deptno
from emp
natural join dept;
```

缺点：表名太长

**解决——起表名**

```text
select e.empno,e.ename,e.sal,d.dname,d.loc,d.deptno
from emp e
natural join dept d;
```

缺点：会自动匹配所有表中同名的字段。有时候我们只需要部分相同的字段

#### 3.3 内连接 inner join

```text
select * 
from emp e
inner join dept d 
using(deptno); -- 同名字段才有效
```

缺点：连接的表字段不同时using无法使用。

解决——**使用on子句**

```text
select *
from emp e
inner join dept d -- 使用on的时候inner可以省略
on e.deptno = d.deptno;
```

#### 3.4 外连接 outer join

* 内连接只显示匹配的数据，例如一个部门没有员工就不会显示
* **外连接可以显示不匹配的数据**
* outer都可以省略的

**左外连接**

左边的那个表的信息，即使不匹配也可以查看出结果

```text
select * 
from emp
left outer join dept d
on e.deptno = d.deptno
```

结果

![image-20210701170246009](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701170246.png)

**右外连接**

右边的那个表的信息，即使不匹配也可以查看出结果

```text
select *
from emp e
right outer join dept d
on e.deptno = d.deptno;
```

结果

![image-20210701163643122](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701163643.png)

**全外连接**

这个语法在mysql里面不支持，oracle才支持

为了代替 可以取左外连接和右外连接的并集

**并集 union**

```text
select * 
from emp
left outer join dept d
on e.deptno = d.deptno
union -- 并集，不去重，效率低
select *
from emp e
right outer join dept d
on e.deptno = d.deptno;
```

结果

![image-20210701170831388](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701170831.png)

**union all**

```text
select * 
from emp
left outer join dept d
on e.deptno = d.deptno
union  all -- 不去重的并集， 效率高
select *
from emp e
right outer join dept d
on e.deptno = d.deptno;
```

结果

![image-20210701171121236](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701171121.png)

#### 3.5 三表查询

案例：查询员工的编号、姓名、薪水、部门编号、部门名称、薪水等级

```text
select * from emp;
select * from dept;
select * from salgrade；

select *
from emp e
right outer join dept d
on e.deptno = d.deptno;
-- 可以看做就是上面的结果是一张表，然后再和第三张表进行连接
innner join salgrade s
on e.sal between salgrade
```

结果

![image-20210701184545238](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210701184545.png)

#### 3.6 自连接查询

相当于把本身表复制一份，本质与前面的连接没有区别

```text
-- 查询员工的编号、姓名、上级编号,上级的姓名
select * from emp;
select e1.empno 员工编号,e1.ename 员工姓名,e1.mgr 领导编号,e2.ename 员工领导姓名
from emp e1
inner join emp e2
on e1.mgr = e2.empno;
-- 左外连接：
select e1.empno 员工编号,e1.ename 员工姓名,e1.mgr 领导编号,e2.ename 员工领导姓名
from emp e1
left outer join emp e2
on e1.mgr = e2.empno;
```

结果

![image-20210702094750390](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210702094750.png)

#### 补充：92语法的多表查询

```text
-- 查询员工的编号，员工姓名，薪水，员工部门编号，部门名称：
select e.empno,e.ename,e.sal,e.deptno,d.dname
from emp e,dept d
-- 相当于99语法中的cross join ,出现笛卡尔积，没有意义
select e.empno,e.ename,e.sal,e.deptno,d.dname
from emp e,dept d
where e.deptno = d.deptno;
-- 相当于99语法中的natural join 
-- 查询员工的编号，员工姓名，薪水，员工部门编号，部门名称，查询出工资大于2000的员工
select e.empno,e.ename,e.sal,e.deptno,d.dname
from emp e,dept d
where e.deptno = d.deptno and e.sal > 2000;
-- 查询员工的名字，岗位，上级编号，上级名称（自连接）：
select e1.ename,e1.job,e1.mgr ,e2.ename 
from emp e1,emp e2
where e1.mgr = e2.empno;
-- 查询员工的编号、姓名、薪水、部门编号、部门名称、薪水等级
select e.empno,e.ename,e.sal,e.deptno,d.dname,s.grade 
from emp e,dept d,salgrade s
where e.deptno = d.deptno and e.sal >= s.losal and e.sal <= s.hisal;
-- 总结：
-- 1.92语法麻烦 
-- 2.92语法中 表的连接条件 和  筛选条件  是放在一起的没有分开
-- 3.99语法中提供了更多的查询连接类型：cross,natural,inner,outer 

```

### 4. 不相关子查询

#### 引入

要查询所有比“CLARK”工资高的员工的信息

需要两个命令而且第二个命令依托于第一个命令，会相互影响

```text
-- 命令一：查询clark的工资
select sal from emp where ename = clark; -- 2450
-- 命令二：查询所有比其工资高的员工信息
select * from emp where sal > 2450;
```

子查询可以通过一个命令解决问题 !

#### 4.1 单行子查询

```text
-- 将上面两个命令合并
-- 查询工资高于平均工资的雇员的名字和工资
select ename,sal
from emp
where sal > (select sal from emp where sal > avg(sal));
 
-- 查询和CLARK同一部门且比他工资低的雇员的名字和工资
select ename,sal
from emp
where deptno = (select deptno from emp where ename='CLARK') 
	  and 
	  sal < (select sal from emp where ename='CLARK' );

-- 查询职务和SCOTT相同，比SCOTT雇佣时间早的雇员信息
select * 
from emp
where job = (select job from emp where ename='SCOTT')
	  and
	  hiredate < (select hiredate from emp where ename='SCOTT');
```

#### 4.2 多行子查询

子查询查出来的记录为多条：

**实例一**

* 查询部门20中职务同部门10的雇员一样的雇员信息。

```text

-- 查询雇员信息
select * from emp;
-- 查询部门20中的雇员信息
select * from emp where deptno = 20;-- CLERK,MANAGER,ANALYST
-- 部门10的雇员的职务：
select job from emp where deptno = 10; -- MANAGER,PRESIDENT,CLERK
```

如果使用单行子查询，就会报错 -&gt;Subquery returns more than 1 row

```text
select * from emp 
where deptno = 20 
and job in (select job from emp where deptno = 10)
-- > Subquery returns more than 1 row
```

多行子查询可以解决

```text
select * from emp 
where deptno = 20 
and job = any(select job from emp where deptno = 10) -- any
```

**实例二**

* 查询工资比所有的“SALESMAN”都高的雇员的编号、名字和工资

  ```text
  -- 查询雇员的编号、名字和工资
  select empno,ename,sal from emp
  -- “SALESMAN”的工资：
  select sal from emp where job = 'SALESMAN'
  -- 查询工资比所有的“SALESMAN”都高的雇员的编号、名字和工资。
  -- 多行子查询：
  select empno,ename,sal 
  from emp 
  where sal > all(select sal from emp where job = 'SALESMAN');
  ```

  ```text
  -- 单行子查询：
  select empno,ename,sal 
  from emp 
  where sal > (select max(sal) from emp where job = 'SALESMAN');
  ```

**实例三**

* 查询工资低于任意一个“CLERK”的工资的雇员信息。

```text
-- 查询雇员信息
select * from emp;
-- 查询工资低于任意一个“CLERK”的工资的雇员信息
select * 
from emp
where sal < any(select sal from emp where job = 'CLERK')
and job != 'CLERK'
```

```text
-- 单行子查询：
select * 
from emp
where sal < (select max(sal) from emp where job = 'CLERK')
and job != 'CLERK'
```

### 5. 相关子查询

#### 引入

* 不相关的子查询引入： 不相关的子查询：子查询可以独立运行，先运行子查询，再运行外查询。 相关子查询：子查询不可以独立运行，并且先运行外查询，再运行子查询
* 不相关的子查询优缺点： 好处：简单 功能强大（一些使用不相关子查询不能实现或者实现繁琐的子查询，可以使用相关子查询实现） 缺点：稍难理解

#### 实例

1. 查询各个部门最高工资的员工

   * 不相关子查询

   ```text
   select * from emp where deptno = 10 and sal = (select max(sal) from emp where deptnp = 10)
   union
   select * from emp where deptno = 20 and sal = (select max(sal) from emp where deptno = 20)
   union
   select * from emp where deptno = 30 and sal = (select max(sal) from emp where deptno = 30);
   ```

   太复杂了😒

   * 相关子查询

   ```text
   select * from emp e where sal = (select max(sal) from emp where deptno = e.deptno)
   ```

   简单多啦😄

2. 查询工高于所在岗位的平均工资的那些员工

   * 不相关子查询

   ```text
   select * from emp where job = '' and sal >= (select avg(sal) from emp where job = '')
   union ....
   ```

   * 相关子查询

   ```text
   select * from emp e where sal >= (select avg(sal) from emp where job = e.job);
   ```

## 数据库对象

### 1. 事务

**事务（Transaction）**是用来维护数据库完整性的，它能够保证一系列的MySQL操作要么全部执行，要么全不执行。

eg1. 转账操作：A账户要转账给B账户，那么A账户上减少的钱数和B账户上增加的钱数必须一致，也就是说A账户的转出操作和B账户的转入操作要么全部执行，要么全不执行；如果其中一个操作出现异常而没有执行的话，就会导致账户A和账户B的转入转出金额不一致的情况，为而事实上这种情况是不允许发生的，所以为了防止这种情况的发生，需要使用事务处理。 eg2. 在淘宝购物下订单的时候，商家库存要减少，订单增加记录，付款我的账号少100元...操作要么全部执行，要么全不执行

**概念**

事务（Transaction）指的是一个操作序列，该操作序列中的多个操作要么都做，要么都不做，是一个不可分割的工作单位，是数据库环境中的逻辑工作单位，由DBMS（数据库管理系统）中的事务管理子系统负责事务的处理。 目前常用的存储引擎有InnoDB（MySQL5.5以后默认的存储引擎）和MyISAM（MySQL5.5之前默认的存储引擎），其中InnoDB支持事务处理机制，而MyISAM不支持。

**特性**

![image-20210703110320474](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210703110321.png)

事务处理可以确保除非事务性序列内的所有操作都成功完成，否则不会永久更新面向数据的资源。通过将一组相关操作组合为一个要么全部成功要么全部失败的序列，可以简化错误恢复并使应用程序更加可靠。 但并不是所有的操作序列都可以称为事务，这是因为一个操作序列要成为事务，必须满足事务的原子性（Atomicity）、一致性（Consistency）、隔离性（Isolation）和持久性（Durability）。这四个特性简称为**ACID特性。**

1. **原子性** 原子是自然界最小的颗粒，具有不可再分的特性。事务中的所有操作可以看做一个原子，事务是应用中不可再分的最小的逻辑执行体。 使用事务对数据进行修改的操作序列，要么全部执行，要么全不执行。通常，某个事务中的操作都具有共同的目标，并且是相互依赖的。如果数据库系统只执行这些操作中的一部分，则可能会破坏事务的总体目标，而原子性消除了系统只处理部分操作的可能性。
2. **一致性** 一致性是指事务执行的结果必须使数据库从一个一致性状态，变到另一个一致性状态。当数据库中只包含事务成功提交的结果时，数据库处于一致性状态。一致性是通过原子性来保证的。 例如：在转账时，只有保证转出和转入的金额一致才能构成事务。也就是说事务发生前和发生后，数据的总额依然匹配。
3. **隔离性** 隔离性是指各个事务的执行互不干扰，任意一个事务的内部操作对其他并发的事务，都是隔离的。也就是说：并发执行的事务之间既不能看到对方的中间状态，也不能相互影响。 例如：在转账时，只有当A账户中的转出和B账户中转入操作都执行成功后才能看到A账户中的金额减少以及B账户中的金额增多。并且其他的事务对于转账操作的事务是不能产生任何影响的。
4. **持久性** 持久性指事务一旦提交，对数据所做的任何改变，都要记录到永久存储器中，通常是保存进物理数据库，即使数据库出现故障，提交的数据也应该能够恢复。但如果是由于外部原因导致的数据库故障，如硬盘被损坏，那么之前提交的数据则有可能会丢失。

**实例：使用事务保证转账安全**

```text
-- 创建账户表：id\uname\balance
create table account(
        id int primary key auto_increment,
        uname varchar(10) not null,
        balance double
);
-- 查看账户表：
select * from account;
-- 在表中插入数据：
insert into account values (null,'丽丽',2000),(null,'小刚',2000);
-- 丽丽给小刚 转200元：
update account set balance = balance - 200 where id = 1;
update account set balance = balance + 200 where id = 2;
-- 默认一个DML语句是一个事务，所以上面的操作执行了2个事务。
update account set balance = balance - 200 where id = 1;
update account set balance = balance2 + 200 where id = 2;
```

**必须让上面的两个操作控制在一个事务中**

```text
-- 手动开启事务：
start transaction;
-- 操作：
update account set balance = balance - 200 where id = 1;
update account set balance = balance + 200 where id = 2;
-- 手动回滚：刚才执行的操作全部取消：
rollback;
-- 手动提交：
commit;
```

在**回滚和提交**之前，数据库中的数据都是操作的缓存中的数据，而不是数据库的真实数据

#### 事务并发问题

**脏读（Dirty read）**

当一个事务正在访问数据并且对数据进行了修改，而这种修改还没有提交到数据库中，这时另外一个事务也访问了这个数据，然后使用了这个数据。因为这个数据是还没有提交的数据，那么另外一个事务读到的这个数据是“脏数据”，依据“脏数据”所做的操作可能是不正确的。

![image-20210703111005503](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210703111005.png)

**不可重复读 （Unrepeatableread）**

指在一个事务内多次读同一数据。在这个事务还没有结束时，另一个事务也访问该数据。那么，在第一个事务中的两次读数据之间，由于第二个事务的修改导致第一个事务两次读取的数据可能不太一样。这就发生了在一个事务内两次读到的数据是不一样的情况，因此称为不可重复读。

![image-20210703111018492](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210703111018.png)

**幻读 （Phantom read）**

幻读与不可重复读类似。它发生在一个事务（T1）读取了几行数据，接着另一个并发事务（T2）插入了一些数据时。在随后的查询中，第一个事务（T1）就会发现多了一些原本不存在的记录，就好像发生了幻觉一样，所以称为幻读。

![image-20210703111036154](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210703111036.png)

**不可重复度和幻读区别：**

不可重复读的重点是修改，幻读的重点在于新增或者删除。解决不可重复读的问题只需锁住满足条件的行，解决幻读需要锁表 。

例1（同样的条件, 你读取过的数据, 再次读取出来发现值不一样了 ）：事务1中的A先生读取自己的工资为 1000的操作还没完成，事务2中的B先生就修改了A的工资为2000，导 致A再读自己的工资时工资变为 2000；这就是不可重复读。

例2（同样的条件, 第1次和第2次读出来的记录数不一样 ）：假某工资单表中工资大于3000的有4人，事务1读取了所有工资大于3000的人，共查到4条记录，这时事务2 又插入了一条工资大于3000的记录，事务1再次读取时查到的记录就变为了5条，这样就导致了幻读

#### 事务隔离级别

 事务的隔离级别用于决定如何控制并发用户读写数据的操作。数据库是允许多用户并发访问的，如果多个用户同时开启事务并对同一数据进行读写操作的话，有可能会出现脏读、不可重复读和幻读问题，所以MySQL中提供了四种隔离级别来解决上述问题。

 从低到高依次为**READ UNCOMMITTED、READ COMMITTED、REPEATABLE READ以及SERIALIZABLE**，隔离级别越低，越能支持高并发的数据库操作。

![image-20210703142738514](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210703142738.png)

```text

-- 查看默认的事务隔离级别  MySQL默认的是repeatable read  
select @@transaction_isolation;  
-- 设置事务的隔离级别   （设置当前会话的隔离级别）
set session transaction isolation level read uncommitted;  
set session transaction isolation level read committed;  
set session transaction isolation level repeatable read;  -- 默认的
set session transaction isolation level serializable;  
start transaction ;
select * from account where id = 1;
```

**实例**

（必须是两个事务都开始以后执行的操作才会影响）

* 在read uncommitted的情况下，开始两个事务：

  * 事务一

  ![image-20210703145526739](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210703152258.png)

  * 事务二

  ![image-20210703145443742](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210703152314.png)

* 事务一查询操作：

  ![image-20210703152514123](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210703152514.png)

* 事务二更新操作，且没提交

  ![image-20210703152552191](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210703152552.png)

* 事务一再次查询：

  ![image-20210703152939898](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210703152939.png)

  读到了在脏数据

* 如果设置为

  ```text
  set session transaction isolation level read committed;
  ```

  事务二就不会读到脏数据。

### 2. 视图

**概念**

视图（view）是一个从单张或多张基础数据表或其他视图中构建出来的虚拟表。同基础表一样，视图中也包含了一系列带有名称的列和行数据，但是数据库中只是存放视图的定义，也就是动态检索数据的查询语句，而并不存放视图中的数据，这些数据依旧存放于构建视图的基础表中，只有当用户使用视图是才去数据库请求相对应的数据，即视图中的数据是在引用视图时动态生成的。因此视图中的数据发生了变化，视图中相应的数据也会跟着改变。

PS：视图本质上就是：一个查询语句，是一个虚拟的表，不存在的表，你查看视图，其实就是查看视图对应的sql语句。

**优点**

简化用户操作：视图可以使用户将注意力集中在所关心地数据上，而不需要关心数据表的结构、与其他表的关联条件以及查询条件等。

对机密数据提供安全保护：有了视图，就可以在设计数据库应用系统时，对不同的用户定义不同的视图，避免机密数据（如，敏感字段“salary”）出现在不应该看到这些数据的用户视图上。这样视图就自动提供了对机密数据的安全保护功能

**实例一：单表视图**

* 创建/替换单表视图：

  ```text
  -- 创建/替换单表视图：
  create or replace view myview01
  as
  select empno,ename,job,deptno 
  from emp
  where deptno = 20
  ```

* 查看视图：

  ```text
  -- 查看视图：
  select * from myview01;
  ```

* 向视图中插入满足条件的数据

  ```text
  -- 在视图中插入数据：
  insert into myview01 (empno,ename,job,deptno) values (9999,'lili','CLERK',20);
  ```

  查看视图：

  ![image-20210703161609044](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210703161609.png)

* 向视图中插入不满足条件的数据

  ```text
  insert into myview01 (empno,ename,job,deptno) values (8888,'nana','CLERK',30);
  ```

  查看原来的表，发现成功插进去了

  ![image-20210703161731377](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210703161731.png)

* 解决上面问题：

  需要加上**check option**

  ```text
  -- 创建/替换单表视图：
  create or replace view myview01
  as
  select empno,ename,job,deptno 
  from emp
  where deptno = 20
  with check option;
  ```

  这样不满足条件的数据就不会轻易被加进去：

  ![image-20210703162005163](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210703162005.png)

**实例二：多表视图**

```text
-- 创建/替换多表视图：
create or replace view myview02
as 
select e.empno,e.ename,e.sal,d.deptno,d.dname
from emp e
join dept d
on e.deptno = d.deptno
where sal > 2000 ;
select * from myview02;
```

**实例三：统计视图**

```text
-- 创建统计视图：
create or replace view myview03
as
select e.deptno,d.dname,avg(sal),min(sal),count(1)
from emp e
join dept d
using(deptno)
group by e.deptno ;
select * from myview03;
```

**实例四：基于视图的视图**

```text
-- 创建基于视图的视图：
create or replace view myview04
as
select * from myview03 where deptno = 20;
select * from myview04;
```

### 3. 存储过程

**概念**

* 通过前面章节的学习，我们已经知道SQL是一种非常便利的语言。从数据库抽取数据，或者对特定的数据集中更新时，都能通过简洁直观的代码实现。
* 但是这个所谓的“简洁”也是有限制，SQL基本是一个命令实现一个处理，是所谓的非程序语言。
* 在不能编写流程的情况下，所有的处理只能通过一个个命令来实现。当然，通过使用连接及子查询，即使使用SQL的单一命令也能实现一些高级的处理，但是，其局限性是显而易见的。例如，在SQL中就很难实现针对不同条件进行不同的处理以及循环等功能。
* 这个时候就出现了存储过程这个概念，简单地说，存储过程就是数据库中保存\(Stored\)的一系列SQL命令（Procedure）的集合。也可以将其看作相互之间有关系的SQL命令组织在一起形成的一个小程序。

**优点**

* 提高执行性能。存储过程执行效率之所高，在于普通的SQL语句，每次都会对语法分析，编译，执行，而存储过程只是在第一次执行语法分析，编译，执行，以后都是对结果进行调用。
* 可减轻网络负担。使用存储过程，复杂的数据库操作也可以在数据库服务器中完成。只需要从客户端\(或应用程序\)传递给数据库必要的参数就行，比起需要多次传递SQL命令本身，这大大减轻了网络负担。
* 可将数据库的处理黑匣子化。应用程序中完全不用考虑存储过程的内部详细处理，只需要知道调用哪个存储过程就可以了

**实例**

* 定义一个没有返回值的存储过程

  ```text
  -- 定义一个没有返回值 存储过程
  -- 实现：模糊查询操作：
  select * from emp where ename like '%A%';
  create procedure mypro01(name varchar(10))
  begin
          if name is null or name = "" then
                  select * from emp; -- 传入null，就查看全表
          else
      select * from emp where ename like concat('%',name,'%'); 
          end if;	
  end;
  -- 删除存储过程：
  drop procedure mypro01;
  -- 调用存储过程：
  call mypro01(null);
  call mypro01('R');
  ```

  结果：

  ![image-20210703171452630](https://gitee.com/TeaSea33/typora-picgo/raw/master/img/20210703171452.png)

* 定义一个有返回值的存储过程

  ```text
  -- 定义一个有返回值的存储过程
  -- in 参数前面的in可以不写
  -- out 
  -- found_rows() 是mysql定义的一个函数，作用返回查询结果的条数
  -- into 
  create procedure mypro02 (in name varchar(10),out num int(4))
  begin 
  		if name is null or name = "" then
  				select * from emp;
  		else 
  				select * from emp where name like concat('%',name,'%');
  		end if;
  		select found_rows() into num;
  end;
  -- 调用存储过程
  call mypro02(null,@num);
  select @num;
  call mypro02('A',@aaa); -- 传入的参数名不一定要一样
  select @aaa;
  ```

  结果：

