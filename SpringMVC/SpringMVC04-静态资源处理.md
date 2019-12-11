# 关于静态资源访问的问题

---

由于我们的servlet设置了URL匹配方式为/，所以，它将静态资源也当做一个后台的请求

比如

http://localhost:8080/s/static/css/index.css

它尝试去匹配一个static/css/index.css的Controller里面的RequestMapping的组合，因为没有，所以404，

解决方式很多，最简单的是让springmvc单独处理，将这些交给容器的默认的servlet处理，就不让DispatcherServlet来处理了。

解决方式1：

```xml
<!--默认的servlet处理者-->
<mvc:default-servlet-handler/>
<!-- 为了让原有的controller生效，要加上一个注解驱动 -->
<mvc:annotation-driven/>
```

MIME类型



解决方式2：

通过映射关系描述，一一对编写规则

```xml
<mvc:resources mapping="/static/css/*" location="static/css/"></mvc:resources>
```



解决方式3：

自行在web.xml定义映射规则