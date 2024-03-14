# MyBatis中接口代理机制及使用

使用mybatis获取dao接口代理对象

```java
AccountDao accountDao = (AccountDao)sqlSession.getMapper(AccountDao.class);
```

使用以上代码的前提是：**AccountMapper.xml文件中的namespace必须和dao接口的全限定名称一致，id必须和dao接口中方法名一致。**

# 小技巧

## #{}与${}

\#{}：先编译sql语句，再给占位符传值，底层是PreparedStatement实现。可以防止sql注入，比较常用。

${}：先进行sql语句拼接，然后再编译sql语句，底层是Statement实现。存在sql注入现象。只有在需要进行sql语句关键字拼接的情况下才会用到。

什么情况下必须使用${}？

当需要进行sql语句关键字拼接的时候。必须使用${}，例如对数据进行升序和降序排序，模糊查询，批量删除

## typealiases

我们来观察一下CarMapper.xml中的配置信息：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="account">
    <select id="selectByActno" resultType="com.xun.bank.pojo.Account">
        select id,actno,balance from bank where actno = #{actno};
    </select>
    <update id="update">
        update bank set balance = #{balance} where actno =#{actno};
    </update>

</mapper>
```

resultType属性用来指定查询结果集的封装类型，这个名字太长，在mybatis-config.xml文件中使用typeAliases标签来起别名，包括两种方式：

```xml
<typeAliases>
  <typeAlias type="com.xun.bank.pojo.Account" alias="Account"/>
</typeAliases>
```

- 首先要注意typeAliases标签的放置位置，如果不清楚的话，可以看看错误提示信息。

- typeAliases标签中的typeAlias可以写多个。

- typeAlias：

  - type属性：指定给哪个类起别名

  - alias属性：别名。

    - alias属性不是必须的，如果缺省的话，type属性指定的类型名的简类名作为别名。

    - alias是大小写不敏感的。也就是说假设alias="Car"，再用的时候，可以CAR，也可以car，也可以Car，都行。

```xml
<typeAliases>
  <package name="com.xun.bank.pojo"/>
</typeAliases>
```

如果一个包下的类太多，每个类都要起别名，会导致typeAlias标签配置较多，所以mybatis用提供package的配置方式，只需要指定包名，该包下的所有类都自动起别名，别名就是简类名。并且别名不区分大小写。

## mappers

SQL映射文件的配置方式包括四种：

- resource：从类路径中加载，所以这种方式要求SQL映射文件必须放在resources目录下或其子目录下。

  ```xml
  <mappers>
    <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
    <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
    <mapper resource="org/mybatis/builder/PostMapper.xml"/>
  </mappers>
  ```

  

- url：从指定的全限定资源路径中加载，这种方式显然使用了绝对路径的方式，这种配置对SQL映射文件存放的位置没有要求，随意。

  ```xml
  <mappers>
    <mapper url="file:///var/mappers/AuthorMapper.xml"/>
    <mapper url="file:///var/mappers/BlogMapper.xml"/>
    <mapper url="file:///var/mappers/PostMapper.xml"/>
  </mappers>
  ```

  

- class：使用映射器接口实现类的完全限定类名

  - 如果使用这种方式必须满足以下条件：
    - SQL映射文件和mapper接口放在同一个目录下。
    - SQL映射文件的名字也必须和mapper接口名一致。

  ```xml
  <!-- 使用映射器接口实现类的完全限定类名 -->
  <mappers>
    <mapper class="org.mybatis.builder.AuthorMapper"/>
    <mapper class="org.mybatis.builder.BlogMapper"/>
    <mapper class="org.mybatis.builder.PostMapper"/>
  </mappers>
  ```

  将CarMapper.xml文件移动到和mapper接口同一个目录下：

  - 在resources目录下新建：com/xun/bank/mapper【这里千万要注意：**不能这样新建 com.xun.bank.mapper**】

  - 将CarMapper.xml文件移动到mapper目录下

  - 修改mybatis-config.xml文件

    ```xml
    <mappers>
      <mapper class="com.xun.bank.mapper.CarMapper"/>
    </mappers>
    ```

    

- package：将包内的映射器接口实现全部注册为映射器，如果class较多，可以使用这种package的方式，但前提条件和上一种方式一样。

  ```xml
  <!-- 将包内的映射器接口实现全部注册为映射器 -->
  <mappers>
    <package name="com.xun.bank.mapper"/>
  </mappers>
  ```

  

























