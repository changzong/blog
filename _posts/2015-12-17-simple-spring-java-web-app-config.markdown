---
layout: post
title:  "跨设备Web应用开发的框架选择"
date:   2015-12-16 14:21:00
categories: jekyll update
---
刚刚接触Java和Spring框架，为了和同事们接轨我也是拼了，IDE配置和整个架构简直比Python的同类技术复杂多了，又是写xml又是导入依赖包的，甚是烦人。刚刚成功配置出了一个简易Spring项目，特此写篇博客记录下来以免之后忘记了重蹈覆辙。网上的论坛博客什么的虽然相关的内容很多，但是要么是复制粘贴别人的文章，要么是根本没有逻辑性，也不具有普适性。最后还是在一次次查找StackOverFlow中成功完成的。
***
需要的工具：
* Intellij idea IDE
* Tomcat
* Maven
* Java

1. 安装配置好Java，Tomcat和Maven
在Linux或OsX下，修改~/.bash_profile文件在里面添加JAVA_HOME，CATALINA_HOME，和MAVEN_HOME同时对PATH做相应修改。Windows下直接编辑环境变量就好（废话不多说）

2. 下载安装好Intellij IDE建立Maven项目
* 选择新建项目，左边选中Maven，右边选择JDK地址和maven的webapp，选下一步
![mavenproj](http://7xoylk.com1.z0.glb.clouddn.com/1.png)

* 输入你的GroupId，ArtifactId，下一步

* 选择你Maven的路径，下一步
![mavenselc](http://7xoylk.com1.z0.glb.clouddn.com/2.png)

* 填入你的项目名称和路径，结束

之后进入到IDE项目界面，这时需要等待IDE下载相关的基本依赖包来完成项目的建立（基本来自maven），要等待一会儿，你会发现左边的文件结构中多出了src，target等文件夹

3. 配置web应用
* File->Project Structure->Project Settings->Modules 右侧上面选中Sources，在src->main下面新建java文件夹，并点击上面的Mark as: Sources设为java源文件夹。
![modulesource](http://7xoylk.com1.z0.glb.clouddn.com/4.png)

* 同样在Modules里右侧上面选中Dependencies，点击下面加号选择Library添加Tomcat依赖。
![moduledepen](http://7xoylk.com1.z0.glb.clouddn.com/3.png)

* 我们先暂时不添加其他依赖包，之后在pom.xml中手动添加

* File->Project Structure->Project Settings->Artifacts，中间框中点加号添加新的，右边Type类型选择Web application:Exploded，点OK结束项目设置。
![artifacttype](http://7xoylk.com1.z0.glb.clouddn.com/5.png)

* 在IDE上面栏点击Run->Edit Configurations，左上角点击加号添加Tomcat local运行配置。

* 在右侧Server栏中设置默认打开浏览器，域名和端口（经实际检测，默认情况下localhost，127.0.0.1，0.0.0.0，和你的ip地址都是有效的域名。端口还是要按照Tomcat中server.xml中设置的一致。）

* 在右侧Deployment中点击加号添加Artifact，选择war exploded那个。

至此，如果你在src->main->webapp目录下有一个index.jsp文件，就应该可以直接run运行了。（注意，运行之前要先确保Tomcat服务器是关闭的，否则会因为端口被占用而出现错误，我觉得这一点很不智能。）

4. 配置Spring项目
之前的配置都是为了基本的Web应用，如果要使用Spring Web框架的话需要进一步配置
* 修改pom.xml文件，主要是添加里面的依赖包。我的pom.xml文件如下：

{% highlight XML %}
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.bigdata.transfar</groupId>
    <artifactId>quickreport</artifactId>
    <packaging>war</packaging>
    <version>1.0-SNAPSHOT</version>
    <name>quickreport Maven Webapp</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>4.2.3.RELEASE</spring.version>
        <spring-data.version>1.9.1.RELEASE</spring-data.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.0</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-jpa</artifactId>
            <version>${spring-data.version}</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate.javax.persistence</groupId>
            <artifactId>hibernate-jpa-2.0-api</artifactId>
            <version>1.0.1.Final</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-entitymanager</artifactId>
            <version>5.0.4.Final</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.38</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20151123</version>
        </dependency>
    </dependencies>
    <build>
        <finalName>quickreport</finalName>
    </build>
</project>
{% endhighlight %}

里面的每一个dependency可以通过[Maven Repository][mvnrepo]查找并直接复制过来。你添加依赖之后，IDE会自动在右上角弹出消息说依赖改变了问是否要导入，你选导入就好了，它会自己下载这些依赖，你可以在External Libraries中找到他们。

* 修改web.xml文件。需要添加servlet和servlet-mapping，这两个的name必须一致，servlet-mapping中的url-pattern可以用通配符*.html这个就可以访问所有html和jsp文件。（注意，不要写成*.jsp，这样不会匹配到任何jsp文件）我的web.xml文件如下：

{% highlight XML %}
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
        version="3.1">
    <display-name>Archetype Created Web Application</display-name>
    <servlet>
        <servlet-name>spring</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>spring</servlet-name>
        <url-pattern>*.html</url-pattern>
    </servlet-mapping>

    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
</web-app>
{% endhighlight %}

* 添加spring-servlet.xml文件在跟web.xml同一个目录下（WEB-INF）。这里面配置用到的Spring的Java package以及可以访问到的jsp文件。在WEB-INF下新建一个jsp的文件夹，xml中就可以写成/WEB-INF/jsp/来使用它们。我的spring-servlet.xml问价如下：

{% highlight XML %}
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
    <context:component-scan base-package="com.bigdata.transfar"/>

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
{% endhighlight %}

* 在src->main->java目录下新建个package，名字为在上一步中spring-servlet.xml设定的base-package值。在下面新建一个java class，里面使用Spring框架进行地址映射，并返回jsp文件的名字。如下：

{% highlight Java %}
package com.bigdata.transfar;

import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
@RequestMapping("/login")
public class HelloController
{
    @RequestMapping(method = RequestMethod.GET)
    public String printWelcome(ModelMap model)
    {
        model.addAttribute("message", "Hello world!");
        return "login";
    }
}
{% endhighlight %}

* 在webapp->WEB-INF->jsp文件夹下新建一个login.jsp文件，执行显示内容，如下：

{% highlight HTML %}
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title></title>
</head>
<body>
<h1>The login page</h1>
<h1>${message}</h1>

</body>
</html>
{% endhighlight %}

我的文件结构如图：
![filestructure](http://7xoylk.com1.z0.glb.clouddn.com/6.png)

***

至此，配置Spring Web项目结束。运行可发现访问localhost:8080可以进入欢迎界面，访问localhost:8080/login.html可以进入login界面。
![index](http://7xoylk.com1.z0.glb.clouddn.com/7.png)
![login](http://7xoylk.com1.z0.glb.clouddn.com/8.png)


[mvnrepo]: http://www.mvnrepository.com/