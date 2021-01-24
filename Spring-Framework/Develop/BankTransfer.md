# 银行转账

## 功能分析

1. 登陆页面
2. 用户登录以及登陆失败的提示语
3. 登录日志
4. 创建银行转账页面
5. 转账账户信息校验
6. 金额校验
7. 入账账户信息校验
8. 转账功能

## 功能实现

### 数据库设计

1. 用户信息表 `t_user`

   | 字段名 |  中文名  |  类型   | 大小 |    约束    |
   | :----: | :------: | :-----: | :--: | :--------: |
   |  uid   | 用户编号 |   INT   |  10  | 主键、非空 |
   | uname  |  用户名  | VARCHAR | 100  |    非空    |
   |  pwd   |   密码   | VARCHAR | 100  |    非空    |

2. 银行账户信息表 `t_account`

   | 字段名  |  中文名  |  类型   | 大小 |    约束    |
   | :-----: | :------: | :-----: | :--: | :--------: |
   |   aid   | 账户编号 |   INT   |  10  | 主键、非空 |
   |  apwd   | 支付密码 | VARCHAR | 100  |    非空    |
   | balance | 账户余额 | DOUBLE  |      |            |
   |   uid   | 用户编号 |   INT   |  10  |    非空    |

### 代码实现

#### 数据库的变现

```sql
## 创建用户表
CREATE TABLE `t_user` (
	`uid` INT(10) NOT NULL,
	`username` VARCHAR(100) NOT NULL COLLATE 'utf8mb4_general_ci',
	`pwd` VARCHAR(100) NOT NULL COLLATE 'utf8mb4_general_ci',
	PRIMARY KEY (`uid`) USING BTREE
)
COLLATE='utf8mb4_general_ci'
ENGINE=InnoDB
;

## 创建银行账户信息表
CREATE TABLE `t_account` (
	`aid` INT(10) NOT NULL AUTO_INCREMENT,
	`apwd` VARCHAR(100) NOT NULL COLLATE 'utf8mb4_general_ci',
	`balance` DOUBLE(22,0) NULL DEFAULT NtestULL,
	`uid` VARCHAR(100) NOT NULL COLLATE 'utf8mb4_unicode_ci',
	PRIMARY KEY (`aid`) USING BTREE
)
COLLATE='utf8mb4_general_ci'
ENGINE=InnoDB
;

## 创建测试数据
-- 创建用户表
CREATE TABLE `t_user` (
	`uid` INT(10) NOT NULL AUTO_INCREMENT,
	`username` VARCHAR(100) NOT NULL COLLATE 'utf8mb4_general_ci',
	`pwd` VARCHAR(100) NOT NULL COLLATE 'utf8mb4_general_ci',
	PRIMARY KEY (`uid`) USING BTREE
)
COLLATE='utf8mb4_general_ci'
ENGINE=InnoDB
;

-- 创建银行账户信息表
CREATE TABLE `t_account` (
	`aid` INT(10) NOT NULL AUTO_INCREMENT,
	`apwd` VARCHAR(100) NOT NULL COLLATE 'utf8mb4_general_ci',
	`balance` DOUBLE(22,0) NULL DEFAULT NULL,
	`uid` VARCHAR(100) NOT NULL COLLATE 'utf8mb4_unicode_ci',
	PRIMARY KEY (`aid`) USING BTREE
)
COLLATE='utf8mb4_general_ci'
ENGINE=InnoDB
;

-- 创建测试数据
INSERT INTO t_user VALUES(DEFAULT, '张三', '123');
INSERT INTO t_user VALUES(DEFAULT, '李四', '456');

INSERT INTO t_account VALUES(DEFAULT, '123', 1000.00, 1);
INSERT INTO t_account VALUES(DEFAULT, '456', 1000.00, 2);
```

#### 搭建 Spring+MyBatis 的开发环境



