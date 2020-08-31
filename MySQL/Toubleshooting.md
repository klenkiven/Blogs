# 数据库使用过程中遇到的一些问题

## mysql的当前时间和系统时间不一致的解决方法

使用SQL指令来修改时区: (推荐)

```sql
# 查看数据库当前时间
SELECT NOW();
#查看时区
show variables like '%zone%';
select @@time_zone;
#修改mysql全局时区为北京时间
set global time_zone = '+8:00';
#修改当前会话时区
set time_zone = '+8:00';
#立即生效
flush privileges;
```

配置配置文件: 

```
编辑配置文件vim /etc/my.cnf，找到[mysqld]，然后加入default-time-zone = '+8:00'。重启mysql让时区生效。
```

