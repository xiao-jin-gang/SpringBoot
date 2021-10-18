# MyBatis

```sql
USE mybatis;
create table `user`(
`id` int(20) NOT NULL PRIMARY KEY,
`name` VARCHAR(30) DEFAULT NULL,
`pwd` VARCHAR(30) DEFAULT NULL
)ENGINE=INNODB DEFAULT CHARSET=utf8;
```

# 1.搭建环境

## 1.maven依赖

```xml
 <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.21</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.7</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
```

## 2.创建一个模块

编写代码

resource===>> **mybatis-config.xml**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/zjz?serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <!-- 绑定mapper对应的xml文件-->
    <mappers>
        <mapper resource="UserDao.xml"/>
    </mappers>
</configuration>
```

resource=>mapper=>**UserDao.xml**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">


<mapper namespace="com.kuang.dao.UserDao">
    <select id="getUserList" resultType="com.kuang.pojo.User">
        select * from user
    </select>
</mapper>
```

## 3.创建Sqlsession工具类

```java
public class MyBatisUtils {

    //提升作用域
    private static SqlSessionFactory sqlSessionFactory;
    static {

        try {
            //获取sqlsessionfactory对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    
    /**
     * 既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。
     * 你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句
     **/
    public static SqlSession getSqlsession(){
        SqlSession session = sqlSessionFactory.openSession();
        return session;
        //return sqlSessionFactory.openSession(true);//自动提交commit
    }
}
```



## 4.测试

**注意点 MapperRegistry是什么？**

```java
public class UserDaoTest {
    @Test
    public void test(){
        //获取sqlsession对象
        SqlSession sqlsession = MyBatisUtils.getSqlsession();
        //获取接口
        UserDao mapper = sqlsession.getMapper(UserDao.class);
        //执行方法
        List<User> list = mapper.getUserList();
        System.out.println("***************");
        for (User user:list) {
            System.out.println(user);
        }
        sqlsession.close();
    }
}
```



## 5.非resource下xml资源过滤问题

```xml
<!--    在build中配置resources，来防止java文件夹中资源导出失败问题  xml文件在src下不生成，我们需要进行过滤-->
    <build>
        <resources>
            <resource>
                <directory>rc/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>
```

## 6.增删改查操作

```xml
<mapper namespace="com.kuang.dao.UserDao">
    <select id="getUserList" resultType="com.kuang.pojo.User">
        select * from user
    </select>

    <select id="getUserById" resultType="com.kuang.pojo.User" parameterType="int">
        select * from user where id=#{id}
    </select>
    
    <insert id="addUser" parameterType="com.kuang.pojo.User">
        insert into user(id,name,pwd) values (#{id},#{name},#{pwd});
    </insert>
    <update id="updateUser">
        update user set name =#{name},pwd=#{pwd} where id=#{id};
    </update>
    <delete id="deleteUser" parameterType="int">
        delete from user where id=#{id}
    </delete>
    <select>
    	select * from user where name like #{value}
    </select>
</mapper>
```

```java
@Mapper
public interface UserDao {

    List<User> getUserList();
    //根据Id查询用户
    User getUserById(Integer id);
    //添加一个用户
    int addUser(User user);
    //更新
    int updateUser(User user);
    //删除
    int deleteUser(int id);
}
```

```java
public void addUser(){
        SqlSession sqlsession = MyBatisUtils.getSqlsession();
        try{
            //获取接口
            UserDao mapper = sqlsession.getMapper(UserDao.class);
            //执行添加方法
            mapper.addUser(new User(1,"zzz","123"));
            //提交事务****************不要忘记
            sqlsession.commit();
            //**********************
        }
        catch (Exception e){
            e.printStackTrace();
        }
        finally {
            sqlsession.close();
        }
    }
```

## 7.万能Map

假设我们的实体类或者数据库中的表，字段或者参数过多，我们应当考虑使用Map

```xml
<insert id="addUser2" parameterType="map">
      insert into user(id,name) values (#{id},#{name});
 </insert>
```

```java
int addUser2(Map<String,Object> map);
```

```java
 public void addUser2(){
        SqlSession sqlsession = MyBatisUtils.getSqlsession();
        UserDao mapper = sqlsession.getMapper(UserDao.class);
        HashMap<String, Object> map = new HashMap<>();
        map.put("id",1);
        map.put("name","zzzzz");
        mapper.addUser2(map);
        sqlsession.commit();
        sqlsession.close();
    }
```

## 8.思考题

模糊查询怎么写？

java代码执行的时候，传递通配符 %李%

# 2.配置解析

## 1.核心配置文件

**mybatis-config.xml**

MyBatis的配置文件包含了会深深影响MyBatis行为设置和属性信息

## 2.环境配置（environments)

**不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

Mybatis默认的事务管理器就是JDBC,连接池：POOLED

## 3.属性（properties）

我们可以通过properties属性来实现引用配置文件

这些属性都是可外部配置且可动态替换的，既可以在典型的Java属性文件中配置，也可以通过properties元素的子元素来传递

db.properties

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/zjz?serverTimezone=UTC
username=root
password=123456
```

引入properties

```xml
<!--    引入外部配置文件-->
    <properties resource="db.properties"></properties>
```

## 4.类型别名

类型别名是为java类型设置一个短的名字

存在的意义仅在于用来减少类完全限定名的冗余

```xml
<!--给实体类起别名  实体类比较少推荐-->
    <typeAliases>
        <typeAlias type="com.kuang.pojo.User" alias="User"></typeAlias>
    </typeAliases>
```

```xml
<!--给实体类起别名 实体类比较多推荐（扫描到类默认是这个类的类名，首字母小写）-->
    <typeAliases>
        <package name="com.kuang.pojo" />
    </typeAliases>
```

第一种可以DIY别名，第二种不行，非要改 使用注解

## 5.设置

## 6.其他配置

## 7.映射器

**方式一：常用**resource

```xml
<mappers>
       <mapper resource="mapper/UserDao.xml"/>
</mappers>
```



**方式二：使用class文件绑定注册**

```xml
<mappers>
	<mapper class="com.kuang.dao.UserMapper"></mapper>
</mappers>
```

**注意点：**

**接口和他的Mapper配置文件必须同名！**

**接口和他的Mapper配置文件必须在同一个包下**

**方式三：使用扫描包进行注入绑定**

```xml
<mappers>
	<package name="com.kuang.dao"></package>
</mappers>
```

## 8.生命周期和作用域

生命周期和作用域是至关重要的，因为错误的使用会导致非常严重的并发问题。

**SqlsessionFactoryBuilder:**

一旦创建了SqlsessionFactory就不需要他了

局部变量

**SqlSessionFactory:**

说白了就是可以想象为：数据库连接池

SqlSessionFactory一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例

因此SqlSessionFactory的最佳作用域是应用作用域

最简单的就是使用单例模式或者静态单例模式

**SqlSession**

连接到连接池的一个请求！

SqlSession的实例不是线程安全的，因此是不能共享的，多以他的最佳作用域是请求或方法作用域。

用完之后需要赶紧关闭，否则资源被占用！

一个http一个sqlsession

# 3.解决属性名和字段名不一致的问题

解决方法：

## 2.resultMap

结果集映射

```xml
<!--    结果集映射数据库pwd java实体类password-->
    <resultMap id="UserMap" type="User">
        <result column="pwd" property="password"></result>
    </resultMap>

<select id="getUserById" resultType="UserMap" >
       select * from user where id=#{id}
</select>
```

resultMap元素是MyBatis中最重要最强大的元素

ResultMap 的设计思想是，对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了。

# 4.日志

## 1.日志工厂

如果一个数据库出现了异常，我们需要排错，日志就是最好的助手。 

在MyBatis中具体使用哪一个日志实现，在设置中设定！

```xml
<settings>
    <setting name="logImpl" value="LOG4J"/>
</settings>
```



## 2.Log4j

引入依赖

```xml
<dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.12</version>
        </dependency>
```

配置打印log4j.properties信息

```properties
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/kuang.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n

#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

mybatis-config.xml配置log4j日志工厂

```xml
<settings>
    <setting name="logImpl" value="LOG4J"/>
</settings>
```

**简单使用**

1.在要使用Log4j的类中，导入包import.org.apache.log4j.Logger;

2.日志对象，参数为当前类class

```java
//获得当前类的反射对象
static Logger logger= Logger.getLogger(UserDaoTest.class);
```

3.日志级别

```java
logger.info("info:进入了特殊tLog4j");
logger.debug("debug");
logger.error("error");
```

# 5.分页

思考：为什么要分页？

减少数据的处理量

**使用Limit分页**

```sql
select * from user limit startIndex,pageSize;
```

使用MyBatis实现分页，核心SQL

1.接口

```java
//分页
    List<User> getUserByLimit(Map<String,Object> map);
```



2.Mapper.xml

```xml
<select id="getUserByLimit" parameterType="map" resultType="user">
        select * from user limit #{startIndex},#{pageSize};
    </select>
```



3.测试

```java
@Test
    public void getlimit(){
        SqlSession sqlSession=MyBatisUtils.getSqlsession();
        UserDao mapper = sqlSession.getMapper(UserDao.class);
        HashMap<String, Object> map = new HashMap<>();
        map.put("startIndex",0);
        map.put("pageSize",2);
        List<User> list = mapper.getUserByLimit(map);
        for (User user:list) {
            System.out.println(user);
        }
        sqlSession.close();
    }
```

Mybatis分页插件PageHelper

# 6.注解开发

## 1.面向接口编程

根本原因：解耦 ，可扩展，提高复用，分层开发中，上层不用管具体的实现，大家都遵守共同的标准，是的开发变得更容易，规范性更好。

接口应用有两类：
第一类是对一个个体的抽象，它可对应为一个抽象体

第二类是对一个个体某一方面的抽象，即形成一个抽象面

一个个体可能有多个抽象面。抽象体和抽象面是有区别的。

## 2.注解开发 

```java
@Select("select * from user")
List<User> getUsers();
```

mybatis-config.xml 绑定接口

```xml
<!--没有xml文件就直接绑定类（接口）-->
<mappers>
	<mapper class="com.kuang.dao.UserMapper"></mapper>
</mappers>
```

 测试使用：//底层主要应用反射

底层：动态代理！

## 3.Mybatis执行流程

测试类：【我们必须要将接口绑定到我们的核心配置文件中mybatis-config.xml】

关于@Param()注解

*基本类型的参数或者String类型，需要加上

*引用类型不需要加

*如果只有一个基本类型的话，可以忽略，但是建议加上



