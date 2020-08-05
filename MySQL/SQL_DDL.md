# DDL：定义语言

## 一、库的管理

>  基本操作：创建、修改、删除

### 1. 库的创建

语法：

```sql
create database (if not exists) databasename;
```

+ 案例：创建数据库Books

  ```sql
  CREATE DATABASE IF NOT EXISTS Books;
  ```

### 2. 库的修改

+ 案例：~~库名字的修改(已经被**废弃**)~~

  ```sql
  -- 已被废弃
  RENAME DATABASE books TO bookcase;
  ```

+ 案例：更改库的字符集

  ```sql
  ALTER DATABASE books CHARACTER SET UTF8MB4;
  ```

### 3. 库的删除

语法：

```sql
DROP DATABASE IF EXISTS books;
```

## 二、表的管理

> 基本操作：创建、修改、删除

### 1. 表的创建

语法：

```sql
CREATE TABLE IF NOT EXISTS table(
	[column_name] [type]([length][constraints])
    [column_name] [type]([length][constraints])
    [column_name] [type]([length][constraints])
)
```

+ 案例1：创建表books

  ```sql
  -- 使用作者编号而不是直接使用作者的原因：
  -- 作者有限可以建立一个作者表
  -- 从而达到分类储存减少冗余的效果
  CREATE TABLE IF NOT EXISTS books(
  	id INT,
  	bName VARCHAR(20),
  	price DOUBLE,
  	authorId INT(20),
  	publishDate DATETIME
  );
  ```

+ 案例2：创建表authors

  ```sql
  CREATE TABLE authors(
  	id INT,
  	au_name VARCHAR(20),
  	nation VARCHAR(10)
  )
  ```

### 2. 表的修改

> 修改目标：
>
> 1. 修改列名
> 2. 修改列的类型或约束
> 3. 添加列
> 4. 删除列
> 5. 修改表名

语法：

```sql
ALTER TABLE tab CHANGE|ADD|DROP|RENAME COLUMN col_name [TYPE|CHECK];
```

+ 案例1：修改列名

  ```sql
  ALTER TABLE books CHANGE COLUMN publishDate pubDate DATETIME;
  ```

+ 案例2：修改列的类型或约束

  ```sql
  ALTER TABLE books CHANGE COLUMN pubdate TIMESTAMP;
  ```

+ 案例3：添加列

  ```sql
  ALTER TABLE authors ADD COLUMN annual DOUBLE;
  ```

+ 案例4：删除列

  ```sql
  ALTER TABLE authors DROP COLUMN annual;
  ```

+ 案例5：修改表名

  ```sql
  ALTER TABLE authors RENAME authorsTO book_author
  ```

### 3.  表的删除

语法：

```sql
DROP TABLE tab;
```

+ 案例：删除book_author表

  ```sql
  DROP TABLE IF EXISTS `book_author`;
  ```

+ 通用写法

  ```sql
  DROP DATABASE IF EXISTS old_db;
  CREATE DATABASE new_db;
  
  DROP DATABASE IF EXISTS old_tab;
  CREATE DATABASE new_tab;
  ```

### 4. 表的复制

> 前面需要的工作
>
> ```sql
> ALTER TABLE authors CONVERT TO CHARACTER SET UTF8MB4;
> INSERT INTO authors VALUES
> (1,'村上春树', '日本'),
> (2,'莫言','中国'),
> (3,'冯唐','中国'),
> (4,'金庸','中国');
> ```

1. 仅仅复制表的结构

   ```sql
   CREATE TABLE copy_tab LIKE authors;
   ```

2. 复制表的结构外加数据

   ```sql
   CREATE TABLE copy2
   SELECT * FROM authors;
   ```

3. 只复制部分数据

   ```sql
   CREATE TABLE copy3
   SELECT id, au_name
   FROM authors
   WHERE nation='中国';
   ```

4. 仅仅复制部分表的字

   ```sql
   CREATE TABLE copy4
   SELECT id, au_name
   FROM authors
   WHERE 0;
   ```



