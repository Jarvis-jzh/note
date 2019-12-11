# 关于form表单提交数据的方式

---

## 方式一 	通过属性名字绑定

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



## 方式二	利用@RequestParam

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





## 方式三	直接使用pojo形式传递

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





# 关于form表单提交日期格式数据问题的处理

springmvc默认不能处理日期，需要自行另外处理

## 1、处理日期（没有时间）

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



## 2、不指定名字，根据数据类型一样可以分析解析转换成功

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



## 3、时间+日期

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





## 4、在pojo里的日期属性上可以加上注解@DateTimeFormat

```java
@DateTimeFormat(pattern = "yyyy-MM-dd")
// @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
private Date date;
```