# Servlet

## Servlet 概念
server applet ,运行在服务器的小程序
servlet就是一个接口,定义了java类被浏览器访问(tomcat识别)的规则

## 快速入门
1.创建javaEE的项目
2.定义一个类,实现Servlet接口
`public class ServletDemo1 implements Servlet`
3.配置Servlet
在web.xml中配置:(<web-app>内)
```
 <!--配置Servlet-->
    <servlet>
        <servlet-name>demo1</servlet-name>
        <servlet-class>cn.itcast.web.servlet.ServletDemo1</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>demo1</servlet-name>
        <url-pattern>/demo1</url-pattern>
    </servlet-mapping>
```

## 执行原理
