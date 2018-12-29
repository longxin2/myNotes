# myNotes
### 1.2 02三层架构和ssm框架的对应关系

- 主题：框架是什么？java项目开发一般分几层架构？对应都有什么框架？
- 内容：

```
什么是框架？
	它是我们软件开发的一套解决方案，不同的框架解决的是不同的问题。
	使用框架的好处：
		框架封装了很多的细节，使开发者可以使用极简的方式实现功能，大大提高效率。
	
java项目开发一般分为三层架构：
	表现层：
		用于展现数据
	业务层：
		处理业务需求
	持久层：
		和数据库交互
```

![01三层架构](img\01三层架构.png)

### 1.3 03jdbc操作数据库的问题分析

- 主题：jdbc回顾和问题分析
- 内容：

```
jdbc回顾：
	代码：参考pdf文档。
	
问题分析：
	1、数据库链接创建、释放频繁造成系统资源浪费从而影响系统性能，如果使用数据库链接池可解决此问题。
	2、Sql 语句在代码中硬编码，造成代码不易维护，实际应用 sql 变化的可能较大，sql 变动需要改变 java代码。
	3、使用 preparedStatement 向占有位符号传参数存在硬编码，因为 sql 语句的 where 条件不一定，可能多也可能少，修改 sql 还要修改代码，系统不易维护。
	4、对结果集解析存在硬编码（查询列名），sql 变化导致解析代码变化，系统不易维护，如果能将数据库记录封装成 pojo 对象解析比较方便。
```



### 1.4 04mybatis概述

- 主题：mybatis是什么？
- 内容：

```
mybatis的概述：
	mybatis是一个持久层框架，用java编写的。
	它封装了jdbc操作的很多细节，使开发者只需要关注sql语句本身，而无需关注注册驱动，创建连接等繁琐过程。

ORM：
	Object Relational Mapping 对象关系映射
	简单的说：
		就是把数据库表和实体类及实体类的属性对应起来
		让我们可以操作实体类就实现操作数据库表
	如：
		User实体类			   user表
		id					 userId
		user_name			 userName
		
		user.setId(rs.getInt("userId"));
		setId(rs.getInt("userId"));
```



## 第二节课

### 2.1 05mybatis环境搭建-前期准备

- 主题：搭建mybatis环境前期需要做什么
- 内容：

```
1、导入数据库数据：
	mysql数据库运行资料下 mybatisdb.sql 文件。
2、创建maven的java工程
3、在pom.xml配置文件加入依赖：
	<dependencies>
    <!--mybatis框架依赖-->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.4.5</version>
    </dependency>
    <!--单元测试-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.10</version>
        <scope>test</scope>
    </dependency>
    <!--mysql连接-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.6</version>
        <scope>runtime</scope>
    </dependency>
    <!--日志-->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.12</version>
    </dependency>
</dependencies>
```



### 2.2 06mybatis的环境搭建

- 主题：搭建mybatis环境的步骤
- 内容：

```
第一步：
	创建maven工程并导入坐标
第二步：
	创建User实体类、IUserDao接口
第三步：
	创建mybatis核心配置文件（SqlMapConfig.xml）
第四步：
	创建映射配置文件（IUserDao.xml）
```



### 2.3 07环境搭建的注意事项

- 主题：搭建mybatis配置环境有哪些注意事项？
- 内容：

```
一：
	创建 IUserDao.xml 和 IUserDao 接口时名称为了和我们之前的知识保持一致。也可以叫做 IUserMapper 等等。所以 IUserDao 和 IUserMapper 是一样的。

二：
	在idea的maven项目中的resources文件夹映射配置文件创建目录的时候要创建为 com/itheima/dao 而不是 com.itheima.dao。 com/itheima/dao 是多层文件夹，而 com.itheima.dao 只是一层。
	
三：
	mybatis的映射配置文件路径位置可以跟dao接口的包结构不一致，但是建议一致。
	
四：
	映射配置文件的mapper标签namespace属性的取值必须是dao接口的全限定类名。
	
五：
	映射配置文件的操作配置（select标签），id属性必须是dao接口的方法名，resultType必须是该方法的返回值的全限定类名或者是返回值泛型的全限定类名。
```



## 第三节课

### 3.1 08mybatis的入门

- 主题：使用mybatis并查询出所有用户
- 内容：

```java
public class MyBatisTest {
    /**
     * 测试mybatis的环境搭建
     */
    public static void main(String[] args) throws IOException {
        // 1.读取配置文件
        InputStream in = Resources.getResourceAsStream("sqlMapConfig.xml");
        // 2.根据配置文件构建SqlSessionFactory
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(in);
        // 3.使用SqlSessionFactory创建SqlSession
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 4.使用SqlSession构建Dao的代理对象
        IUserDao userDao = sqlSession.getMapper(IUserDao.class);
        // 5.执行dao的findAll方法
        List<User> list = userDao.findAll();
        for (User user : list) {
            System.out.println(user);
        }
        // 6.关闭资源
        sqlSession.close();
        in.close();
    }
}
```



### 3.2 09mybatis注解开发和编写dao实现类的方式

- 主题：其他两种使用mybatis的方式
- 内容：

```
注解方式：
	1、在dao接口的findAll()方法上加入@Select("select * from user")注解。
	2、把<mapper resource="com/itheima/dao/IUserDao.xml"/>换成<mapper class="com.itheima.dao.IUserDao"/>，然后把IUserDao.xml映射配置文件删除。
	注意：
		如果映射配置文件目录结构和dao接口包结构一致，必须把映射配置文件删除，否则一样能加载到导致重复。
		
dao实现类方式：
	1、编写dao接口的实现类，SqlSessionFactory通过实现类的构造方法传入，用于获取session。
	2、findAll()方法使用session.selectList("com.itheima.dao.IUserDao.findAll")进行查询所有。
	注意：
		selectList方法中写的是映射配置文件中mapper标签的namespace中的值加上select标签中id的值。
```



## 第四节课

### 4.1 10mybatis入门案例中的设计模式分析

- 主题：mybatis入门案例中都用了那些设计模式
- 内容：

```
1、构建者模式：
	构建SqlSessionFactory对象时使用。
	把构建对象的细节给封装了，使用构建者对象去获取要构建的对象，从而使开发者不再关心构建对象的细节。
2、工厂模式：
	创建SqlSession对象时使用。
	把创建对象的方式由new改为使用工厂生产，从而减少代码中的硬编码，降低类之间的依赖关系（耦合），提高程序的灵活性。
3、代理模式（JDK动态代理）：
	生成Mapper（Dao）代理对象时使用。
	通过创建dao接口的代理对象，为我们实现dao的功能。用的是动态代理。
```

![入门案例的分析](img\入门案例的分析.png)

### 4.2 11自定义Mybatis的分析-执行查询所有分析

- 主题：mybatis入门案例执行查询所有的分析
- 内容：

```
图解。
```

![查询所有的分析](img\查询所有的分析.png)

## 第五节课

### 5.1 12自定义Mybatis的分析-创建代理对象的分析

- 主题：创建Mapper（Dao）代理对象的分析
- 内容：

```
图解。
```

![创建代理对象的分析](img\创建代理对象的分析.png)

### 5.2 13自定义mybatis的编码-根据测试类中缺少的创建接口和类

- 主题：去掉mybatis依赖，缺少的创建接口和类补充完整
- 内容：

```
1、创建Resources类，并编写getResourceAsStream静态方法。
2、创建SqlSessionFactoryBuilder类，并编写build方法。
3、创建SqlSessionFactory接口，并编写openSession方法。
4、创建SqlSession接口，并编写getMapper方法和close方法。
```

![代码分析](img\代码分析.png)

### 5.3 14自定义mybatis的编码-解析XML的工具类介绍

- 主题：解析XML的工具类介绍
- 内容：

```
1、在资料下面把 XMLConfigBuilder 类复制到项目中。
2、创建 Configuration 类，用于存放数据库连接信息。
3、创建 Mapper 类，用于存放执行的SQL语句和返回结果类型的全限定类名。
4、Configuration 类中加入 Map 对象，key 用于存放接口namespace 的值加上 select 标签中 id 的值，value 为Mapper 对象。
```



## 第六节课

### 6.1 15自定义Mybatis的编码-创建两个默认实现类并分析类之间的关系

- 主题：创建两个默认实现类并分析类之间的关系
- 内容：

```
1、创建SqlSessionFactory接口的实现类DefaultSqlSessionFactory。
2、创建SqlSession接口的实现类DefaultSqlSession。
```



### 6.2 16自定义Mybatis的编码-实现基于XML的查询所有操作

- 主题：实现基于XML的查询所有操作
- 内容：

```
1、在资料下面把Executor类复制到项目中。
2、补充解析xml配置文件以及动态代理生成Mapper代理对象的方法。
```



### 6.3 17自定义Mybatis的编码-实现基于注解配置的查询所有

- 主题：实现基于注解配置的查询所有
- 内容：

```
1、把<mapper resource="com/itheima/dao/IUserDao.xml"/>换成<mapper class="com.itheima.dao.IUserDao"/>，然后把IUserDao.xml映射配置文件删除。
2、编写@Select注解类
3、放开XMLConfigBuilder类用于解析注解的方法逻辑。
4、测试查询结果。
```


