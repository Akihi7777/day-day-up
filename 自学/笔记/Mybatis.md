# Mybatis

```mysql
 <select id="getUserById" resultType="com.hwt.pojo.User" parameterType="int">
        select * from mybatis.user where id = #{id}
</select>
```

## 增删改

```mysql
<insert id="addUser" parameterType="com.hwt.pojo.User">
    insert into mybatis.user (id,name,pwd) values (#{id},#{name},#{pwd});
</insert>
```

## 参数的传递

### 使用Map传递参数

将参数封装到一个Map对象中，然后在SQL语句中通过键名获取对应的值。

```java
Map<String, Object> params = new HashMap<>();
params.put("courseId", 123);

mapper.selectByCourseId(params);
```

在Mapper XML文件中，可以直接通过**键名**获取参数的值：

```java
parameterType="java.util.Map"
```

### 使用对象传递参数

创建一个Java对象，将需要的参数封装到对象的属性中，然后在SQL语句中直接引用对象的属性

```java
public class MyParam {
    private Long courseId;
    // 其他属性和对应的getter/setter方法
}

MyParam param = new MyParam();
param.setCourseId(123);

teachplanMediaMapper.selectByCourseId(param);
```

```xml
parameterType="com.example.MyParam"
```

### 使用@Param注解传递多个参数

当需要传递多个参数时，可以使用@Param注解为每个参数指定一个名称，在SQL语句中使用这些参数名称。

```java
teachplanMediaMapper.selectByMultipleParams(@Param("param1") Long courseId, @Param("param2") String name);
```

在Mapper XML文件中，可以通过`param1`和`param2`引用这些参数：

```xml
<select id="selectByMultipleParams" parameterType="java.lang.Long" resultType="com.example.TeachplanMedia">
    SELECT * FROM teachplan_media
    WHERE course_id = #{param1} AND name = #{param2}
</select>
```

## 查询

### 占位符

\#{value}和${value}的区别：

- `#{value}` 是预编译特性的占位符语法，更安全，可以防止SQL注入攻击，能够处理参数类型转换和安全性验证。
- `${value}` 是简单的文本替换语法，通常用于替换列名、表名、表达式等固定部分的场景，不可以防止SQL注入攻击，存在潜在的安全风险。

```xml
<select id="getUser" parameterType="java.lang.String" resultType="com.example.User">
    SELECT * FROM ${tableName} WHERE username = '${username}'
</select>
```

#### sql注入

SQL注入（SQL Injection）是一种常见的安全漏洞，发生在应用程序中对用户输入数据的不正确处理，使得恶意用户能够通过**构造特定的输入**来修改应用程序的SQL语句，从而执行未经授权的数据库操作。

${value}是简单的文本替换语法，它在SQL语句中直接将参数值作为字符串进行替换，而没有进行任何预编译或安全性验证。这就导致${value}无法有效防止SQL注入攻击的原因

### 条件查询

#### QueryWrapper

##### 使用方法：

```java
// 查询条件构造器
QueryWrapper<BannerItem> wrapper = new QueryWrapper<>();
wrapper.eq("banner_id", id);
// 查询操作
List<BannerItem> bannerItems = bannerItemMapper.selectList(wrapper);
```

缺点：数据库中的字段名需要硬编码写在程序中（如"banner_id"）

##### 查询条件

**eq**：等于条件、**ne**：不等于条件、**gt**：大于条件、**ge**：大于等于条件、**lt**：小于条件、**le**：小于等于条件、**like**：模糊查询条件、**notLike**：不包含关键字的模糊查询条件、左模糊查询条件、右模糊查询条件、**isNull**：字段为 NULL 的条件、**isNotNull**：字段不为 NULL 的条件、**in**：包含在给定集合中的条件、notIn：不包含在给定集合中的条件、**between / notBetween**：范围条件、orderBy：排序条件（Asc或Desc）、**group by**：分组条件、**having**：分组后的条件、**notExists**：不存在子查询条件

apply：自定义 SQL 片段

```java
queryWrapper.apply("column_name = {0}", value);
```

nested：嵌套条件

```java
queryWrapper.nested(i -> i.eq("column1", value1).or().eq("column2", value2));
```

or：或条件

```java
queryWrapper.or(i -> i.eq("column1", value1).eq("column2", value2));
```

and：与条件

```java
queryWrapper.and(i -> i.eq("column1", value1).eq("column2", value2));
```

exists：存在子查询条件

```java
wrapper.exists("SELECT 1 FROM other_table ot WHERE ot.column_name = table_name.column_name");
```

last：拼接 SQL 最后部分

```java
wrapper.last("LIMIT 10");
```

#### LambdaQueryWrapper

使用方法：

```java
LambdaQueryWrapper<BannerItem> wrapper = new LambdaQueryWrapper<>();
wrapper.eq(BannerItem::getBannerId, id);
List<BannerItem> bannerItems = bannerItemMapper.selectList(wrapper);
```

- 链式查询

```java
List<BannerItem> bannerItems = new LambdaQueryChainWrapper<>(bannerItemMapper)
                        .eq(BannerItem::getBannerId, id)
                        .list();
```

## 结果映射

### resultType

resultType直接表示返回类型，一般用于程序中有对应于查询结果的数据类型或对象

### resultMap

当提供的返回类型是resultMap时，因为Map不能很好表示领域模型，就需要自己再进一步的把它转化为对应的对象，这常常在复杂查询中很有作用。

#### 简单查询

```xml
<resultMap type="Blog" id="BlogResult">
    
<!--  colum数据库中的字段，property实体类中的属性-->
<id column="id" property="id"/>
<result column="title" property="title"/>
<result column="content" property="content"/>
<result column="owner" property="owner"/>
</resultMap>

<select id="selectBlog" parameterType="int" resultMap="BlogResult">
select * from t_blog where id = #{id}
</select>
```

Column属性表示从数据库中查询的属性，Property则表示查询出来的属性对应的值赋给实体对象的哪个属性

#### 复杂查询

##### 一对一

```java
public class Blog {
    private int id;
    private String title;
    private String content;
    private String owner;
    private List<Comment> comments;
}
public class Comment {
    private int id;
    private String content;
    private Date commentDate = new Date();
    private Blog blog;
}
```

来自CommentMapper.xml文件：

```xml
<resultMap type="Comment" id="CommentResult">
<association property="blog" select="selectBlog" column="blog" javaType="Blog"/>
</resultMap>

<select id="selectComment" parameterType="int" resultMap="CommentResult">
select * from t_Comment where id = #{id}
</select>

<select id="selectBlog" parameterType="int" resultType="Blog">
select * from t_Blog where id = #{id}
</select>
```

上面的**association**子节点中，**property**属性指定了Java对象中的属性（或字段）名称，对于上面的例子就是Comment管理的blog属性；**select**表示进行哪个select映射来映射对应的关联属性，即会去请求id为select所对应的值的select映射 来查询出其所关联的属性对象；**column**指定了数据库查询结果集中的列名，用于表示该列的值将被映射到Java对象的哪个属性；**javaType**表示当前关联对象在JAVA中的类型

##### 一对多

来自BlogMapper.xml文件：

```xml
<resultMap type="Blog" id="BlogResult">
   <id column="id" property="id"/>
   <collection property="comments" select="selectCommentsByBlog" column="id" ofType="Comment"></collection>
</resultMap>

<resultMap type="Comment" id="CommentResult">
	<association property="blog" javaType="Blog" column="blog" select="selectBlog"/>
</resultMap>

<select id="selectBlog" parameterType="int" resultMap="BlogResult">
	select * from t_blog where id = #{id}
</select>

<select id="selectCommentsByBlog" parameterType="int" resultMap="CommentResult">
  select * from t_Comment where blog = #{blogId}
</select>
```

因为一个Blog可以有很多Comment，该comments为一个集合，所以用集合**collection**进行映射；**ofType**也是表示返回类型，这里的返回类型是集合内部的类型，之所以用ofType而不是用type是MyBatis内部为了和关联association进行区别。

