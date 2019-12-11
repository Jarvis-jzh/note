

# 动态SQL

​	MyBatis的强大特性之一便是它的动态SQL。如果你有使用JDBC或者其他类似的框架的经验，你就能体会到根据不同条件拼接SQL语句的痛苦。例如拼接时要确保不能忘记添加必要的空格，还要注意去掉列表最后一个列名的逗号。利用动态SQL这一特性可以彻底摆脱这种痛苦。

​	Mybatis3大大精简了元素种类，现在只需要学习原来一半的元素便可。MyBatis采用功能强大的基于OGNL的表达式来淘汰其它大部分元素。

看看我们的四大护法：

- if
- choose(when,otherwise)
- trim (where,set)
- foreach

很好很强大，我们一个个搞



## if

​	判断，典型应用就是根据传入的值要做不同处理，对于一个if要判断多个条件一起的话使用**and**连接即可。

官方例子：

```xml
<select id="findActiveBlogWithTitleLike"
     resultType="Blog">
  SELECT * FROM BLOG 
  WHERE state = ‘ACTIVE’ 
  <if test="title != null">
    AND title like #{title}
  </if>
</select>
```

​	如果标题不为空，才使用标题作为条件进行判断

​	对于连接多个条件的问题

```xml
<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’ 
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```

​	同时判断author与author的名字



## choose

- when

- otherwise

​	当某某的时候 ，否则，与switch结构是一个套路。

官例：

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

当标题不为空，标题将作为一个查询条件

当作者不为空，作者的名称不为空，才使用作者名称作为查询条件

如果都不满足，使用feartured = 1 作为查询条件



## trim

- where
  - 处理多余的and（or），且and必须在前缀
- set
  - 处理前缀后缀，前缀重载符，后缀重载符

官例：

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG 
  WHERE 
  <if test="state != null">
    state = #{state}
  </if> 
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```

​	如果state为空，title为空，author为空，SQL就会成为这个样子
```sql
SELECT * FROM BLOG
where
```

典型的词法错误。

​	并且，如果state为空，第二项不为空引发的问题是SQL为：

```sql
SELECT * FROM BLOG
WHERE
AND title like 'someTitle'
```

多了and

​	针对这些情况，mybatis专门设计了where标签来解决，*where* 元素只会在至少有一个子元素的条件返回 SQL 子句的情况下才去插入“WHERE”子句。而且，若语句的开头为“AND”或“OR”，*where* 元素也会将它们去除。

​	如果 *where* 元素没有按正常套路出牌，我们可以通过自定义 trim 元素来定制 *where* 元素的功能。比如，和 *where* 元素等价的自定义 trim 元素为：

```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ... 
</trim>
```

​	*prefixOverrides* 属性会忽略通过管道分隔的文本序列（注意此例中的空格也是必要的）。它的作用是移除所有指定在 *prefixOverrides* 属性中的内容，并且插入 *prefix* 属性中指定的内容。

​	类似的还有suffix和suffixOverrides，这两个分别处理后缀和后缀重载符。





## foreach

- collection 描述集合list,set,map
- open 是后面的SQL语句的拼接以什么开头
- close 以什么结尾
- separator item之间的分隔符
- item 代表集合中的元素，它将作为表达式被引用
- index 如果需要标号（序号），也可以使用

官例：

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```

实例：

```java
List<Addresses> queryByIds(List<Integer> list);
```

```xml
<select id="queryByIds" resultType="person.jzh.pojo.Addresses">
        select * from addresses
        <where>
            addr_id in
            <foreach item="item" index="index" collection="list"
                     open="(" separator="," close=")">
              #{item}+#{index}
            </foreach>
        </where>
    </select>a
```





## bind

​	模糊查询的时候，我们可以从OGNL当中创建一个变量绑定到上下文当中

官例：

```xml
<select id="selectBlogsLike" resultType="Blog">
  <bind name="pattern" value="'%' + _parameter.getTitle() + '%'" />
  SELECT * FROM BLOG
  WHERE title LIKE #{pattern}
</select>
```

pattern是由title和%组成的，这个时候就可以使用模糊查询了。





## 作业

产品表为例完成今天所有的知识

- if
- choose

  - when
  - otherwise
- where
- set
- foreach
- trim
- sql
- include
- bind
- cache



产品表设计如下：

- 产品ID
- 产品名称
- 产品进货价格
- 产品销售价格
- 产品上架时间
- 产品状态（1 已上架，2  已下架）