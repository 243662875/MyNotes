[TOC]



# Jenkins基础

jenkins官网地址为：[http://jenkins-ci.org/](http://mirrors.jenkins-ci.org/war/latest/jenkins.war)  本文参考：https://jenkins.io/zh/doc/  和  https://www.w3cschool.cn/jenkins/

jenkins本身是用java语言开发的，所以安装jenkins的机器至少要有jdk(Java 8以上版本)。

jenkins是一个广泛用于持续构建的可视化web工具。可以很好的支持各种语言（比如：java, c#, php等）的项目构建，也完全兼容Ant、maven、gradle等多种第三方构建工具，同时跟svn、git能无缝集成，也支持直接与知名源代码托管网站，比如github、bitbucket直接集成。

主要用于：持续、自动地构建/测试软件项目。监控一些定时执行的任务。

## 1、安装部署

[下载Jenkins地址](http://mirrors.jenkins-ci.org/war/latest/)：https://jenkins.io/zh/download/

下载最新war包后，使用命令 java -jar jenkins.war --httpPort=8080 就可以启动，然后访问 http://ip:8080 就可以了

**这里，我们把Jenkins放到tomcat里运行**

jdk下载地址：https://download.oracle.com/otn-pub/java/jdk/12.0.1+12/69cfe15208a647278a19ef0990eea691/jdk-12.0.1_linux-x64_bin.rpm

tomcat下载地址：http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-9/v9.0.21/bin/apache-tomcat-9.0.21.tar.gz

```bash
wget http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-9/v9.0.21/bin/apache-tomcat-9.0.21.tar.gz
wget https://download.oracle.com/otn-pub/java/jdk/12.0.1+12/69cfe15208a647278a19ef0990eea691/jdk-12.0.1_linux-x64_bin.rpm
wget http://mirrors.jenkins.io/war-stable/latest/jenkins.war

#1.安装jdk
rpm -ivh jdk-12.0.1_linux-x64_bin.rpm

#2.配置环境变量
cat <<EOF>> /etc/profile

export JAVA_HOME=/usr/java/jdk-12.0.1
export PATH=\$JAVA_HOME/bin:\$PATH
export CLASSPATH=.:\$JAVA_HOME/lib/dt.jar:\$JAVA_HOME/lib/tools.jar
EOF

#3.重新加载环境变量
source /etc/profile

#4.解压tomcat
tar -zxvf apache-tomcat-9.0.21.tar.gz -C apache-tomcat-jenkins

#5.拷贝项目文件到tomcat工作目录
cp jenkins.war apache-tomcat-jenkins/webapps/

#6.jenkins密码存放在/root/.jenkins/secrets/initialAdminPassword目录
#7.浏览器访问Jenkins http://ip:8080/jenkins,等待准备就绪就可以使用密码登陆，接下来就是自定义Jenkins了，我这里选择（安装推荐的插件），等待安装插件，如果失败可以现在继续执行。
```

[图1](http://images.cnblogs.com/cnblogs_com/fan-gx/1492851/o_git%E5%9B%BE1.png) 

```bash
#1.我们需要一个key证书，用于Jenkins和服务器和GitLab之间建立信任关系(更新/克隆代码)回到Jenkins服务器上执行命令
ssh-keygen -t rsa

#2.查看 ssh key，复制文件/root/.ssh/id_rsa.pub内容，将其添加到Gitlab SSH Keys管理中（如图1）,后续需要用于Jenkins从Gitlab 仓库拉取代码并同步的实际的业务服务器
cat /root/.ssh/id_rsa.pub

#3.部署maven
wget http://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.6.1/binaries/apache-maven-3.6.1-bin.tar.gz

tar -zxvf apache-maven-3.6.1-bin.tar.gz

mv apache-maven-3.6.1 /usr/local/maven

#4.配置环境变量,然后更新环境变量
cat <<EOF>>  /etc/profile
export PATH=/usr/local/maven/bin:$PATH
EOF
```

## 2、Jenkins设置

### 2.1、Jenkins插件

jenkins系统管理比较重要的就是插件管理了 ，因为jenkins的工作全部是由插件来完成。在插件管理中，有可更新、可选插件、已安装，日常的插件安装都是在这个界面上完成的，如果插件安装不上，可以从网上下载，进行离线安装

参考：https://www.cnblogs.com/yy-cola/p/10162062.html

```bash
# 下载地址
https://plugins.jenkins.io/

http://updates.jenkins-ci.org/download/plugins/
```

在主页上选择  (manage jenkins)   >>   (Manage Plugins)  可以在可选插件里选择需要安装插件来满足参数化构建

**以下插件请按需安装**

Groovy 	==	 ==enkins执行Groovy代码的能力==	<!-- 我们将用该插件编写实现Groovy代码获取Git仓库的版本和历史备份记录 -->

ThinBackup 	==	==备份全局配置(Jobs的任务配置，不包含插件和工具)==	<!-- 用于备份、恢复Jobs配置-->

Publish Over SSH 	==		==Jenkins可直接在需要发布的节点上执行脚本==	<!--用于Jenkins支持在远程节点行执行-->

Git Parameter Plug-In 	==		==使得Jenkins可以读取GIt分支的版本号或Tag号==	<!--可用于获取Tag版本号-->

Multiple SCMs plugin	==	 	==Jenkins可克隆多个Git分支==	<!--下载配置文件和项目源码(git版本仓库)-->

Environment Injector Plugin		==	==参数化构建核心插件==	<!--使jenkins支持参数化构建-->

Dynamic Extended Choice Parameter Plug-In   

Extensible Choice Parameter plugin

Environment Injector Plugin     <!--用于根据选择来决定打印不同的命令结果-->

[Subversion Plug-in](http://updates.jenkins-ci.org/latest/subversion.hpi)  <!--版本管理 SVN 的插件-->

[Git plugin](http://updates.jenkins-ci.org/latest/git.hpi) <!--版本管理 GIT 的插件-->

[Maven Integration plugin](http://updates.jenkins-ci.org/latest/maven-plugin.hpi) 	==	==使得Jenkins支持创建一个maven项目==	<!--项目构建 Maven 的插件-->

[Gradle Plugin](http://updates.jenkins-ci.org/latest/gradle.hpi) <!--项目构建 Gradle 的插件 -->

Mercutial

SonarQube Scanner

Ant

Ansible

NodeJS

---------------------------

git，ssh，maven

---

### 2.2、全局工具

我们还需要设置全局工具如果Groovy和Maven的安装或者配置工具的安装目录。

在主页上选择 （Manage Jenkins） >>  (Global Tool Configuration) 

![1563346745485.png](https://i.loli.net/2019/07/17/5d2ed1ff7b38a17755.png)

![1563346869473.png](https://i.loli.net/2019/07/17/5d2ed267c90ee51201.png)



### 2.3、系统设置

Manage Jenkins	>>	Configure System 

执行者数量：配置并发数，一般设置为5，建议不超过10

参考：[图1](https://www.cnblogs.com/images/cnblogs_com/fan-gx/1492851/o_1.png)	[图3](https://www.cnblogs.com/images/cnblogs_com/fan-gx/1492851/o_3.png)	[图4](https://www.cnblogs.com/images/cnblogs_com/fan-gx/1492851/o_4.png)	[图5](https://www.cnblogs.com/images/cnblogs_com/fan-gx/1492851/o_5.png)	[图6](https://www.cnblogs.com/images/cnblogs_com/fan-gx/1492851/o_6.png)	







## 3、创建可以被maven参数化构建的项目



```bash

```













部署[SonarQube-7.3部署](https://www.cnblogs.com/zhanglianghhh/p/9787459.html)

https://www.cnblogs.com/zhanglianghhh/p/9787459.html



