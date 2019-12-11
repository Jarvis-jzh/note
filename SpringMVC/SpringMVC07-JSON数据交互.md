# JSON数据交互

## 额外依赖

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





## JSON数据返回前台以及如何解析



### JSON后台数据返回

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





### JSON前台如何解析

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



## JSON数据如何使用Ajax提交到后台，后台如何解析

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



### 1.发送一个pojo

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



### 2.发送一组pojo到后台i

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