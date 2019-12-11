# SpringMVC

## 简介

​	springmvc是一个web层的mvc框架，何为MVC?

- model	模型

- view          视图

- controller   控制器

  这是一种设计模式，将责任进行拆分，不同的组件负责不同的事情。

好处：

- 结构清晰
- 更好维护（大量使用jsp的年代，<%%>，应该只是用来显示）



坏处：

- 更加复杂了



## 入门体验

1. 创建web项目
2. 编写web.xml，在其中注册一个特殊的servlet，前端控制器
3. 编写一个springmvc的配置文件
   1. 注册一个视图解析器
4. 编写一个控制器
5. 编写一个结果页面



创建maven web项目结构如下：

![1553824739186](../../%E7%AC%94%E8%AE%B0/assets/1553824739186.png)





### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>person.jzh</groupId>
  <artifactId>springmvc01</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>

  <name>springmvc01 Maven Webapp</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.1.5.RELEASE</version>
    </dependency>

  </dependencies>

  <build>
    <finalName>springmvc01</finalName>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.22.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-war-plugin</artifactId>
          <version>3.2.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>
```





### web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1"
         metadata-complete="true">


    <!--注册一个前端控制器
        DispatcherServlet
    -->
    <servlet>
        <!--这里写的这个名字是有讲究的，
            如果我们不去修改spring配置文件默认的位置，那么
            它会去web-inf下面找一个叫做springmvc-servlet.xml的文件
        -->
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    </servlet>

    <!--servlet映射配置-->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <!--这里统一给我写斜杠-->
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```



### springmvc-servlet.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--配置一个视图解析器
        常用内部资源视图解析器
    -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!--1. 前缀-->
        <property name="prefix" value="/jsp/"/>

        <!--2. 后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>

    <bean class="person.jzh.controller.HelloController" name="/helloController">

    </bean>


</beans>
```



### 控制器

```java
package person.jzh.controller;

import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;


//实现一个Controller接口的方式
public class HelloController implements Controller {

    @Override
    public ModelAndView handleRequest(javax.servlet.http.HttpServletRequest request, javax.servlet.http.HttpServletResponse response) throws Exception {
        ModelAndView mav = new ModelAndView();
        mav.addObject("girl", "菲菲");
        mav.setViewName("girl");
        return mav;
    }
}
```



### 视图代码

```jsp
<%--
  Created by IntelliJ IDEA.
  User: jzh
  Date: 2019/3/29
  Time: 10:21
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    我的女孩：${girl}
</body>
</html>
```





## SpringMVC分析

------

### 流程图

![1553824076571](../../%E7%AC%94%E8%AE%B0/assets/1553824076571.png)

- Front Controller：前端控制器，大部分逻辑都是通过它来完成的，但其实它只是一个空执行而已，它不具体干活，只是指挥谁去干活，属于发号施令的。
- Controller：控制器，具体工作的人，通过Front Controller来委托处理前端请求，在完成请求后会将数据传回前端控制器
- View Template：视图解析器，通过Front Controller来决定使用哪种视图技术来完成请求，再返回给前端控制器





### 组件分析

- web.xml：注册前端控制器，目的在于，我们希望让springmvc去处理所有的请求

  - 通过

    ```xml
    <!--servlet映射配置-->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <!--这里统一给我写斜杠-->
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    ```

    确实是处理所有的请求（不是真的所有）

  - urlPattern的写法问题

    - /
    - /* （永远都不要这么写，）
    - *.do



#### /*不能写的原因

请求/helloController过去的时候，它的视图名称是girl，girl.jsp页面，但它将其当作了一个叫做girl.jsp的请求，尝试去匹配对应的controller，但是我的容器当中根本不存在这样的controller，所以无法匹配，导致404问题。





#### *.do的用法

这种方式，是有的团队习惯将请求的行为加个小尾巴用来区分，do行为，有些团队习惯用*.action

这种写法也可以处理多层/请求路径

```xml
<bean class="person.jzh.controller.HelloController" name="/hello/Controller.do"></bean>
```





#### /用法

也是处理所有的请求，但与/*不一样，它处理完之后要出去的时候不会再去将这个girl.jsp当作一个新的请求，而是将这个渲染的结构直接返回给浏览器





### 前端控制器

------

springmvc的设计理念是希望开发者尽量远离原生的servletAPI，因为其不是很好用，比较繁琐，希望将操作进一步的简化，它将很多东西的责任进行了拆分，不希望我们将一些技术点绑定死，而是可以做到随意的切换。本身还是基于servlet设计的，是一个分发的servlet。



#### springmvc配置文件名字的问题

默认情况下是用dispatcherServlet的名字当作命名空间

[servletName] -servlet.xml(WEB-INF)之下寻找。

[servletName] -servlet = namespace

将配置文件移动位置之后，会出现以下异常

```java
 Could not open ServletContext resource [/WEB-INF/springmvc-servlet.xml]
```

如果非要重新使用另一个名字

```xml
<servlet>
    <!--这里写的这个名字是有讲究的，
        如果我们不去修改spring配置文件默认的位置，那么
        它会去web-inf下面找一个叫做springmvc-servlet.xml的文件
    -->
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <!--可以重新生成配置文件的名字-->
        <param-name>namespace</param-name>
        <param-value>mvc-servlet</param-value>
    </init-param>
</servlet>
```

默认的规则要求在web-inf下，但是maven项目的标准应该在resources下面，如何解决这下问题呢？

- 重新制定上下文的配置文件的位置即可

```xml
<init-param>
    <!--上下文配置的位置的制定-->
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:springmvc.xml</param-value>
</init-param>
```

此时是在类路径下寻找springmvc.xml这个配置文件，推荐使用这种





### 视图解析器

------

springmvc支持多种视图技术

- jsp
- freemaker（模板技术）

内部的资源视图解析器

- 视图前缀

  - /jsp/：它是我们的请求响应的资源的路径的配置 ,	viewname：girl ---->   /jsp/girl

  - ```java
    mav.setViewName("girl");
    ```

- 后缀

  - .jsp：此时我们的前缀+视图名称+后缀 = /jsp/girl.jsp

物理视图由逻辑视图转换而来

物理视图：webapp/jsp/girl.jsp



逻辑视图

- prefix：前缀
- logicViewName：视图名称
- suffix：后缀





### 控制器

是一种比较传统的实现一个接口的方式完成的，Controller

如果一个接口只有一个方法，这种接口叫做函数式接口

```java
@FunctionalInterface
public interface Controller {
   @Nullable
   ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception;

}
```

servlet里面由doGet,doPost里面入参就是请求与响应，而这个方法的入参也是一样的

设计为ModelAndView，在model当中填充数据，然后在具体的视图进行展示

还需要在配置文件当中配置一下这个bean，要取个名字，名字充当了这个请求的URL

```xml
<bean class="person.jzh.controller.HelloController" name="/hello/Controller.do"></bean>
```

它就处理一个请求，跟servlet差别不是很大



## 常用注解

```java
@Controller       //将一个类映射为Controller层
@RestController   //将一个类映射为Controller层(方法返回的都是数据模型)
@RequestMapping   //将一个方法映射到URL上
@GetMapping   //将一个方法映射到URL上(只能为Get请求)
@PostMapping  //将一个方法映射到URL上(只能为Post请求)
@RequestParam     //将请求参数封装到方法参数上
@RequestHeader    //将请求头部封装到方法参数上
@CookieValue      //将Cookie封装到方法参数上
@PathVariable     //将URL中的值封装到方法参数上
@RequestBody      //将请求体(POST请求)中的值封装到方法参数上
@ModelAttrubite   //将请求参数封装为对象(在请求作用域中,参数和方法级别)
@SessionAttributes//将请求参数封装为对象(在Session作用域中,类级别)
@Valid            //后台校验
@InitBinder       //(Spring2.5及之前版本会使用)
@ResponseBody     //将方法返回值写入到ResponseBody中
@ExceptionHandler //同一异常处理  
@ControllerAdvice //Controller的通知

```







## 注解开发模式

```
基于实现接口的方式已经是过去式了，采用注解开发很简单
```

基本注解：

- @Controller
- @RequestMapping



### 开发步骤

1. 记得配置一下基础扫描的包，这样配置的注解才会生效
2. 在指定的类上面添加（@Controller） 注解
3. 添加@RequestMapping类似于前面的Controller的那个名字（只是它们是由不同的requesthandler处理的[HandlerMapping](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-handlermappping)）

当我们写上Controller之后，就标记了它为spring的一个组件，并且是控制器的组件，此时我们的handlemapping会去扫描寻找这个controller是否与之匹配，如果发现匹配就把这里处理的工作交给它。



匹配的规则

具体的匹配就是通过请求的路径进行匹配的

@RequestMapping(URI)

此时就是通过这个URI进行匹配



@RequestMapping

可以写在方法上，也可以写在类上（推荐两者结合）





### 转发与重定向

- 转发到页面  默认到选项
- 重定向到页面    redirect:path
- 转发到另外一个控制器   forward:path





### 关于springmvc访问web元素

- request
- session
- application

可以通过模拟的对象完成操作，也可以使用原生的servletAPI完成，直接在方法当中入参即可





## 注解详解

### @RequestMapping

- value：写的是路径，是一个数组的形式，可以匹配多个路径
- path：是value的别名，所以二者任选其一，他们的作用是一样的
- method：指定可以访问的请求的类型，比如get   post  它也可以写成一个数组的形式
- params：可以指定参数（有参的请求方式），你还可以去限定这个参数的特征，比如必须等于某个值，不等于某个值
- headers：能够影响浏览器的行为
- consumers：消费者，媒体类型，比如可以限定必须为application/json;charset=utf-8
- produces：产生的响应的类型



### 关于请求路径的问题

springmvc支持ant风格

- ?：任意的字符，斜杠除外
- *：0到n个字符都可以，斜杠除外
- `**`：支持任意层路径，   要写成/m3/`**`这样才可以体现出来，/m3`**`这样的效果等同于m3后面加任意多个字符（即单个*）



### @GetMapping、@PostMapping

- @GetMapping：限定了只支持get请求
- @PostMapping：限定了只支持post请求



### 对于非get、post请求的支持

需要有额外的内容添加，要增加一个过滤器来额外处理

- 过滤器
- 返回的不再是页面而是数据

```xml
<!--注册一个支持所有http请求类型的过滤器-->
<filter>
  <filter-name>hiddenHttpMethodFilter</filter-name>
  <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>hiddenHttpMethodFilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

- 表单提交里面还要添加一个隐藏的参数

```http
<form action="${ctx}/web/m5" method="post">
    <input type="hidden" name="_method" value="DELETE">
    <input type="submit" value="提交">
</form>
```

### @Responsebody

> 返回数据的，一般情况下返回JSON格式





### @InitBinder

数据转换使用，比如将日期进行转换处理





### @PathVariable

restful风格

> user/delete/288（原风格：user/delete?id=288）

```java
@GetMapping("/add/{id}/{name}/{price}")
public String addProduct(@PathVariable("id") Integer id, @PathVariable("name") String name, @PathVariable("price") Double price){

    System.out.println(id + name + price);
    return "forward";
}
```









### @ModelAttribute

使用方式一

```java
@Controller
@RequestMapping("/user2")
public class UserController2 {
//    就是在controller里面的任意一个处理具体的方法之前都会执行
    @ModelAttribute
    public User init(){
        System.out.println("init........");
        User u = new User();
        u.setName("aaa");
        return u;
    }
    @RequestMapping("/login")
    public String login(Model model){
        System.out.println(model.containsAttribute("u"));
        System.out.println(model.containsAttribute("user"));
        System.out.println(model.containsAttribute("udsfdsf"));
        return "msg";
    }
}
```

如果某些对象从头到尾每次请求当中都要存在，不消失，就适合这么用

使用方式二

```java
@ModelAttribute("user")
public void init(Model model) {
    System.out.println("init........");
    User u = new User();
    u.setName("aaa");
    model.addAttribute("user", u);
}
```



使用方式三

如果没有传递这个模型过来，那么方法上加了@ModelAttribute的为你提供，如果你传了就用你的。

```java
@ModelAttribute("user")
public void init(Model model) {
    System.out.println("init........");
    User u = new User();
    u.setName("aaa");
    model.addAttribute("user", u);
}

//    这种方式会直接去我们的模型里面去找
@RequestMapping("/login")
public String login(@ModelAttribute User user) {
    System.out.println(user.getName() + user.getPassword());
    return "msg";
}
```









### @SessionAttribute

要求当前这次访问当中的会话里面必须要有某个对象（检查）

```java
package person.jzh.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.SessionAttribute;
import org.springframework.web.bind.annotation.SessionAttributes;
import person.jzh.pojo.Dog;

@Controller
@RequestMapping("/dog")
@SessionAttributes("dog")
public class DogController {

    @RequestMapping("/register")
    public String register(Dog dog){
        return "dog";
    }


//   它是检查当前这个会话里面是否有dog
    @RequestMapping("/login")
    public String login(@SessionAttribute Dog dog){
        return "redirect:/jsp/dog.jsp";
    }
}
```





### @SessionAttributes

放在类上面，它会将模型自动填充到会话里面去（保存）

```java
package person.jzh.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.SessionAttributes;
import person.jzh.pojo.User;

@Controller
@RequestMapping("/user3")
@SessionAttributes("user")
public class UserController3 {

//    就是在controller里面的任意一个处理具体的方法之前都会执行
//    @ModelAttribute
//    public User init(){
//        System.out.println("init........");
//        User u = new User();
//        u.setName("aaa");
//        return u;
//    }
    @ModelAttribute("user")
    public void init(Model model) {
        System.out.println("init........");
        User u = new User();
        u.setName("aaa");
        model.addAttribute("user", u);
    }

//    这种方式会直接去我们的模型里面去找
    @RequestMapping("/login")
    public String login(@ModelAttribute User user) {
        System.out.println(user.getName() + user.getPassword());
        return "redirect:/jsp/user.jsp";
    }
//    @RequestMapping("/login")
//    public String login(Model model) {
//        System.out.println(model.containsAttribute("u"));
//        System.out.println(model.containsAttribute("user"));
//        System.out.println(model.containsAttribute("udsfdsf"));
//        return "msg";
//    }

}
```





### @RequestParam







### @RequestBody

json数据，不是通过form表单传递

ajax({

```
data:
```

})





## 关于静态资源访问的问题

------

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








## 关于post请求中文乱码问题

我们添加一个过滤器即可，springmvc提供了非常好的字符编码过滤器，所以我们注册即可

```xml
<!--中文乱码过滤器-->
<filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <!--指定字符编码-->
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceRequestEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```





## 关于form表单提交数据的方式

---

### 方式一 	通过属性名字绑定

通过属性名称进行绑定，可以完成数据传递

页面当中表单元素的name值要和后台的形参的名字保持一致

如果有多个形参，按名字(name)绑定即可

```java
package person.jzh.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.HashMap;
import java.util.Map;

@Controller
@RequestMapping("/user")
public class UserController {

    @PutMapping("/put")
//    需要额外的json包支持
    @ResponseBody
    public String put(String name,String password) {
        System.out.println(name + password);
//        Map<String, String> map = new HashMap<>();
//        map.put("msg", "ok");
//        return map;
        return "ok";
    }
}
```

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
put
<br/>
<form action="${ctx}/user/put" method="post">
    <input type="hidden" name="_method" value="put">
    <input type="text" name="name">
    <input type="password" name="password">
    <input type="submit" value="提交">
</form>
</body>
</html>
```



### 方式二	利用@RequestParam

jsp页面不变

后台

```java
@PutMapping("/put")
@ResponseBody
public String put(@RequestParam("name") String name, @RequestParam("password") String password) {
    System.out.println(name + password);
    return "ok";
}
```





### 方式三	直接使用pojo形式传递

jsp不变

后台

```java
@PutMapping("/put")
@ResponseBody
public String put(User user) {
    System.out.println(user.getName() + user.getPassword());
    return "ok";
}
```





## 关于form表单提交日期格式数据问题的处理

springmvc默认不能处理日期，需要自行另外处理

### 1、处理日期（没有时间）

可以直接在Controller里直接加

```java
@InitBinder("user")
public void init(WebDataBinder dataBinder){
//  这里指定什么格式，前台就只能输出什么形式
    SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
    simpleDateFormat.setLenient(false);
    dataBinder.registerCustomEditor(Date.class, new CustomDateEditor(simpleDateFormat, false));
}
@PutMapping("/put")
//  需要额外的json包支持
@ResponseBody
public String put(@ModelAttribute("user") User user) {
    System.out.println(user);
//  Map<String, String> map = new HashMap<>();
//  map.put("msg", "ok");
//  return map;
    return "ok";
}
//	通过initBinder指定了user名字和modelAttribute里面的user绑定
```



### 2、不指定名字，根据数据类型一样可以分析解析转换成功

```java
@InitBinder
public void init(WebDataBinder dataBinder){
//  这里指定什么格式，前台就只能输出什么形式
    SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
    simpleDateFormat.setLenient(false);
    dataBinder.registerCustomEditor(Date.class, new CustomDateEditor(simpleDateFormat, false));
}
@PutMapping("/put")
//  需要额外的json包支持
@ResponseBody
public String put(@ModelAttribute User user) {
    System.out.println(user);
//  Map<String, String> map = new HashMap<>();
//  map.put("msg", "ok");
//  return map;
    return "ok";
}
//	通过initBinder指定了user名字和modelAttribute里面的user绑定
```



### 3、时间+日期

```java
package person.jzh.controller;

import org.springframework.beans.propertyeditors.CustomDateEditor;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.WebDataBinder;
import org.springframework.web.bind.annotation.InitBinder;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import person.jzh.pojo.User;

import java.text.SimpleDateFormat;
import java.util.Date;

@Controller
@RequestMapping("/user")
public class UserController {

    @InitBinder
    public void init(WebDataBinder dataBinder){
//        这里指定什么格式，前台就只能输出什么形式
//        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        simpleDateFormat.setLenient(false);
        dataBinder.registerCustomEditor(Date.class, new CustomDateEditor(simpleDateFormat, false));
    }


    @PutMapping("/put")
//    需要额外的json包支持
    @ResponseBody
    public String put(User user) {
        System.out.println(user);
//        Map<String, String> map = new HashMap<>();
//        map.put("msg", "ok");
//        return map;
        return "ok";
    }
}
```





### 4、在pojo里的日期属性上可以加上注解@DateTimeFormat

```java
@DateTimeFormat(pattern = "yyyy-MM-dd")
// @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
private Date date;
```



## JSON数据交互

### 额外依赖

```xml
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.8</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.9.8</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-annotations -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-annotations</artifactId>
    <version>2.9.8</version>
</dependency>
<!-- https://mvnrepository.com/artifact/net.sf.json-lib/json-lib -->
<dependency>
    <groupId>net.sf.json-lib</groupId>
    <artifactId>json-lib</artifactId>
    <version>2.4</version>
    <classifier>jdk15</classifier>
</dependency>

<!-- 添加处理json为javabean -->
<!-- https://mvnrepository.com/artifact/org.codehaus.jackson/jackson-core-asl -->
<dependency>
    <groupId>org.codehaus.jackson</groupId>
    <artifactId>jackson-core-asl</artifactId>
    <version>1.9.13</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.codehaus.jackson/jackson-mapper-asl -->
<dependency>
    <groupId>org.codehaus.jackson</groupId>
    <artifactId>jackson-mapper-asl</artifactId>
    <version>1.9.13</version>
</dependency>

```





### JSON数据返回前台以及如何解析



#### JSON后台数据返回

1. 返回POJO

```java
@RequestMapping("/m1")
@ResponseBody   // 这个注解将知道现在返回的不是视图，它会将这个数据转换为JSON格式
public User m1() {
    User u = new User();
    u.setName("sd");
    u.setPwd("123123");
    return u;
}
```



2. 返回map

```java
@RequestMapping("/m2")
@ResponseBody   // 这个注解将知道现在返回的不是视图，它会将这个数据转换为JSON格式
public Map<String, Object> m2() {
    Map<String, Object> map = new HashMap<>();
    map.put("name", "sd");
    map.put("age", 28);
    return map;
}
```





3. 返回数组

```java
@RequestMapping("/m3")
@ResponseBody   // 这个注解将知道现在返回的不是视图，它会将这个数据转换为JSON格式
public User[] m3() {
    User u = new User();
    u.setPwd("123");
    u.setName("sd");
    User u2 = new User();
    u2.setPwd("12dd3");
    u2.setName("ssd");
    return new User[]{u,u2};
}
```



4. 返回list

```java
@RequestMapping("/m4")
@ResponseBody   // 这个注解将知道现在返回的不是视图，它会将这个数据转换为JSON格式
public List<User> m4() {
    List<User> l = new ArrayList<>();
    User u = new User();
    u.setPwd("123");
    u.setName("sd");
    User u2 = new User();
    u2.setPwd("12dd3");
    u2.setName("ssd");
    l.add(u);
    l.add(u2);
    return l;
}
```





#### JSON前台如何解析

1. 解析返回的POJO

```javascript
$(function () {
    $('#b1').click(function () {
        $.ajax({
            url: '${ctx}/json/m1',
            type: 'post',
            success: function (data) {
                alert(data.name);
            }
        })
    })
})
```

2. 返回Map

与上方一样



3. 返回数组

```javascript
$(function () {
    $('#b3').click(function () {
        $.ajax({
            url: '${ctx}/json/m3',
            type: 'post',
            success: function (data) {
                for(var i = 0 ; i < data.length ; i++){
                    alert(data[i].name);
                    alert(data[i].pwd);
                }
            }
        })
    })
})
```

4. 返回list

与上方的一致



5. 返回的List<Map<String,User>>

```js
$(function () {
    $('#b5').click(function () {
        $.ajax({
            url: '${ctx}/json/m5',
            type: 'post',
            success: function (data) {
                for(var i = 0 ; i < data.length ; i++){
                    alert(data[i].u1.name);
                    alert(data[i].u1.pwd);
                    alert(data[i].u2.name);
                    alert(data[i].u2.pwd);
                }
            }
        })
    })
})
```



### JSON数据如何使用Ajax提交到后台，后台如何解析

```
contentType:"application/json;charset=UTF-8"
```

1.前台写法

```js
$(function () {
    $('#b1').click(function () {
        var obj = {
            'name':'叶问',
            'pwd':'怕老婆'
        }
        $.ajax({
            url: '${ctx}/json1/add',
            type: 'post',
            contentType: 'application/json',
            data: JSON.stringify(obj),
            success: function (data) {
                // alert(data.name);
            }
        })
    })
})
```

2.后台写法

```java
//    前台如何提交一个User对象过来
@RequestMapping("/add")
//    User user 入参只能处理表单提交的数据
public String add(@RequestBody User user){
    System.out.println(user.getName()+user.getPwd());
    return "msg";
}
```

一定要记得添加@RequestBody，否则无法解析



对于form表单提交的数据，它的contentType是属于Content-Type:application/x-www-form-urlencoded

对于ajax发送JSON则是 application/json.



#### 1.发送一个pojo

前台

```js
$('#b1').click(function () {
    var obj = {
        'name':'叶问',
        'pwd':'怕老婆'
    }
    $.ajax({
        url: '${ctx}/json2/add',
        type: 'post',
        contentType: 'application/json',
        data: JSON.stringify(obj),
        success: function (data) {
            // alert(data.name);
        }
    })
})
```



后台

```java
//    前台如何提交一个User对象过来
@RequestMapping("/add")
//    User user 入参只能处理表单提交的数据
public String add(@RequestBody User user) {
    System.out.println(user.getName() + user.getPwd());
    return "msg";
}
```



#### 2.发送一组pojo到后台i

前台

```java
$('#b2').click(function () {
    var obj = {
        'name':'叶问',
        'pwd':'怕老婆'
    };
    var obj2 = {
        'name':'陈华顺',
        'pwd':'123'
    };
    var arr = new Array();
    arr.push(obj);
    arr.push(obj2);
    $.ajax({
        url: '${ctx}/json2/addList',
        type: 'post',
        contentType: 'application/json',
        data: JSON.stringify(arr),
        success: function (data) {
            alert(data.code);
        }
    })
})
```



后台

```java
//    前台如何提交一个User对象过来
@RequestMapping("/addList")
//    User user 入参只能处理表单提交的数据
public Map<String,Integer> add(@RequestBody List<User> list) {
    System.out.println(list);
    Map<String,Integer> map = new HashMap<>();
    map.put("code",2000);
    return map;
}
```







## XML数据交互

---

对于很多第三方开发，还是由很多会采用XML作为数据交互，比如微信

1. 添加依赖

```xml
<!--XML数据处理-->
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.dataformat/jackson-dataformat-xml -->
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
    <version>2.9.8</version>
</dependency>
```



2. 方法返回数据类型定义

```java
//    produces:描述生产的类型，返回类型的描述，返回什么数据
@RequestMapping(value = "/m1",produces = {MediaType.APPLICATION_ATOM_XML_VALUE})
@ResponseBody
public User m1(){
//        将数据转换为XML形式user
    User u = new User();
    u.setName("张福新");
    u.setPwd("好好学习，记得看书");
    return u;
}
```



如果我要实现如下效果可以吗？

```xml
<user name="haha">
	<pwd>123</pwd>
</user>
```







## 文件上传



### apache 上传组件方案

1. 添加依赖

```xml
<!--apache文件上传组件-->
<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.4</version>
</dependency>
```

2. 在springmvc当中要注册一个文件上传解析器

```xml
<!--文件上传解析器
    id必须是multipartResolver，原因是源代码里面写死了这个名字
-->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <!--定义最大上传的大小 总的 单位为bytes-->
    <property name="maxUploadSize" value="102400000000"></property>
    <!--定义上传的编码-->
    <property name="defaultEncoding" value="UTF-8"></property>
    <!--定义单个文件的上传大小-->
    <property name="maxUploadSizePerFile" value="2000000"></property>
</bean>
```

3. 准备上传的页面

```jsp
<%--
  Created by IntelliJ IDEA.
  User: jzh
  Date: 2019/4/17
  Time: 15:44
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%--添加一个 enctype="multipart/form-data"--%>
<form action="${ctx}/file/upload" method="post" enctype="multipart/form-data">
    ${ctx}
    文件：<input type="file" name="file"><br>
    <input type="submit" value="提交">
</form>
</body>
</html>
```

4. 后台代码

```java
package person.jzh.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.multipart.MultipartFile;

import java.io.File;
import java.io.IOException;
import java.util.Date;

@Controller
@RequestMapping("/file")
public class FileController {

    private static String uploadPath = "E:" + File.separator;

    //    入参就可以代表上传的文件
    @RequestMapping("/upload")
    public String upload(@RequestParam("file") MultipartFile multipartFile, Model model) {
//        1 传到哪里去 2 传什么东西 3 传的细节
//        1 判断
        if (multipartFile != null && !multipartFile.isEmpty()) {
//            不空才传
//            构建新的文件
//           2 获取原文件名字
            String originalFilename = multipartFile.getOriginalFilename();


//            3 先截取原文件的文件名前缀，不带后缀
            String fileNamePrefix = originalFilename.substring(0, originalFilename.lastIndexOf("."));

//            4 加工处理文件名，将原文件名+时间戳
            String newFileNamePrefix = fileNamePrefix + new Date().getTime();

//            5 得到新文件名
            String newFileName = newFileNamePrefix + originalFilename.substring(originalFilename.lastIndexOf("."));

//            6 构建文件对象
            File file = new File(uploadPath + newFileName);

//            7 上传
            try {
                multipartFile.transferTo(file);
                model.addAttribute("filename", newFileName);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return "uploadSuc";
    }
}
```



### 多文件上传

前台

```jsp
<%--
  Created by IntelliJ IDEA.
  User: jzh
  Date: 2019/4/17
  Time: 15:44
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%--多个文件上传--%>
<form action="${ctx}/file/upload2" method="post" enctype="multipart/form-data">
    ${ctx}
    文件：<input type="file" name="file"><br>
    文件：<input type="file" name="file"><br>
    文件：<input type="file" name="file"><br>
    文件：<input type="file" name="file"><br>
    文件：<input type="file" name="file"><br>
    <input type="submit" value="提交">
</form>
</body>
</html>
```

后台

```java
//    入参就可以代表上传的文件
@RequestMapping("/upload2")
public String upload2(@RequestParam("file") MultipartFile[] multipartFiles, Model model) {
//        1 传到哪里去 2 传什么东西 3 传的细节
//        1 判断
    List<String> fileNames = new ArrayList<>();
    if (multipartFiles != null && multipartFiles.length > 0){
//            遍历
        for(MultipartFile multipartFile : multipartFiles){
            //            不空才传
//            构建新的文件
//           2 获取原文件名字
            String originalFilename = multipartFile.getOriginalFilename();


//            3 先截取原文件的文件名前缀，不带后缀
            String fileNamePrefix = originalFilename.substring(0, originalFilename.lastIndexOf("."));

//            4 加工处理文件名，将原文件名+时间戳
            String newFileNamePrefix = fileNamePrefix + new Date().getTime();

//            5 得到新文件名
            String newFileName = newFileNamePrefix + originalFilename.substring(originalFilename.lastIndexOf("."));

//            6 构建文件对象
            File file = new File(uploadPath + newFileName);

//            7 上传
            try {
                multipartFile.transferTo(file);
                fileNames.add(newFileName);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    model.addAttribute("filenames",fileNames);

    return "uploadSuc2";
}
```



### servlet3.0新特性

```xml
<bean id="multipartResolver" class="org.springframework.web.multipart.support.StandardServletMultipartResolver">

</bean>
```

servlet

```xml
<servlet>
    <!--这里写的这个名字是有讲究的，
        如果我们不去修改spring配置文件默认的位置，那么
        它会去web-inf下面找一个叫做springmvc-servlet.xml的文件
    -->
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--<servlet-class>person.jzh.servlet.WebPathInitServlet</servlet-class>-->
    <!--<init-param>-->
    <!--&lt;!&ndash;可以重新生成配置文件的名字&ndash;&gt;-->
    <!--<param-name>namespace</param-name>-->
    <!--<param-value>mvc-servlet</param-value>-->
    <!--</init-param>-->
    <init-param>
        <!--上下文配置的位置的制定-->
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <multipart-config>
        <max-file-size>102400000000</max-file-size>
    </multipart-config>
    <!--<load-on-startup>2</load-on-startup>-->
</servlet>
```







## 文件下载

```java
package person.jzh.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.servlet.http.HttpServletResponse;
import java.io.File;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;

@Controller
@RequestMapping("/download")
public class DownloadController {

    //    定义一个文件下载的目录
    private static String parentPath = "E:" + File.separator;

    @RequestMapping("/down")
    public String down(HttpServletResponse response) {
        response.setCharacterEncoding("UTF-8");
//        通过输出流写到客户端，浏览器
//        1 获取文件下载名
        String fileName = "111.txt";

//        2 构建一个文件对象通过Paths工具类获取一个Path对象，NIO
        Path path = Paths.get(parentPath, fileName);

//        3 判断它是否存在
        if (Files.exists(path)) {
//            存在则下载
//            通过response设定它的响应类型
//            4 获取文件的后缀
            String fileSuffix = fileName.substring(fileName.lastIndexOf(".") + 1);

//            5 添加头信息
//            response.addHeader("contentType", );

//            6 设置contentType
            response.setContentType("application/" + fileSuffix);

            // ISO8859-1
            try {
                response.addHeader("Content-Disposition", "attachment;filename=" + new String(fileName.getBytes("utf-8"), "ISO8859-1"));
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }

//            7 通过Path写出去
            try {
                Files.copy(path, response.getOutputStream());
            } catch (IOException e) {
                e.printStackTrace();
            }

        }
        return "msg";
    }

}
```