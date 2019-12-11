# 注解开发模式

	基于实现接口的方式已经是过去式了，采用注解开发很简单

基本注解：

- @Controller
- @RequestMapping



## 开发步骤

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





## 转发与重定向

- 转发到页面  默认到选项
- 重定向到页面    redirect:path
- 转发到另外一个控制器   forward:path





## 关于springmvc访问web元素

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

	data:

})