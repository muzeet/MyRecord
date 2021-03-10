## 一步一步构建Maven工程

#### Maven下载和安装

在Maven的官网即可下载，点击访问[Apache Maven](http://maven.apache.org/download.cgi)。

![image-20210310165952114](D:\Work\MyRecord\IMG\normal\Maven download.png)

安装直接解压即可

#### Maven常用配置

##### 环境变量配置

1.添加M2_HOME，对应Maven bin解压目录

![image-20210310170831233](D:\Work\MyRecord\IMG\normal\M2_HOME.png)

2.增加PATH

![image-20210310171039028](D:\Work\MyRecord\IMG\normal\MAVEN PATH ENV.png)

3.测试

在cmd窗口输入`mvn -v`查看，显示如下即为配置成功

```
C:\Users\mindspore>mvn -v
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
Maven home: D:\Work\apache-maven-3.6.3\bin\..
Java version: 1.8.0_281, vendor: Oracle Corporation, runtime: D:\Work\Java\Java\jdk1.8.0_281\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```



##### 配置文件修改

修改maven解压目录下conf/settings.xml文件

1.修改本地仓库位置

在<**localRepository**>标签内添加自己的本地位置路径

```
<!-- localRepository
   | The path to the local repository maven will use to store artifacts.
   |
   | Default: ${user.home}/.m2/repository
  <localRepository>/path/to/local/repo</localRepository>
  -->
  <localRepository>D:\Work\Mvn_repoy</localRepository>
```

2.修改maven默认JDK版本

在<**profiles**>标签下添加一个<**profile**>标签，修改maven默认的JDK版本

```
<profile>     
    <id>JDK-1.8</id>       
    <activation>       
        <activeByDefault>true</activeByDefault>       
        <jdk>1.8</jdk>       
    </activation>       
    <properties>       
        <maven.compiler.source>1.8</maven.compiler.source>       
        <maven.compiler.target>1.8</maven.compiler.target>       
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>       
    </properties>       
</profile>
```

3.添加国内镜像源

添加<**mirrors**>标签下<**mirror**>，添加国内镜像源，一般使用阿里云镜像库

```
<!-- 阿里云仓库 -->
<mirror>
    <id>alimaven</id>
    <mirrorOf>central</mirrorOf>
    <name>aliyun maven</name>
    <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
</mirror>

<!-- 中央仓库1 -->
<mirror>
    <id>repo1</id>
    <mirrorOf>central</mirrorOf>
    <name>Human Readable Name for this Mirror.</name>
    <url>http://repo1.maven.org/maven2/</url>
</mirror>

<!-- 中央仓库2 -->
<mirror>
    <id>repo2</id>
    <mirrorOf>central</mirrorOf>
    <name>Human Readable Name for this Mirror.</name>
    <url>http://repo2.maven.org/maven2/</url>
</mirror>
```

#### IDEA下配置Maven

![image-20210310181927477](D:\Work\MyRecord\IMG\normal\Idea maven setting.png)

#### IDEA创建Maven项目

![image-20210310173759462](D:\Work\MyRecord\IMG\normal\idea maven proj start.png)



![image-20210310174342307](D:\Work\MyRecord\IMG\normal\Maven_quickstart.png)

```
<groupId>muzeet.data</groupId>
<artifactId>AwsomeBigData</artifactId>
<version>1.0-SNAPSHOT</version>
```

创建子module

![image-20210310174550058](D:\Work\MyRecord\IMG\normal\create sub module.png)

![image-20210310174724698](D:\Work\MyRecord\IMG\normal\create submodle from parent module.png)

![image-20210310174856364](D:\Work\MyRecord\IMG\normal\submodule maven info.png)