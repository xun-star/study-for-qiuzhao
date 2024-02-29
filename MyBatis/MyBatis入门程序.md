## 入门程序

1. 打包方式

```xml
    <groupId>com.xun</groupId>
    <artifactId>mybatis</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>
```

2. 引入依赖

```xml
<!--mybatis核心依赖-->
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>3.5.10</version>
</dependency>
<!--mysql驱动依赖-->
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>8.0.30</version>
</dependency>
```

3. 在resources根目录下新建mybatis-config.xml配置文件

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
                <property name="url" value="jdbc:mysql://localhost:3306/test"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!--sql映射文件创建好之后，需要将该文件路径配置到这里-->
        <mapper resource=""/>
    </mappers>
</configuration>
```

4. 在resources根目录下新建CarMapper.xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace先随意写一个-->
<mapper namespace="car">
    <!--insert sql：保存一个汽车信息-->
    <insert id="insertCar">
        insert into t_car
            (id,car_num,brand,guide_price,produce_time,car_type)
        values
            (null,'102','丰田mirai',40.30,'2014-10-05','氢能源')
    </insert>
</mapper>


<!--
注意1：sql语句最后结尾可以不写“;”
注意2：CarMapper.xml文件的名字不是固定的。可以使用其它名字。
注意3：CarMapper.xml文件的位置也是随意的。这里选择放在resources根下，相当于放到了类的根路径下。
注意4：将CarMapper.xml文件路径配置到mybatis-config.xml：
<mapper resource="CarMapper.xml"/>
-->
```

5. 编写MyBatisTest代码

```java
package test;

import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;

public class MyBatisTest {
    public static void main(String[] args) {
        //1.创建SqlSessionFactoryBuilder对象
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        //InputStream is = Resources.getResourceAsStream("mybatis-config.xml");这种方式也可以
        //Resources类可以从类路径当中获取资源，我们通常使用它来获取输入流InputStream
        InputStream is = Thread.currentThread().getContextClassLoader().getResourceAsStream("mybatis-config.xml");
        //2.创建SqlSessionFactory对象
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
        //3.创建SqlSession对象
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //4.执行sql
        int count = sqlSession.insert("insertCar");// 这个"insertCar"必须是sql的id
        System.out.println("插入了" + count + "条数据");
        // 5. 提交（mybatis默认采用的事务管理器是JDBC，默认是不提交的，需要手动提交。）
        sqlSession.commit();
        // 6. 关闭资源（只关闭是不会提交的）
        sqlSession.close();
    }
}

```

## 引入JUnit

1. 引入依赖

   ```xml
   <!-- junit依赖 -->
   <dependency>
       <groupId>junit</groupId>
       <artifactId>junit</artifactId>
       <version>4.13.2</version>
       <scope>test</scope>
   </dependency>
   ```

2. 编写单元测试类【测试用例】，测试用例中每一个测试方法上使用@Test注解进行标注。

   - 测试用例的名字以及每个测试方法的定义都是有规范的：

   - 测试用例的名字：XxxTest

   - 测试方法声明格式：public void test业务方法名(){}

   ```java
   // 测试用例
   public class CarMapperTest{
       
       // 测试方法
       @Test
       public void testInsert(){}
       
       @Test
       public void testUpdate(){}
       
   }
   ```

3. 可以在类上执行，也可以在方法上执行

   - 在类上执行时，该类中所有的测试方法都会执行。

   - 在方法上执行时，只执行当前的测试方法。

   ```java
   package test;
   
   import org.apache.ibatis.io.Resources;
   import org.apache.ibatis.session.SqlSession;
   import org.apache.ibatis.session.SqlSessionFactory;
   import org.apache.ibatis.session.SqlSessionFactoryBuilder;
   import org.junit.jupiter.api.Test;
   
   public class CarMapperTest {
       @Test
       public void testInsert(){
           SqlSession sqlSession = null;
           try{
               SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
   
               SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(Resources.getResourceAsStream("mybatis-config.xml"));
   
               sqlSession = sqlSessionFactory.openSession();
   
               int count = sqlSession.insert("insertCar");
   
               System.out.println("插入了" + count + "条数据");
   
               sqlSession.commit();
   
           }catch (Exception e){
               if (sqlSession != null) {
                   sqlSession.rollback();
               }
               e.printStackTrace();
           }finally {
               // 6.关闭
               if (sqlSession != null) {
                   sqlSession.close();
               }
           }
       }
   
   }
   
   ```

## 引入日志框架logback

- 引入日志框架的目的是为了看清楚mybatis执行的具体sql。

- 启用标准日志组件，只需要在mybatis-config.xml文件中添加以下配置

  ```xml
  <settings>
    <setting name="logImpl" value="STDOUT_LOGGING" />
  </settings>
  ```

1. 引入logback相关依赖

   ```xml
   <dependency>
     <groupId>ch.qos.logback</groupId>
     <artifactId>logback-classic</artifactId>
     <version>1.2.11</version>
     <scope>test</scope>
   </dependency>
   ```

2. 引入logback相关配置文件（文件名叫做logback.xml或logback-test.xml，放到类路径当中）

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   
   <configuration debug="false">
       <!-- 控制台输出 -->
       <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
           <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
               <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
               <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
           </encoder>
       </appender>
       <!-- 按照每天生成日志文件 -->
       <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
           <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
               <!--日志文件输出的文件名-->
               <FileNamePattern>${LOG_HOME}/TestWeb.log.%d{yyyy-MM-dd}.log</FileNamePattern>
               <!--日志文件保留天数-->
               <MaxHistory>30</MaxHistory>
           </rollingPolicy>
           <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
               <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
               <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
           </encoder>
           <!--日志文件最大的大小-->
           <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
               <MaxFileSize>100MB</MaxFileSize>
           </triggeringPolicy>
       </appender>
   
       <!--mybatis log configure-->
       <logger name="com.apache.ibatis" level="TRACE"/>
       <logger name="java.sql.Connection" level="DEBUG"/>
       <logger name="java.sql.Statement" level="DEBUG"/>
       <logger name="java.sql.PreparedStatement" level="DEBUG"/>
   
       <!-- 日志输出级别,logback日志级别包括五个：TRACE < DEBUG < INFO < WARN < ERROR -->
       <root level="DEBUG">
           <appender-ref ref="STDOUT"/>
           <appender-ref ref="FILE"/>
       </root>
   
   </configuration>
   ```

   ![image-20240229154254399](D:\study-for-qiuzhao\MyBatis\assets\image-20240229154254399.png)

## MyBatis工具类SqlSessionUtil的封装

- 每一次获取SqlSession对象代码太繁琐，封装一个工具类

```java
package utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

public class SqlSessionUtil {
    private static SqlSessionFactory sqlSessionFactory;

    /**
     * 类加载时初始化sqlSessionFactory对象
     */
    static {
        try {
            SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
            sqlSessionFactory = sqlSessionFactoryBuilder.build(Resources.getResourceAsStream("mybatis-config.xml"));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 每调用一次openSession()可获取一个新的会话，该会话支持自动提交。
     *
     * @return 新的会话对象
     */
    public static SqlSession openSession() {
        return sqlSessionFactory.openSession(true);
    }
}

```

- 测试工具类，将testInsertCar()改造

  ```java
  package test;
  
  import org.apache.ibatis.io.Resources;
  import org.apache.ibatis.session.SqlSession;
  import org.apache.ibatis.session.SqlSessionFactory;
  import org.apache.ibatis.session.SqlSessionFactoryBuilder;
  import org.junit.jupiter.api.Test;
  import utils.SqlSessionUtil;
  
  public class CarMapperTest {
      @Test
      public void testInsert(){
          SqlSession sqlSession = null;
          try{
              sqlSession = SqlSessionUtil.openSession();
  
              int count = sqlSession.insert("insertCar");
  
              System.out.println("插入了" + count + "条数据");
  
              sqlSession.commit();
  
          }catch (Exception e){
              if (sqlSession != null) {
                  sqlSession.rollback();
              }
              e.printStackTrace();
          }finally {
              // 6.关闭
              if (sqlSession != null) {
                  sqlSession.close();
              }
          }
      }
  
  }
  
  ```

  