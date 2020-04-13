# DQL：查询语言

## 基础查询

### 语法及其特点

+ 语法

  ```sql
  select 查询列表
  from 表名;
  ```

   类似于，System.out.println();
  
+ 特点：

  1. 查询列表可以是：表中的字段，常量值，表达式，函数。
  2. 查询的结果是一个虚拟的表格

### 用法

0. 使用某个数据库的时候，需要使用USE

   ```sql
   USE myemployees;
   ```

1. 查询表中的**单个字段**

   ```sql
   SELECT first_name FROM employees;
   ```

2. 查询表中的**多个字段** 

   ```sql
   SELECT last_name, salary, email FROM employees;
   ```

3. 查询表中的**所有字段**

   1. 方法一：列举出所有的列，缺点就是比较繁琐

       ```sql
       SELECT
        employee_id,
           first_name,
           last_name,
           email,
           phone_number,
           job_id,
           salary,
           commission_pct,
           manager_id,
           department_id,
           hiredate
       FROM
        employees;
       ```

   2. 方法二：使用通配符，缺点是不够灵活

       ```sql
       SELECT * FROM employees;
       ```

4. 查询**常量值**

   ```sql
   SELECT 100;
   SELECT 'join';
   ```

5. 查询**函数**

   ```sql
   SELECT VERSION();  # 查看但前MySQL的版本
   ```

6. **起别名**

   1. 方法一：AS
   
      ```sql
      SELECT 100%98 AS result;
      SELECT 
      	last_name AS 姓, 
          first_name AS 名 
      FROM employees;
      ```
   
   2. 方法二：使用空格
   
      ```sql
      SELECT 
      	last_name 姓, 
          first_name 名 
      FROM employees;
      ```
   
   3. 案例：查询salary，显示为 "out put"
   
      > 别名有特殊字符，在MySQL中建议使用双引号，把别名引起来
   
      ```sql
      SELECT salary AS "out put" FROM employees;
      ```
   
7. **去重**：关键字 DISTINCT

   + 案例：查询员工表中涉及到的所有的部门编号

     ```sql
     SELECT department_id FROM employees; # 不去重版本
     SELECT DISTINCT department_id FROM employees;
     ```

8. 在MySQL中**加号**的作用

   + java中的加号：

     1. **运算符**，两个操作数都为数值型
     2. **连接符**，只要油一个操作数为字符串

   + MySQL中的加号只作为运算符：

     1. 两者都是数值型，则直接相加 
     2. 其中一个是字符型的，那么试图酱字符型数值转换成数值型
        + 如果转换成功，则继续做加法运算;
        + 如果转换失败，则将字符型转换成0，在做加法运算;
     3. 如果有一方是NULL，则结果肯定为NULL

   + 案例：查询员工名和姓名连接成一个字段，并显示为 姓名

     ```sql
     SELECT 
     	CONCAT(last_name, ' ', first_name) AS 姓名
     FROM employees;
     ```

     > CONCAT()函数是用于凭借字符串的函数

### 相关测试

1. 示表departments的结构，并查询其中的全部数据 

   ```sql
   DESC departments;
   SELECT * FROM departments;
   ```

2. 显示表employees中全部的job_id（不能重复）

   ```sql
   SELECT DISTINCT job_id FROM employees;
   ```

3. 显示出表employees的部分列，各个列之间用逗号连接，列头显示成OUT_PUT

   ```sql
   SELECT 
   	CONCAT(employee_id, ', ', 
   			first_name, ', ', 
               last_name, '') AS OUT_PUT
   FROM employees;
   ```

## 条件查询

### 语法及其特点

+ 语法：

  ```sql
  SELECT 
  	查询列表
  FROM
  	表名
  WHERE
  	筛选条件;
  ```

+ 特点：

  1. 按照条件表达式筛选 
  2. 按照逻辑表达式筛选
  3. 模糊查询
  4. 安全等于 <=>

### 用法

1. 按照条件表达式筛选 

   > 条件运算符：< > = <= >= <>

   + 案例1：查询工资大于1200的信息

     ```sql
     SELECT * FROM employees WHERE salary>12000;
     ```

   + 案例2：查询部门编号不等于90的员工名和部门编号

     ```sql
     SELECT 
     	last_name,
         department_id
     FROM
     	employees
     WHERE
     	department_id <> 90;
     ```

2. 按照逻辑表达式筛选

   > 逻辑运算符：&& || !
   > 			AND OR NOT

   + 作用：用于连接条件表达式

   + 案例1：查询工资在10000到22000之间的员工名，工资以及奖金

     ```sql
     SELECT
     	last_name,
         salary,
         commission_pct
     FROM 
     	employees
     WHERE
     	10000 <= salary 
     		AND salary <= 22000;
     ```

   + 案例2：查询部门编号不是在90到110之间，或者工资高于15000的员工信息

     ```sql
     SELECT *
     FROM employees
     WHERE
     	NOT(department_id>=90 
             AND department_id<=110) 
             OR salary>15000;
     ```

3. 模糊查询

   > LIKE
   > BETWEEN AND
   > IN
   > IS NULL

   + 特点：和通配符搭配使用

     > 通配符： 
     > 		% 任意多个字符，包含0个字符
     >         _ 任意单个字符

   1. **LIKE**

      + 案例1：查询员工名中包含字符a的员工信息

        ```sql
        SELECT 
        	*
        FROM 
        	employees
        WHERE 
        	last_name LIKE '%a%';
        ```

      + 案例2：查询员工名中第三个字符为e，第五个字符为e的员工名和工资

        ```sql
        SELECT
        	last_name,
            salary
        FROM
        	employees
        WHERE
        	last_name LIKE '__e_e%';
        ```

      + 案例3：查询员工名中第二个字符为_的员工名

        ```sql
        SELECT
        	last_name,
            salary
        FROM
        	employees
        WHERE
        	last_name LIKE '_\_%';
        ```

   2. **BETWEEN AND**

      + 注意：

        1. 查询结果包含临界值
        2. AND 左边的必须小于右边的

      + 案例：查询员工编号在100到120之间的员工信息

        ```sql
        SELECT 
            *
        FROM
            employees
        WHERE
            employee_id BETWEEN 100 AND 120;
        ```

   3. **IN()**

      + 含义：判断字段的信息是否属于in列表中的某一项

      + Note：

        1. 提高语句的简洁度
        2. IN里面的值必须是同一个类型，或者是兼容
        3. 不可以使用通配符

      + 案例：查询员工的公众编号是 IT_PROG, AD_PRES中的员工名和工种编号

        ```sql
        SELECT 
            last_name, job_id
        FROM
            employees
        WHERE
            job_id IN ('IT_PROG' , 'AD_VP');
        ```

   4. **IS NULL**

      + Note：

        1. = 或者 <> 不能用于判断NULL
        2. IS 只能和 NULL 配合使用 

      + 案例：查询没有奖金的员工名和奖金率

        ```sql
        SELECT 
            last_name, commission_pct
        FROM
            employees
        WHERE
            commission_pct IS NOT NULL;
        ```

4. 安全等于 <=>

   > IS NULL：仅仅可以判断NULL值，可读性较高
   >
   > <=>：既可以判断NULL值，也可以判断普通数值，可读性较低

   + 案例1：查询没有奖金的员工名和奖金率

     ```sql
     SELECT 
         last_name, commission_pct
     FROM
         employees
     WHERE
         commission_pct <=> NULL;
     ```

   + 案例2：查询工资为1200的员工名和工资

     ```sql
     SELECT 
         last_name, salary
     FROM
         employees
     WHERE
         salary <=> 12000;
     ```

### 相关测试

1. 查询员工号为176的员工的姓名和部门号和年薪

   ```sql
   SELECT 
       CONCAT(last_name, ' ', first_name) AS 姓名,
       department_id AS 部门ID,
       salary * 12 * (1 + commission_pct) AS 年薪
   FROM
       employees
   WHERE
       employee_id IN (175);
   ```

2. 查询没有奖金，且工资小于18000的salary, last_name

   ```sql
   SELECT 
       last_name, salary
   FROM
       employees
   WHERE
       commission_pct IS NULL 
       AND salary < 18000;
   ```

3. 查询employees表中，job_id不为'IT'或者工资为12000的员工信息 

   ```sql
   SELECT 
       *
   FROM
       employees
   WHERE
       NOT (job_id LIKE 'IT%')
           OR salary = 12000;
   ```

4. 查看部门departments表的结构

   ```sql
   DESC departments
   ```

5. 查询部门部门departments表中涉及到了那些位置编号

   ```sql
   SELECT DISTINCT
       location_id
   FROM
       departments;
   ```

### 面试题

1. A：`select * from employees; `和`select * from employees where last_name like '%%' and commission_pct like '%%'`是否一样？

   Q：不一样，因为‘%%’通配符不能匹配NULL。

## 排序查询

> 按照数据库里面的数据排序
>
> ```sql
> SELECT * FROM employees;
> ```
>
> 但是，想要按照salary工资顺序查看的时候，就需要排序

### 语法及其特点

+ 语法

  ```sql
  SELECT
  	查询列表
  FROM
  	表
  [WHERE 筛选条件]
  ORDER BY 
  	排序列表1 [desc 或 asc], 
  	排序列表2 [desc 或 asc];
  ```

+ 特点：

  1. asc代表升序，desc代表的是降序

     > 如果不写默认就是升序

  2. ORDER BY子句中可以支持**单个字段，多个字段，表达式，函数，别名** 

  3. ORDER BY子句一般是放在查询语句的最后面（LIMIT子句除外）

### 用法

+ 案例：查询员工信息，要求工资按照从低到高和从高到低排序

  ```sql
  # 升序
  SELECT 
      *
  FROM
      employees
  ORDER BY salary ASC;
  
  # 降序
  SELECT 
      *
  FROM
      employees
  ORDER BY salary DESC;
  ```

+ 案例2：查询部门编号大于等于90的员工信息，按照入职时间先后排序

  ```sql
  SELECT 
      *
  FROM
      employees
  WHERE
      department_id >= 90
  ORDER BY hiredate;
  ```

+ 案例3：按年薪高低显示员工的信息和年薪[**按表达式排序**]

  ```sql
  SELECT 
      *, salary * 12 * (1 + IFNULL(commission_pct, 0)) 年薪
  FROM
      employees
  ORDER BY salary * 12 * (1 + IFNULL(commission_pct, 0)) DESC;
  ```

+ 案例4：按年薪高低显示员工的信息和年薪[**按别名排序**]

  ```sql
  SELECT 
      *, 
      salary * 12 * (1 + IFNULL(commission_pct, 0)) 年薪
  FROM
      employees
  ORDER BY 年薪 DESC;
  ```

+ 案例5：按照姓名的长度显示员工的姓名和工资[**按函数排序**]

  ```sql
  SELECT 
      LENGTH(last_name) 字节长度, last_name, salary
  FROM
      employees
  ORDER BY 字节长度 DESC;
  ```

+ 案例6：查询员工信息，要求先按照工资排序，再按照员工号排序[**按多个字段排序**]

  ```sql
  SELECT 
      *
  FROM
      employees
  ORDER BY salary ASC, employee_id DESC;
  ```

### 相关测试

1. 查询员工姓名和部门号和年薪，按年薪降序，按姓名升序

   ```sql
   SELECT 
       last_name, 
       salary*12*(1+ifnull(commission_pct, 0)) 年薪
   FROM
       employees
   ORDER BY 年薪 DESC , last_name ASC;
   ```

2. 选择工资不再8000到17000员工的姓名和工资，按工资降序

   ```sql
   SELECT 
       last_name, salary
   FROM
       employees
   WHERE
       salary NOT BETWEEN 8000 AND 17000
   ORDER BY salary DESC;
   ```

3. 查询邮箱中包含e的员工信息，并先按邮箱的字节数降序，再按部门号降序

   ```sql
   SELECT 
       *
   FROM
       employees
   WHERE
       email LIKE '%e%'
   ORDER BY email DESC , department_id ASC;
   ```

## 常见函数

### 概述

> 函数类似于Java中的方法，将实现某种功能的一组逻辑语句封装在方法体中，对外暴露方法名

+ 优点

  1. 隐藏了实现细节
  2. 提高代码的重用性

+ 通用

  ```sql
  SELECT 函数名() [FROM 表];
  ```

+ 特点

  1. 函数名
  2. 函数功能

+ 分类

  1. 单行函数

     如：concat, length, ifnull等等

  2. 分组函数[统计函数，聚合函数，组函数]

     功能：做统计使用

### 单行函数

#### 一、字符函数

1. length()：获取参数值的字节数

   ```sql
   SELECT LENGTH('john');
   # 这里使用的字符集是utf8mb4，一个中文字符占3个字节
   # 如果是GBK字符集，那个一个中文字符占2个字节
   SELECT LENGTH('张三丰hahaha');
   ### 查看各种字符集
   show variables like '%char%';
   ```

2. concat()：拼接字符串

   ```sql
   SELECT 
       CONCAT(last_name, '_', first_name) 姓名
   FROM
       employees;
   ```

3. upper(), lower()

   ```sql
   SELECT UPPER('Jhon');
   SELECT LOWER('Jhon');
   ```

   + 案例：将姓变大写，名变小写

     ```sql
     SELECT 
         UPPER(last_name), LOWER(first_name)
     FROM
         employees;
     ```

4. substr(), substring()

   ```sql
   # Note: SQL中索引都是从1开始
   # 指定索引往后到结束的子串
   SELECT SUBSTR('KlenKiven', 5);
   # 指定索引往后n个字符的字串
   SELECT SUBSTR('KlenKiven', 1, 4);
   ```

   + 案例：姓名首字母大写，其他字母小写用下划线拼接，显示出来

     ```sql
     SELECT 
         CONCAT(UPPER(SUBSTR(last_name, 1, 1)),
                 '_',
                 LOWER(SUBSTR(last_name, 2))) out_put
     FROM
         employees;
     ```

5. instr()：返回字串第一次在字符串中的索引位置，如果找不到返回0

   ```sql
   SELECT INSTR('Abc', 'bc');
   ```

6. trim()：去掉前后的空格

   ```sql
   SELECT TRIM('      ABC							');
   SELECT TRIM('a' FROM 'aaaaaaaaaaaaaaaaaKlenaaaaaaaaaaa');
   SELECT TRIM('ab' FROM 'ababababababababababKlenababababab');
   ```

7. lpad()：指定字符左填充指定长度（像是那个格式化输出那个）

   ```sql
   SELECT LPAD('ABC', 10, '*');
   ```

8. rpad()：指定字符右填充指定长度（像是那个格式化输出那个）

   ```sql
   SELECT RPAD('ABC', 10, '*');
   ```

9. replace()：替换

   ```sql
   SELECT REPLACE('ABCDEFBCDGH', 'BCD', 'XYZ');
   ```

#### 二、数学函数

1. round()：四舍五入

   ```sql
   SELECT ROUND(1.65);
   SELECT ROUND(1.45);
   ```

2. ceil()：向上取整

   ```sql
   SELECT CEIL(1.52);
   SELECT CEIL(-1.02);
   ```

3. floor()：向下取整

   ```sql
   SELECT FLOOR(1.99);
   SELECT FLOOR(-1.99);
   ```

4. truncate()：截断

   ```sql
   SELECT TRUNCATE(1.6999, 1);
   ```

5. mod()：取余

   ```sql
   SELECT MOD(10, 3);
   SELECT MOD(- 10, - 3);
   SELECT 10 % 3;
   ```

#### 三、日期函数

1. now()：返回当前系统日期时间

2. curdate()：返回当前的日期不包含时间

3. curtime()：返回当前的时间不包含日期

4. year(), month(), day(), hour(), minute(), second()：返回特定的值

5. str_to_date()：将日期格式的字符串转换成指定格式的日期

   ```sql
   SELECT STR_TO_DATE('9-3-1999', '%m-%d-%Y');
   ```

   + 查询入职日期为1992-4-3的员工信息

     ```sql
     SELECT 
         *
     FROM
         employees
     WHERE
         hiredate = '1992-4-3';
     ```

     + 使用str_to_date()版本

     ```sql
     SELECT 
         *
     FROM
         employees
     WHERE
         hiredate = STR_TO_DATE('1992-4-3', '%Y-%m-%d');
     ```

6. date_format()：将日期转换成字符

   ```sql
   SELECT DATE_FORMAT('2018/6/6', '%Y年%m月%d日');
   ```

   + 查询有奖金的员工名和入职时间（XX月/XX日 XX年）

     ```sql
     SELECT 
         last_name, 
         DATE_FORMAT(hiredate, '%m月/%d日 %Y年') 入职日期
     FROM
         employees;
     ```

#### 四、其他函数

1. version()：MySQL的版本
2. databases()：数据库
3. user()：用户

#### 五、流程控制函数

1. if()：实现if-else的效果

   ```sql
   SELECT IF(10 > 5, '大', '小');
   
   SELECT 
   	last_name,
       IF(commission_pct IS NOT NULL,
           '有奖',
           '没有奖') 有无
   FROM employees;
   ```

2. case-when：switch-case使用效果

   >case 要判断的字段或者表达式
   >	when 常量1 then 要显示的值1或语句1;
   >	when 常量2 then 要显示的值2或语句2;
   >	else 要显示的值或语句;
   >end

   + 案例：查询员工的工资，要求

     > 如果部门号=30,显示的工资为1.1倍
     > 如果部门号=40,显示的工资为1.2倍
     > 如果部门号=50,显示的工资为1.3倍
     > 其他部门,显示的工资为保底工资

     ```sql
     SELECT 
         salary 原始工资,
         department_id,
         CASE department_id
             WHEN 30 THEN salary * 1.1
             WHEN 40 THEN salary * 1.2
             WHEN 50 THEN salary * 1.3
             ELSE salary
         END 新工资
     FROM
         employees;
     ```

3. case：多重if

   > case
   > 	when 判断条件1 then 要显示的值1或语句1;
   > 	when 判断条件2 then 要显示的值2或语句2;
   > 	when 判断条件3 then 要显示的值3或语句3;
   > 	else 要显示的值或语句;
   > end

   + 案例：查询员工的新工资，要求

     > 如果工资>20000，显示A级别
     > 如果工资>15000，显示B级别
     > 如果工资>10000，显示C级别
     > 否则，显示D级别

     ```sql
     SELECT 
         salary,
         CASE
             WHEN salary > 20000 THEN 'A级别'
             WHEN salary > 15000 THEN 'B级别'
             WHEN salary > 10000 THEN 'C级别'
             ELSE 'D级别'
         END 级别
     FROM
         employees;
     ```

### 多行函数

#### 概述

+ 功能

  用作统计使用，又称为聚合函数或统计函数或组函数

+ 分类

  + 数值计算：sum 求和、avg 平均值
  + 数据分析：max 最大值 、min 最小值 、count 计算个数

+ 特点

  1. sum、avg一般用于处理数值型

  2. max、min、count可以处理任何类型

  3. 以上分组函数都忽略null值

  4. 可以和distinct搭配实现去重的运算

  5. count函数：

     一般使用`count(*)`统计行数

  6. 和分组函数一同查询的字段要求是，`GROUP BY`后的字段

#### 1. 简单使用

```sql
SELECT SUM(salary) FROM employees;
SELECT AVG(salary) FROM employees;
SELECT MIN(salary) FROM employees;
SELECT MAX(salary) FROM employees;
SELECT COUNT(salary) FROM employees;


SELECT SUM(salary) 和,AVG(salary) 平均,MAX(salary) 最高,MIN(salary) 最低,COUNT(salary) 个数
FROM employees;

SELECT SUM(salary) 和,ROUND(AVG(salary),2) 平均,MAX(salary) 最高,MIN(salary) 最低,COUNT(salary) 个数
FROM employees;
```

#### 2. 是否忽略null

```sql
SELECT SUM(commission_pct) ,AVG(commission_pct),SUM(commission_pct)/35,SUM(commission_pct)/107 FROM employees;

SELECT MAX(commission_pct) ,MIN(commission_pct) FROM employees;

SELECT COUNT(commission_pct) FROM employees;
SELECT commission_pct FROM employees;

```

#### 3. 和distinct搭配

```sql
SELECT 
	SUM(DISTINCT salary),
	SUM(salary) 
FROM 
	employees;

SELECT 
	COUNT(DISTINCT salary),
	COUNT(salary) 
FROM 
	employees;
```

#### 4. count函数的详细介绍

```sql
SELECT 
    COUNT(salary)
FROM
    employees;
# 统计行数
SELECT 
    COUNT(*)
FROM
    employees;
# 统计1的个数，相当于加了一列的1的个数
SELECT 
    COUNT(1)
FROM
    employees;
```

> MYISAM存储引擎下  ，COUNT(*)的效率高
>
> INNODB存储引擎下，COUNT(*)和COUNT(1)的效率差不多，比COUNT(字段)要高一些

#### 5. 和分组函数一同查询的字段有限制

```sql
SELECT 
    AVG(salary), employee_id
FROM
    employees;
```

### 相关测试

1. 显示系统时间

   ```sql
   SELECT NOW();
   ```

2. 询员工号，姓名，工资，以及工资提高20%以后的结果（new salary）

   ```sql
   SELECT 
       employee_id,
       last_name,
       salary,
       salary * (1 + 0.2) 'new salary'
   FROM
       employees;
   ```

3. 将员工的姓名按照，并写出姓名的长度

   ```sql
   SELECT 
       last_name, LENGTH(last_name) length
   FROM
       employees
   ORDER BY SUBSTR(last_name, 1, 1);
   ```

4. 做一个查询，产生下面的结果

   > <last_name> earns <salary> monthly but wants <salary*3> Dream Salary

   ```sql
   SELECT 
       CONCAT(last_name,
               ' earns ',
               salary,
               'monthly but wants ',
               salary * 3,
               'Dream Salary') Sentence
   FROM
       employees;
   ```

5. 使用case-when，按照下面的条件

   > job_id		grade
   > AD_PRES		A
   > ST_MAN		 B
   > IT_PROG		C

   ```sql
   SELECT 
       last_name,
       job_id Job,
       CASE job_id
           WHEN 'AD_PRES' THEN 'A'
           WHEN 'ST_MAN' THEN 'B'
           WHEN 'IT_PROG' THEN 'C'
       END Grade
   FROM
       employees
   WHERE
       job_id IN ('AD_PRES' , 'ST_MAN', 'IT_PROG');
   ```

6. 查询员工粽子最大值，最小值，平均值，总和。

   ```sql
   SELECT 
       MAX(salary) MAX_SAL,
       MIN(salary) MIN_SAL,
       AVG(salary) AVG_SAL,
       SUM(salary) SUM_SAL
   FROM
       employees;
   ```

7. 查询员工表中的最大入职时间和最小入职时间相差的天数

   ```sql
   FROM
       employees;
       
   SELECT 
       MAX(hiredate) MAX_DATE,
       MIN(hiredate) MIN_DATE,
       DATEDIFF(MAX(hiredate), MIN(hiredate)) DIFFERENCE
   FROM
       employees;
   ```

8. 查询部门编号为90的员工个数

   ```sql
   SELECT 
       COUNT(*)
   FROM
       employees
   WHERE
       department_id = 90;
   ```

## 分组查询

### 概述

+ 语法

  ```sql
  SELECT column, group_function(column)
  FROM table
  [WHERE condition]
  GROUP BY group_by_expression
  [ORDER BY column];
  ```

+ 注意

  查询列表必须特殊，要求时分组函数和group by后出现的字段

+ 特点

  1. 分组筛选中的筛选条件分为两类：

     |  分组状态  |     数据源     |        位置        | 关键字 |
     | :--------: | :------------: | :----------------: | :----: |
     | 分组前筛选 |     原始表     | group by子句的前面 | where  |
     | 分组后筛选 | 分组厚的结果集 | group by子句的后面 | having |

     + 分组函数最条件肯定是放在having子句中
     + 能用分组前少选的，就优先考虑使用分组前筛选 

  2. group by语句支持单个字段分组，也可以支持多个 字段分组（多个字段之间用逗号隔开），表达式或函数（用的较少）

  3. 排序也可以被添加，是放在最后的

### 引入：查询每个部门的平均工资 

```sql
SELECT DISTINCT
    department_id, AVG(salary)
FROM
    employees;
```

+ 案例1：查询每个工种的最高工资，并且降序排序

  ```sql
  SELECT 
      job_id, MAX(salary) MAX_SAL
  FROM
      employees
  GROUP BY job_id
  ORDER BY MAX_SAL DESC;
  ```

+ 案例2：查询每个位置上的部门的个数

  ```sql
  SELECT 
      COUNT(*), location_id
  FROM
      departments
  GROUP BY location_id;
  ```

### 添加筛选条件

+ 案例1：查询邮箱中包含a字符的，每个部门的平均工资

  ```sql
  SELECT 
      AVG(salary), department_id
  FROM
      employees
  WHERE
      email LIKE '%a%'
  GROUP BY department_id;
  ```

+ 案例2：查询有奖金的每个领导手下员工的最高工资

  ```sql
  SELECT 
      MAX(salary), manager_id
  FROM
      employees
  WHERE
      commission_pct IS NOT NULL
  GROUP BY manager_id;
  ```

### 添加复杂的筛选条件

+ 案例1：查询那个部门员工的个数大于2

  ```sql
  # 1. 查询每个部门的员工数
  SELECT 
      department_id, COUNT(*) DEP_COUNT
  FROM
      employees
  GROUP BY department_id;
  # 2. 根据1的结果进行筛选(使用关键字 HAVING)
  SELECT 
      department_id, COUNT(*) DEP_COUNT
  FROM
      employees
  GROUP BY department_id
  HAVING DEP_COUNT > 2;
  ```

+ 案例2：查询每个工种有奖金的员工的最高工资大于12000的工种编号和最高工资 

  ```sql
  SELECT 
      job_id, MAX(salary) MAX_SAL
  FROM
      employees
  GROUP BY job_id
  HAVING MAX_SAL > 12000;
  ```

+ 案例3：查询领导编号大于120的每个领导手下的最低工资大于5000的林道编号是那个，及其最低工资

  ```sql
  SELECT 
      manager_id, MIN(salary) MIN_SAL
  FROM
      employees
  WHERE
      manager_id > 120
  GROUP BY manager_id
  HAVING MIN_SAL > 5000;
  ```

### 按表达式或函数分组

+ 案例：按员工行民的长度分组，查询每一组的员工个数，筛选员工个数大于5的有那些

  ```sql
  SELECT 
      LENGTH(last_name) NAME_LEN, COUNT(*)
  FROM
      employees
  GROUP BY NAME_LEN
  HAVING COUNT(*) > 5;
  ```

### 按多个字段分组

+ 案例：查询每个部门每个工种的员工的平均工资 

```sql
SELECT 
    department_id, job_id, ROUND(AVG(salary), 2)
FROM
    employees
GROUP BY department_id , job_id;
```

### 相关测试

1. 查询job_id的员工工资的最大值，最小值，平均值，总和，并按照job_id升序 

   ```sql
   SELECT 
       job_id,
       MAX(salary),
       MIN(salary),
       ROUND(AVG(salary), 2),
       SUM(salary)
   FROM
       employees
   GROUP BY job_id
   ORDER BY job_id ASC;
   ```

2. 查询员工最高工资和最低工资的差距（DIFFERENCE）

   ```sql
   SELECT 
       MAX(salary) - MIN(salary) DIFFERENCE
   FROM
       employees;
   ```

3. 查询各个管理者手下员工的最低工资，其中最低工资不能低于6000,没有管理者的员工不计算在内 

   ```sql
   SELECT 
       manager_id, MIN(salary) MIN_SAL
   FROM
       employees
   WHERE
       manager_id IS NOT NULL
   GROUP BY manager_id
   HAVING MIN_SAL >= 6000;
   ```

4. 查询所有部门的编号，员工数量和工资平均值，并按照平均工资降序

   ```sql
   SELECT 
       department_id, COUNT(*) STUFF_Q, AVG(salary) AVG_SAL
   FROM
       employees
   GROUP BY department_id
   ORDER BY AVG_SAL DESC;
   ```

## 连接查询

## 子查询

## 分页查询

## union联合查询