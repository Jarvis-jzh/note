# 动态SQL

## 获取连接

> ``` java
>  	//获取myutil的连接源	
> 		SqlSession sqlSession = myutil.getSession();
>      //mapper包里面的mapper  
> 		AddstudentMapper addstudentMapper = sqlSession.getMapper(AddstudentMapper.class);
>        
> ```
>





## mybatis的sql

里面的标签<set></set>、<where></where>、<choose></choose>、<trim></trim>

![1553139308998](assets/1553139308998.png)



<choose>          == ----->if  else

​	<when test="----">	

​		---

​	</when>

​	<when test="----">

​		-----

​	</when>	

​	<otherwise>

​		------

​	</otherwise>

</choose>



<trim>、</trim>用法：

![1553141228832](assets/1553141228832.png)





<foreach collection="list" open="(" close=")" item="item" separator="," index="index">

</foreach>---->子查询

![1553215697909](assets/1553215697909.png)

![1553215492366](assets/1553215492366.png)







模糊查询：



![1553215192006](assets/1553215192006.png)

查询一：在应用程序层面加入%%拼接

![1553216277962](assets/1553216277962.png)



查询二：通过mysql的函数完成concat也可以

![1553216359320](assets/1553216359320.png)

查询三:通过通过bind标签，对我们的变量重新进行绑定，然后通过新绑定的变量进行应用即可

![1553216984257](assets/1553216984257.png)





​	select * from .....where .....like （select.........）


```java
<select id="listAll" resultType="Addresses">
    select
    	<include refid="baseColumn">
     from addresses       
</select>
```

查询四：

<sql id= "baseColumn">

​	country、state、city

<sql>

![1553217774321](assets/1553217774321.png)

![1553217701694](assets/1553217701694.png)

