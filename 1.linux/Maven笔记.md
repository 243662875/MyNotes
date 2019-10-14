[TOC]



# Maven基础

Apache Maven是一个软件项目管理和综合工具。基于项目对象模型（POM）的概念，[Maven](http://www.yiibai.com/maven)可以从一个中心资料片管理项目构建，报告和文件。给开发人员提供构建一个完整的生命周期框架。开发团队可以自动完成项目的基础工具建设，Maven使用标准的目录结构和默认构建生命周期。概括地说，Maven简化和标准化项目建设过程。处理编译，分配，文档，团队协作和其他任务的无缝连接。 Maven增加可重用性并负责建立相关的任务。

### 1、Windows上安装Maven

下载地址：http://maven.apache.org/download.cgi ，先安装JDK，然后下载 [apache-maven-3.6.1-bin.zip](http://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.6.1/binaries/apache-maven-3.6.1-bin.zip) 直接解压，并配置环境变量

 win10系统：系统变量 >>> Path >>> D:\apache-maven\bin

验证：mvn --version 显示出maven版本信息。

**配置maven文件**： 在conf目录下 找到 settings.xml 文件，可参考配置文件

链接：https://pan.baidu.com/s/1pBpbr16_A1qsOAsORqoZ-g   提取码：h6w6 

**更改maven的本地库**

通常情况下，可改变默认的 .m2 目录下的默认本地存储库文件夹到其他更有意义的名称，例如， maven-repo 

 	找到 {M2_HOME}\conf\setting.xml, 更新 localRepository 到其它名称。 

 	{M2_HOME}\conf\setting.xml 

```bash
<settings><!-- localRepository
   | The path to the local repository maven will use to store artifacts.
   |
   | Default: ~/.m2/repository
  <localRepository>/path/to/local/repo</localRepository> -->
  
<localRepository>D:\apache-maven\repository</localRepository>
#------------以上是setting配置------------------------------ 
#-----------------保存文件-------新的Maven本地存储D:\apache-maven\repository------执行命令就会保存到这个目录
mvn archetype:generate -DgroupId=com.yiibai -DartifactId=NumberGenerator -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false


#在Maven中，当你声明的库不存在于本地存储库中，也没有不存在于Maven中心储存库，该过程将停止并将错误消息输出到 Maven 控制台
#如pom.xml文件,在 Maven 本地资源库中搜索，如果没有找到，进入第2步，否则退出
<dependency>
        <groupId>org.jvnet.localizer</groupId>
        <artifactId>localizer</artifactId>
        <version>1.8</version>
</dependency>
#告诉 Maven 来获得 Java.net 的依赖，你需要声明远程仓库在 pom.xml 文件,在java.net Maven的远程存储库搜索，如果没有找到，提示错误信息，否则退出
	<repository>
	    <id>java.net</id>
	    <url>https://maven.java.net/content/repositories/public/</url>
	</repository>
</repositories>


#下载 “kaptcha”，将其解压缩并将 kaptcha-version.jar 复制到其他地方，比如：C盘
mvn install:install-file -Dfile=c:\kaptcha-{version}.jar -DgroupId=com.google.code -DartifactId=kaptcha -Dversion={version} -Dpackaging=jar
#下载完成后就在pom.xml中声明kaptcha的坐标
<dependency>
      <groupId>com.google.code</groupId>
      <artifactId>kaptcha</artifactId>
      <version>2.3</version>
</dependency>



```

当你建立一个 Maven 的项目，Maven 会检查你的 pom.xml 文件，以确定哪些依赖下载。首先，Maven 将从本地资源库获得 Maven 的本地资源库依赖资源，如果没有找到，然后把它会从默认的 Maven 中央存储库  http://search.maven.org/ 查找下载。阿里的maven仓库：https://maven.aliyun.com/mvn/search



# Centos7下Nexus3的安装和配置

参考文档：https://help.sonatype.com/repomanager3

### 1、要使用nexus服务需要安装jdk和maven

#### 1.1、jdk下载地址：https://www.oracle.com/technetwork/java/javase/downloads/index.html

```bash
rpm -ivh jdk-8u221-linux-x64.rpm

vim /etc/profile
export JAVA_HOME=/usr/java/jdk1.8.0_221-amd64
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar 

source /etc/profile

java -version
```

#### 1.2、maven下载地址：https://maven.apache.org/download.cgi

```bash
tar -zxvf apache-maven-3.6.1-bin.tar.gz

vim /etc/profile
export PATH=$PATH:/usr/local/maven/bin

source /etc/profile

mvn -v
```

### 2、安装nexus

下载地址：https://www.sonatype.com/nexus-repository-oss，点击首页大广告图，跳转后填写邮箱，点击，download，网页跳转后，选择 nexus repository manager oss 3.x - unix，下载最新安装包。

或者打开网址：https://my.sonatype.com/  在 Latest Releases 标签下， 下载最新nexus repository安装包 

```bash
wget https://download.sonatype.com/nexus/3/latest-unix.tar.gz

tar -zxvf nexus-3.18.1-01-unix.tar.gz
#解压后又2个目录
	#nexus-3.18.1-01：包含了 Nexus 运行所需要的文件。是 Nexus 运行必须的
	#sonatype-work：包含了 Nexus 生成的配置文件、日志文件、仓库文件等。当我们需要备份 Nexus 的时候默认备份此目录即可

#修改环境变量
vim /etc/profile
export NEXUS_HOME=/usr/local/nexus/nexus-3.18.1
export PATH=$PATH:$NEXUS_HOME/bin

source /etc/profile

#修改启动用户
vim /usr/local/nexus/nexus-3.18.1/bin/nexus.rc
#run_as_user=""			#内容就这一行，放开注释，填写用户即可

#修改端口
vim /usr/local/nexus/nexus-3.18.1/etc/nexus-default.properties	#默认是8081

#最后启动nexus
cd /usr/local/nexus/nexus-3.18.1/bin
./nexus start
./nexus status

#访问http://ip:8081，登陆用户admin 密码存放在：/usr/local/nexus/sonatype-work/nexus3/admin.password 目录

#开机自启动
vim /etc/rc.d/rc.local

/usr/local/nexus/nexus-3.18.1/bin/nexus start	#添加这一行内容

chmod 755 /etc/rc.d/rc.local
```



