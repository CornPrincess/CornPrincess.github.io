---
layout: post
title:  "JavaWeb-Tomcat"
date:   2018-08-25 10:44:31 +0800
categories: JavaWeb
tags: tomcat
---

* content
{:toc}

## Tomcat
实际应用中实现了 JavaEE规范的Web服务器很多，如 Oracle 的 WebLogic（需购买）、IBM的WebSphere（需购买）、RedHat公司的JBoss（不支持Servlet/JSP 规范，开源免费）等。这些都称为重量级服务器。

Apache 的 Tomcat，只是JavaEE规范中Servlet/JSP规范的实现者，所以其是一个轻量级服务器，它是开源免费的。我们现在的学习过程，使用的是 Tomcat 服务器。Tomcat也称为Web容器，或Servlet容器，但不能称为 JavaEE 容器。

参考链接：
[Tomcat安装、配置、优化及负载均衡详解][1]
## 目录结构
![目录结构][2]

 - bin：存放着各种命令。最为最要的是startup.bat 与 shutdown.bat两个批处理文件，用于开启和关闭 Tomcat 服务器。
 - conf：存放各种Tomcat的配置文件。主要修改的两个配置文件是server.xml与tomcat-users.xml。
 - lib：Tomcat 运行的类库，即字节码文件。
 - logs：Tomcat 的运行日志。
 - temp：Tomcat 运行的临时文件目录。
 - webapps：存放各种具体的 Web 应用的目录。
 - work：jsp 文件转换编辑结果的存放目录。

## Tomcat的运行环境配置
Tomcat 安装完毕后，需要安装JDK或JRE。对于Tomcat5 及以前版本，要求必须要安装JDK，而Tomcat6 及其后版本可以只安装JRE 而不安装JDK。

目前我安装的JDK是8.0，在系统环境变量中设置JAVA_HOME 后，将其bin目录添加到path变量中即可。
现在不需要做CATALINA配置，因为在startup.bat里面已经进行了配置

## Tomcat的运行与访问
运行startup.bat文件就可以运行tomcat。成功运行之后可以进行本机访问。

默认端口是8080，改端口：tomcat的`conf/server.xml`的`Connector`标签，**若端口号改为80，则在浏览器中不用输入端口号**

注：运行期间出现的问题
1. JAVA_HOME配置问题，按上面正确配置就可
2. 8080端口被占用问题，在win下解决方法：
```
// 找到占用8080端口的PID
netstat -ano | findstr 8080
// 关闭相应进程
taskkill -pid {进程pid} -f
```
3. 其他原因
通过把startup.bat里的`start`改为`run`可以避免重新打开一个窗口，可以看到错误日志

### 127.0.0.1 访问
通过 http://127.0.0.1:8080，可以进行访问。127.0.0.1 称为`回送地址`，表示本机。无论本机是否连接网络，均可进行访问。
### localhost访问
通过 http://localhost:8080可以进行访问。localhost 是本地DNS解析的127.0.0.1的域名，打开本机的名称为 hosts文件就可以看到。该文件在 Win10 系统中一般位于如下位置：
`C:\Windows\System32\drivers\etc\`
![host文件][3]
### JCP
JCP，Java Community Process，Java 审核社区，是一个开放的国际组织，主要负责规范、监督 Java 的发展。其他个人、企业、机构等制定的 Java 规范，必须通过JCP审核后，才可被认定为 Java 规范。JCP 的创始者为 SUN 公司。
JCP 官网为：http://jcp.org。

## 第一个Tomcat项目 HelloWorld 
### JavaWeb 项目结构
```
HelloWorld---------------------------Web应用所在目录
           |----html、jsp、css、js等文件，根目录下的文件外界可以直接访问
           |----WEB-INF目录
                    |---------classes目录(java类)
                    |---------lib目录(java类运行所需的jar包)
                    |---------web.xml(web应用的配置文件)
```
WEB-INF 这个目录下的文件外界无法直接访问，由web服务器负责调用
### JavaWeb项目打包
输入的打包命令为： 
```
jar cvfd:\abc\myprimary.war .
```
 - `cvf` 是命令参数，表示生成一个文档、显示生成过程、指定生成的文件名。
 - `d:\abc\myprimary.war`为生成的文档的存放路径及文件名。
 - 最后一个`.`表示要将当前目录中所有内容进行打包.

### 手动创建项目
将war包放在webapps目录下，tomcat启动时会自动解压war包并发布
### eclipse创建项目

在eclipse中创建dynami JavaWeb项目并运行后，因为项目是在eclipse里的tomcat的副本里运行的，所以tomcat目录下的webapps下没有。项目发布在以下目录：
```
{workspace}\.metadata\.plugins\org.eclipse.wst.server.core\tmp0\wtpwebapps
```
这个目录是由以下路径中的`server.xml`里的`deployDir="wtpwebapps"`字段决定

## Tomcat核心
### Tomcat组织结构
Tomcat 的核心配置都集中在 Tomcat 安装目录的 conf\server.xml 文件中。
![tomcat组织结构][4]
```
<Server>
    <Service>
        <Connector/>
        <Engine>
            <Host>
                <Context>
            </Host>
        </Engine>
    </Service>
</Server>
```
`Server`代表整个Servlet容器组件，是最顶层元素，可以包含一个或多个`Service`元素
`Service`包含一个`Engine`元素以及一个或多个`Connector`元素，这些`Connector`共享一个`Engine`
`Connector`代表和客户程序实际交互的组件，负责接收客户请求，以及向客户返回响应
`Engine`每个`Service`元素只能包含一个`Engine`元素，它处理在同一个`Service`中所有`Connector`接收到的客户请求
`Host`在一个`Engine`中可以包含多个`Host`,它代表一个虚拟主机(即一个服务器程序可以部署在多个有不同IP的服务器主机上)，它可以包含一个或多个应用
`Context`使用最频繁的元素，代表了运行在虚拟主机上的单个web应用

### 配置虚拟目录
默认情况下，主机会将appBase指定目录中的所有项目通过当前主机域名访问。但也可以将本地文件系统中任意目录指定为通过该机域名访问的应用目录，交由Tomcat进行管理。这个被指定的文件系统中的目录，称为`虚拟目录`。

有两种方式可以实现对虚拟目录的配置。
#### 1.在主机配置中添加`Context`标签
```
<Context path="/virtual-path" docBase="D:/helloworld2"/>
```
 - path：指定在当前主机域名下访问该应用的 URI。
 - docBase：指定应用目录的路径。
 - 
这种添加虚拟目录的方式存在一个问题：若采用添加虚拟目录的方式向当前主机中添加一个项目，需要重启 Tomcat 服务器后，该修改过的server.xml 文件才会被重新加载。而Tomcat服务器的重启，将直接影响到其它项目的正常运行。

#### 2.在主机目录中添加配置
Tomcat 启动后，会在 Tomcat 安装目录的 conf 中自动创建一个目录。该目录与server.xml的`Engine`标签对应，目录名与<Engine/>的name属性相同，默认为 Catalina该目录即为引擎目录。打开该引擎目录，可以看到有 localhost 主机目录

需要注意的是，该`Context`标签没有path属性。该 path 属性值即为该XML文件的文件名。也就是说，该XML文件名即为在当前主机域名下访问该应用的URI。该方式所创建的虚拟目录无需重启Tomct，就可以直接由Tomcat来管理。当这个XML文件写完后一保存，即可发现Tomcat的控制台信息中提示，已经成功发布该项目。

### 配置虚拟主机
配置虚拟主机，即在同一个硬件主机上运行多个域名系统，当作多个主机来使用，即在同一台机器上虚拟出多个主机。换句话说，在同一个Tomcat上，除了运行着默认的localhost域名系统外，同时还运行着其它域名的系统。

#### 1.域名解析服务DNS
只所以我们通过域名locahost可以访问本机，是因为我们 Windows系统本身也充当前DNS服务器的角色。打开`C:/Windows/System32/drivers/etc` 中的 `hosts`文件，可以看到 localhost域名所对应的 IP 地址为 127.0.0.1，即本地回送地址，表示本机。

#### 配置虚拟主机步骤
1. 修改 server.xml，在`servlet.xml`中`Host`后添加另一个`Host`主机，指定该主机的域名为www.xiaocorn.com。
2. 在tomcat目录下创建新的目录`mywebapps`，并向里面添加工程文件。
3. 修改windows中的`hosts`文件，向里面添加以下映射
```
127.0.0.1      www.xiaocorn.com
```
4. 重新启动tomcat

结果：
1. 在`{tomcatroot}\conf\Catalina`中会有新的目录`www.xiaocorn.com`,代表新的虚拟主机，并且可以访问`www.xiaocorn.com:8080/helloworld`
2. 若创建多个虚拟主机，则127.0.0.1默认访问localhost，因为在server.xml中的<Engine/>标签中有一个属性 defaultHost 指明了使用相同IP的不同域
名设置情况下，使用IP访问时默认访问的主机,默认情况下，defaultHost的值为localhost，也就是说，这里若使用127.0.0.1来访问的话，真正访问的是 localhost 虚拟主机。
```
<Engine name="Catalina" defaultHost="localhost">
```

### Context查找顺序
tomcat在加载一个web应用时，会一次按照以下五种方式查找web应用中的`Context`元素，优先级一次降低，直到找到为止。

```
1. 到Tomcat安装目录`/conf/Context.xml`文件中查找<Context>元素。
2. 到Tomcat安装目录`/conf/[enginename]/[hostname]/context.xml.default`文件中查找`Context`元素。       `[enginename]`:表示`Engine`的`name`属性
    `[hostname]`:表示`Host`的那么属性
3. 到Tomcat安装目录`/conf/[enginename]/[hostname]/[contextpath].xml`文件中查找`Context`元素
    `[contextpath]`:表示单个Web应用的URL入口（这个可以用来配置虚拟路劲）
4. 到Web应用的`META-INF/context.xml`文件中查找`Context`元素
5. 到Tomcat安装目录`/conf/server.xml`文件中查找`Context`元素。只适用于单个Web应用
```

### 将项目部署为Tomcat默认应用
见第一篇参考博客[Tomcat安装、配置、优化及负载均衡详解][1]


  [1]: http://www.cnblogs.com/rocomp/p/4802396.html
  [2]: http://static.zybuluo.com/xiaocorn/j3ywo0g94olqn0ww43ydes5b/image_1clnfqodv13ca1ejgabturfror9.png
  [3]: http://static.zybuluo.com/xiaocorn/uttwruz2egw2akz4kri6op7o/image_1clnh906d1qjh1sjm1ud012v1au99.png
  [4]: http://static.zybuluo.com/xiaocorn/q5ni6m87gc4d1bsn8qnfnxuw/image_1clo54ld911l31qioba8pb9lci9.png