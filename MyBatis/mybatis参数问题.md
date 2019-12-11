# mybatis参数问题

## 单个基本数据类型

```java
Girl queryById(int id);
```

​	对于mybatis而言，如果仅仅是简单的一个单值传入 ，那么#{}表达式里面随便写什么都可以，只在一个参数，mybatis没有入参绑定的烦恼。建议还是写一个有含义的名称。



## 单个非基本数据类型

```java
Girl queryByName(String name)
```



## 多个基本或非基本数据类型

```java
Girl queryByNameFlower(String name,String flower)
```





## 单个JavaBean

```java
Girl query(Girl girl)
```

```xml
<select id="queryByAB" resultType="Girl">
	select * from girl where last_name = #{name} and flower = #{flower}
</select>
```

默认通过javabean里面的名称去引用，其实就是通过getter方法去找这些值

提供了get,set方法的叫属性



## MAP

```java
Girl query(Map<String,Object> map)
```

```xml
<select id="queryByAB" resultType="Girl">
	select * from girl where last_name = #{name} and flower = #{flower}
</select>
```

按照这种方式封装，就是按照键的名称进行取值



## 多个JavaBean

```java
Girl queryByAB(@Param("a") A a, @Param("b") B b)
```

```xml
<select id="queryByAB" resultType="Girl">
	select * from girl where last_name = #{a.name} and flower = #{b.flower}
</select>
```





## 问题

> Unknown column 'name' in 'where clause'

解决：

- SQL语句的列名写错了



> org.apache.ibatis.exceptions.TooManyResultsException:Expected one result (or null) to be returned by selectOne(), but found : 2

解决：

- 我们所要求的结果是一个对象，而查询返回为两个，这个时候不匹配。
- 只要不是按照主键查询，结果都可能不止一个



> org.apache.ibatis.binding.BindingException:Parameter 'name' not found. Available parameters are [arg1,arg0,param1,param2]

解决：

- 某个参数没有找到，可用的参数仅仅是：

  - arg1,arg0 ,param1, param2

  - ```java
    Girl queryByNameFlower(String name,String flower)
    ```

  - ```sql
    select * from girl where last_name = #{param1} and flower = #{param2}
    ```

- 第二种方法，推荐使用

  - ```java
    Girl queryByNameFlower(@Param("name") String name,@Param("flower") String flower);
    ```

  - ```sql
    select * from girl where last_name = #{name} and flower = #{flower}
    ```

