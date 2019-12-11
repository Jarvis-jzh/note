# 拦截器

​	springmvc提供了拦截器，类似于过滤器，它将在我们的请求距离处理之前先做检查，有权决定接下来是否继续，还可以对我们的请求进行加工。拦截器，可以设计多个。

​	通过实现HandlerInterceptor，这是一个接口，定义了三个非常重要的方法

- 前置处理
- 后置处理
- 完成处理

## 案例一

通过拦截器实现方法耗时统计与警告。

后台

```java
package person.jzh.interceptors;

import org.apache.log4j.Logger;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 方法耗时统计的拦截器
 */
public class MethodTimerInterceptor implements HandlerInterceptor {

    private static final Logger LOGGER = Logger.getLogger(MethodTimerInterceptor.class);

    //    前置功能  开始到结束，两个点减法
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
//        1 定义开始时间
        long start = System.currentTimeMillis();
//        2 将其存到请求域当中
        request.setAttribute("start", start);
//        返回true，才会找下一个拦截器，如果没有下一个，则去Controller
//        记录请求日志
        LOGGER.info(request.getRequestURI() + ",请求到达");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
//        1 取出start
        long start = (long) request.getAttribute("start");
//        2 得到end
        long end = System.currentTimeMillis();
//        3 记录一下耗时
        long spendTime = end - start;
        if (spendTime >= 1000) {
            LOGGER.warn("方法耗时严重，请及时处理，耗时：" + spendTime + "毫秒");
        } else {
            LOGGER.info("方法耗时：" + spendTime + "毫秒，速度正常");
        }
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

    }
}
```



springMVC配置

```xml
<!--拦截器的配置-->
<mvc:interceptors>
    <mvc:interceptor>
        <!--
            /*的写法只能拦截/name的方法，只能有一层，不是多层拦截
        -->
        <mvc:mapping path="/**/*"/>
        <bean class="person.jzh.interceptors.MethodTimerInterceptor">

        </bean>
    </mvc:interceptor>
    <mvc:interceptor>
        <!--
            只想拦截/user/**/*
            还需要开放登陆权限
        -->
        <mvc:mapping path="/user/**/*"/>
        <!--排除登陆的这个URI-->
        <mvc:exclude-mapping path="/user/login"/>
        <bean class="person.jzh.interceptors.SessionInterceptor">

        </bean>
    </mvc:interceptor>
</mvc:interceptors>
```





## 案例二

会话拦截器，做用户检查

后台

```java
package person.jzh.interceptors;

import org.apache.log4j.Logger;
import org.springframework.web.servlet.HandlerInterceptor;
import person.jzh.pojo.User;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 *  会话拦截器
 */
public class SessionInterceptor implements HandlerInterceptor {

    private static final Logger LOGGER = Logger.getLogger(SessionInterceptor.class);

//    检查当前会话是否有User，如果有，放行，没有不放行
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        Object user = request.getSession().getAttribute("SESSION_USER");
        if (user == null){
            LOGGER.warn("您不具备权限，请先登陆");
            return false;
        }
        if (user instanceof User){
//            再去数据库检查其身份对不对
            User u = (User)user;
            u.setPwd(null);
            request.getSession().setAttribute("SESSION_USER",u);
            LOGGER.info(u.getName()+"处于登陆状态，可以执行操作");
            return true;
        }else{
            LOGGER.warn("请不要搞事，请先登陆");
            return false;
        }
    }
}
```

springMVC配置

```xml
<mvc:interceptor>
    <!--
        只想拦截/user/**/*
        还需要开放登陆权限
    -->
    <mvc:mapping path="/user/**/*"/>
    <!--排除登陆的这个URI-->
    <mvc:exclude-mapping path="/user/login"/>
    <bean class="person.jzh.interceptors.SessionInterceptor">

    </bean>
</mvc:interceptor>
```



## 拦截器执行顺序的问题

如果有N个拦截器，都能拦截到某个URI的时候，执行顺序问题

在SpringMVC当中拦截器定义的顺序是有关系的。

配在前面的优先拦截，按照顺序来

i1

i2

i3

前置处理是i1 i2 i3

后置处理是i3 i2 i1

倒过来





## 拦截器与过滤器的比较

### 相似

​	都有优先处理请求的权利，可以决定是否将请求转移到实际处理的控制器处，都可以对请求或者会话当中数据进行加工



### 不同

 - 拦截器可以做前置处理，也可以后置处理，还可以完成后处理，控制更加细一些，过滤器只负责前面的过滤的行为而已
 - 过滤器优先执行
    - 过滤器是servlet规范里面的组件
    - 拦截器都是框架自己额外添加的组件