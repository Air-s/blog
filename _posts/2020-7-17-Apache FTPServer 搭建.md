---
layout: post
title: Apache FTPServer 搭建
tags: [基础]
excerpt_separator: <!--more-->
---

<!--more-->

#### Apache FTPServer 相关文档

- Apache FTPServer 下载：[All Releases](<https://mina.apache.org/ftpserver-project/downloads.html>)
- Apache FTPServer 文档：[Documentation](<https://mina.apache.org/ftpserver-project/documentation.html>)
- 参考的文章
  - [springboot整合apache ftpserver详细教程](<https://www.jianshu.com/p/299d2105b04e>)
  - [Java使用Apache FtpServer实现嵌入式FTP服务器](<https://blog.csdn.net/xiao__gui/article/details/19810379>)

#### Step0（可选）：修改maven为国内源

- IDE 为 IDEA

- 右键 pom.xml，maven，open 'settings.xml'

- 找到 `<mirrors>`，添加如下内容

  ```xml
  <mirror>
      <id>aliyunmaven</id>
      <mirrorOf>central</mirrorOf>
      <name>阿里云公共仓库</name>
      <url>https://maven.aliyun.com/repository/central</url>
  </mirror>
  ```

#### Step1：准备

##### 1.1 添加依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.30</version>
        <!--<scope>test</scope>-->
    </dependency>
    <dependency>
        <groupId>org.apache.ftpserver</groupId>
        <artifactId>ftpserver-core</artifactId>
        <version>1.0.6</version>
        <!--<scope>test</scope>-->
    </dependency>
</dependencies>
```

> 我这里 ftpserver-core 版本最高至 1.0.6，虽然 Maven repository 中搜索到了1.1.1版本，但是下载不了，出现 failed to read artifact descriptor 之类的错误

##### 1.2 添加log4j配置文件

在 resources 文件夹中新建名为 `log4j.properties` 文件，内容如下

```properties
log4j.rootLogger=INFO,logfile,stdout
            
#log4j.logger.org.springframework.web.servlet=INFO,db

#log4j.logger.org.springframework.beans.factory.xml=INFO
#log4j.logger.com.neam.stum.user=INFO,db

#log4j.appender.stdout=org.apache.log4j.ConsoleAppender
#log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
#log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH\:mm\:ss} %p [%c] %X{remoteAddr}  %X{remotePort}  %X{remoteHost}  %X{remoteUser} operator\:[姓名\:%X{userName} 工号\:%X{userId}] message\:<%m>%n

#write log into file
log4j.appender.logfile=org.apache.log4j.DailyRollingFileAppender
log4j.appender.logfile.Threshold=warn
log4j.appender.logfile.File=${webapp.root}\\logs\\main.log
log4j.appender.logfile.DatePattern=.yyyy-MM-dd
log4j.appender.logfile.layout=org.apache.log4j.PatternLayout
log4j.appender.logfile.layout.ConversionPattern=[AppLog] %d{yyyy-MM-dd HH\:mm\:ss} %X{remoteAddr} %X{remotePort} %m %n

#display in console
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Threshold=info
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=[AppLog] %d{yyyy-MM-dd HH\:mm\:ss} %X{remoteAddr} %X{remotePort} %m %n
```

#### Step2：代码

```java
public static void main(String[] args) throws FtpException 
{
	//xxxFactory代表xxx结点的工厂类，通过工厂类配置与生成相应XML结点
	//以下代码相当于生成一个XML配置文件
    FtpServerFactory serverFactory = new FtpServerFactory();

    ListenerFactory listenerFactory = new ListenerFactory();
    listenerFactory.setPort(3131);
	//生成nio-listener节点中的dataConnection结点
    DataConnectionConfigurationFactory dataConnection = 
    	new DataConnectionConfigurationFactory();
    dataConnection.setPassivePorts("10000-10500");
    //将配置好的dataConnection结点添加至nio-listener节点中
    listenerFactory.setDataConnectionConfiguration(dataConnection
    	.createDataConnectionConfiguration());
	//生成nio-listener节点
    Listener listener = listenerFactory.createListener();
    //将nio-listener节点添加至Server结点中
    serverFactory.addListener("default", listener);

	//通过配置文件配置用户信息
    //PropertiesUserManagerFactory userManager = new PropertiesUserManagerFactory();
    //userManager.setFile(new File("users.properties"));
    //serverFactory.setUserManager(userManager.createUserManager());
	
	//生成与配置用户信息
    BaseUser user = new BaseUser();
    user.setName("admin");
    user.setPassword("admin");
    var homeDir = System.getProperty("user.dir").replace("\\", "/");
    user.setHomeDirectory(homeDir);
    user.setAuthorities(new ArrayList<Authority>(){{
        add(new WritePermission());
        add(new TransferRatePermission(48000000, 48000000));
        add(new ConcurrentLoginPermission(20, 6));
    }});
    serverFactory.getUserManager().save(user);

    FtpServer server = serverFactory.createServer();
    server.start();
}
```

以上代码等同于生成一个XML配置文件和一个用户的properties文件，如下所示

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--其中有些内容我省略了，具体配置文件可在下载包的res\conf中查看-->
<server>
    <listeners>
        <nio-listener name="default" port="3131">
            <data-connection>
                <active enabled="true"/>
                <passive ports="10000-10500"/>
            </data-connection>
        </nio-listener>
    </listeners>
    <!--通过配置文件配置用户信息-->
    <!--<file-user-manager file="users.properties"
                       encrypt-passwords="clear"/>-->
</server>
```

```properties
ftpserver.user.admin.userpassword=admin
ftpserver.user.admin.homedirectory=./
ftpserver.user.admin.enableflag=true
ftpserver.user.admin.writepermission=true
ftpserver.user.admin.maxloginnumber=20
ftpserver.user.admin.maxloginperip=6
ftpserver.user.admin.idletime=0
ftpserver.user.admin.uploadrate=48000000
ftpserver.user.admin.downloadrate=48000000
```

#### Step3：打包

在 pom.xml 添加如下代码

```xml
<build>
    <finalName>FTPServer</finalName>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-assembly-plugin</artifactId>
            <version>2.5.5</version>
            <configuration>
                <archive>
                    <manifest>
                        <mainClass>MAIN_CLASS_NAME</mainClass>
                    </manifest>
                </archive>
                <descriptorRefs>
                    <descriptorRef>
                        jar-with-dependencies
                    </descriptorRef>
                </descriptorRefs>
            </configuration>
        </plugin>
    </plugins>
</build>
```

注意 mainClass 结点要填写 main 函数所在类全名，为 package + 类名

在 terminal 中输入以下命令打包

```
mvn package assembly:single
```

成功打包会在 terminal 界面显示包存储路径，注意包是后缀为 jar-with-dependencies 的 jar 包

#### Step4：运行

将 cmd 定位至包所在路径（或者在文件浏览器中路径框输入cmd回车能直接定位至当前路径），执行以下命令

```
java -cp PACKAGE_NAME.jar MAIN_CLASS_NAME
```

例如我需要执行的命令为

```
java -cp FTPServer-jar-with-dependencies.jar bootstrap.StartUp
```

其中 FTPServer-jar-with-dependencies.jar 是生成的 jar 包名称，bootstrap.StartUp 是主类类全名，bootstrap 是 package 名称，StartUp 是 main 方法所在的类名

在浏览器中输入 `ftp://ServerIP:3131/` 即可访问，其中 ServerIP 为本机 IP 地址，在命令行中键入 ipconfig 即可查看

> 以上配置适合于1路由器+n设备的简单网络，如果是多路由器形成的网络，还需要配置路由器将数据请求转发至 FTP 服务（例如水星路由器就有一个虚拟服务器功能），最好还需要将运行FTP服务的机器IP固定（也可在路由器中设置）

停止服务只需在命令行中键入 Ctrl+C 即可。