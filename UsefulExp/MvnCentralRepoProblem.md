# Mven中央仓库repo.maven.apache.org/maven2的错误问题

## 现象：

即使你配置了私有库，并且在maven setting.xml中配置了mirror，但是，经常会遇到执行mvn命令的时候，会提醒：
`Downloading: http://repo.maven.apache.org/maven2/`

## 原因：

所有自定义pom.xml都是继承自super pom：
`http://maven.apache.org/ref/3.0.4/maven-model-builder/super-pom.html`
super pom中有如下内容：

```xml
<repositories>
    <repository>
      <id>central</id>
      <name>Central Repository</name>
      <url>http://repo.maven.apache.org/maven2</url>
      <layout>default</layout>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
</repositories>

<pluginRepositories>
    <pluginRepository>
      <id>central</id>
      <name>Central Repository</name>
      <url>http://repo.maven.apache.org/maven2</url>
      <layout>default</layout>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
      <releases>
        <updatePolicy>never</updatePolicy>
      </releases>
    </pluginRepository>
</pluginRepositories>
```

因此，当maven项目需要下载一些metadata、pom、jar的时候，会优先去中央仓库下载，导致内网用户各种报错！

## 解决办法：

在**项目pom.xml中**添加如下配置：

```xml
<project>
<repositories>
    <repository>
        <id>central</id>
        <url>http://host:port/content/groups/public</url>
    </repository>
</repositories>

<pluginRepositories>
    <pluginRepository>
        <id>central</id>
        <url>http://host:port/content/groups/public</url>
    </pluginRepository>
</pluginRepositories>
</project>

<repositories>
    <repository>
      <id>central</id>
      <name>Central Repository</name>
      <url>http://repo.maven.apache.org/maven2</url>
      <layout>default</layout>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
  </repositories>

  <pluginRepositories>
    <pluginRepository>
      <id>central</id>
      <name>Central Repository</name>
      <url>http://repo.maven.apache.org/maven2</url>
      <layout>default</layout>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
      <releases>
        <updatePolicy>never</updatePolicy>
      </releases>
    </pluginRepository>
  </pluginRepositories>
```

阿里中央仓库版本（一般直接粘贴下面的代码就行）：

```xml
<project>
<repositories>
    <repository>
        <id>alimaven</id>
        <url>https://maven.aliyun.com/repository/public</url>
    </repository>
</repositories>
 
<pluginRepositories>
    <pluginRepository>
        <id>alimaven</id>
        <url>https://maven.aliyun.com/repository/public</url>
    </pluginRepository>
</pluginRepositories>
</project>
```

————————————————
版权声明：本文为CSDN博主「**daijiguo**」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。

原文链接：https://blog.csdn.net/daijiguo/article/details/82887362

阿里中央仓库版本的代码来自，文章下方评论用户为「**fierys**」