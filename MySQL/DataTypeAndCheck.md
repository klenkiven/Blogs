# 	数据类型和约束

## 一、常见的数据类型

### 1. 概述

+ **数值型**
  + 整型
  + 小数
    + 定点数
    + 浮点数
+ **字符型**
  + 较短的文本
    + char，varchar
  + 较长的文本
    + text，blob（较长的二进制数据）
+ **日期型**

### 2. 数值型 -- 整形

+ 分类

    |   整数类型   | 字节数 |
    | :----------: | :----: |
    |   Tinyint    |   1    |
    |   Smallint   |   2    |
    |  Mediumint   |   3    |
    | Int、Integer |   4    |
    |    Bigint    |   8    |

+ 案例：如何设置无符号和有符号

    ```sql
    CREATE TABLE tab_int(
        t1 INT(7) ZEROFILL,
        t2 INT UNSIGNED
    );
    -- 例如是INT(7) ZEROFILL 并且数字是 123
    -- 显示结果 0000123

    -- 正常输入
    INSERT INTO tab_int VALUES(-123456, 123456);
    -- MariaDB 这里发生了报错,不匹配，这里是不能运行的
    INSERT INTO tab_int VALUES(-123456);
    -- MariaDB 这里发生了报错Out of range，这里是不能运行的
    INSERT INTO tab_int VALUES(-123456, -123456);
    -- MariaDB 超出范围报出错误Out of range，，这里是不能运行的
    INSERT INTO tab_int VALUES(3241342443, 23434556213);
    ```

+ 特点：
1. 如果不设置是有符号还是无符号，默认是有符号；如果设置无符号需要加入关键字 `UNSIGNED` 关键字。
  2. 如果插入的值超出了整形的范围，会报出out of range异常，并且停止继续运行。
  3. 如果不设置长度会有默认的长度（INT 默认11， INT UNSIGNED 默认 10）
  4. 长度代表显示的宽度，如果不够会用0填充（**必须使用ZEROFILL来配合使用**）

### 3. 数值型 -- 小数

+ 分类：

|         浮点类型         |   字节数   |
| :----------------------: | :--------: |
|       float(M, D)        |     4      |
|       double(M, D)       |     8      |
|       **定点类型**       | **字节数** |
| DECIMAL(M, D) / DEC(M,D) |    M+2     |

+ 案例：

  ```sql
  CREATE TABLE tab_float(
  	f1 FLOAT(5, 2),
  	f2 DOUBLE(5, 2),
  	f3 DECIMAL(5, 2)
  );
  
  -- 正常插入
  INSERT INTO tab_float VALUES
  (123.45, 123.45, 123.45);
  -- 小数位数超过2位，那就保留到3位
  -- 小数位数小于2位，那就0补齐
  INSERT INTO tab_float VALUES
  (123.456, 123.4567, 123.45678),
  (123.4, 123.4, 123.4);
  -- 所有数字超过5位，则报错out of range，无法运行
  INSERT INTO tab_float VALUES
  (1234.5, 1234.5, 1234.5);
  ```

+ 特点：

  1. M：整数部分和小数部分总体的长度；D：小数部分的长度
  2. float 和 double 的 M 和 D 都可以省略，可以根据插入熟知的精度确定；但是如果是DECIMAL的话默认是(10, 0)
  3. 定点型的精度较高，如果要求插入的数值精度较高，则优先使用定点型

### 4. 字符型

+ 分类（较短的文本）

  | 字符串类型 | 最多字符数 |
  | :--------: | :--------: |
  |  CHAR(M)   |     M      |
  | VARCHAR(M) |     M      |

+ 特点：

  1. char比较耗费空间，但是性能较高；varchar比较节约空间，但是性能较低
  2. char的字符数可以省略默认为1，varchar不可以省略

+ 其他类型：

  + 较短的二进制：binary,  varbinary
  + 保存枚举：enum
  + 保存集合：set

### 5. 日期型

+ 分类

  + date：只有日期没有时间

  + time：只有时间没有日期

  + year：保存年份

  + **datetime**：8个字节，最小值1000-01-01 00:00:00 - 9999-12-31 23:59:59

  + **timestamp**：4个字节，最小值19700101080001 - 2038年某个时刻

    > timestamp和实际时区有关























# 原则：类型越简单越好，占地越小越好