mybatis-config.xml配置文件

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
        <mapper resource="CarMapper.xml"/>
        <mapper resource="CarMapper2.xml"/>
    </mappers>
</configuration>
```

- configuration：根标签，表示配置信息。
- environments：环境（多个），以“s”结尾表示复数，也就是说mybatis的环境可以配置多个数据源。
  - default属性：表示默认使用的是哪个环境，default后面填写的是environment的id。**default的值只需要和environment的id值一致即可**。

- environment：具体的环境配置（**主要包括：事务管理器的配置 + 数据源的配置**）
  - id：给当前环境一个唯一标识，该标识用在environments的default后面，用来指定默认环境的选择。

- transactionManager：配置事务管理器

  - type属性：指定事务管理器具体使用什么方式，可选值包括两个

    - **JDBC**：使用JDBC原生的事务管理机制。**底层原理：事务开启conn.setAutoCommit(false); ...处理业务...事务提交conn.commit();**

    - **MANAGED**：交给其它容器来管理事务，比如WebLogic、JBOSS等。如果没有管理事务的容器，则没有事务。**没有事务的含义：只要执行一条DML语句，则提交一次**。

- dataSource：指定数据源

  - type属性：用来指定具体使用的数据库连接池的策略，可选值包括三个

    - **UNPOOLED**：采用传统的获取连接的方式，虽然也实现Javax.sql.DataSource接口，但是并没有使用池的思想。

      - property可以是：

        - driver 这是 JDBC 驱动的 Java 类全限定名。

        - url 这是数据库的 JDBC URL 地址。

        - username 登录数据库的用户名。

        - password 登录数据库的密码。

        - defaultTransactionIsolationLevel 默认的连接事务隔离级别。

        - defaultNetworkTimeout 等待数据库操作完成的默认网络超时时间（单位：毫秒）

    - **POOLED**：采用传统的javax.sql.DataSource规范中的连接池，mybatis中有针对规范的实现。

      - property可以是（除了包含**UNPOOLED**中之外）：

        - poolMaximumActiveConnections 在任意时间可存在的活动（正在使用）连接数量，默认值：10

        - poolMaximumIdleConnections 任意时间可能存在的空闲连接数。

        - 其它....

    - **JNDI**：采用服务器提供的JNDI技术实现，来获取DataSource对象，不同的服务器所能拿到DataSource是不一样。如果不是web或者maven的war工程，JNDI是不能使用的。

      - property可以是（最多只包含以下两个属性）：

        - initial_context 这个属性用来在 InitialContext 中寻找上下文（即，initialContext.lookup(initial_context)）这是个可选属性，如果忽略，那么将会直接从 InitialContext 中寻找 data_source 属性。

        - data_source 这是引用数据源实例位置的上下文路径。提供了 initial_context 配置时会在其返回的上下文中进行查找，没有提供时则直接在 InitialContext 中查找。

- mappers：在mappers标签中可以配置多个sql映射文件的路径。
- mapper：配置某个sql映射文件的路径
  - resource属性：使用相对于类路径的资源引用方式
  - url属性：使用完全限定资源定位符（URL）方式	



当事务管理器是：JDBC

- 采用JDBC的原生事务机制：

  - 开启事务：conn.setAutoCommit(false);

  - 处理业务......

  - 提交事务：conn.commit();

当事务管理器是：MANAGED

- 交给容器去管理事务，但目前使用的是本地程序，没有容器的支持，**当mybatis找不到容器的支持时：没有事务**。也就是说只要执行一条DML语句，则提交一次。

## properties

mybatis提供了更加灵活的配置，连接数据库的信息可以单独写到一个属性资源文件中，假设在类的根路径下创建jdbc.properties文件，配置如下：

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/powernode
```

在mybatis核心配置文件中引入：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--引入外部属性资源文件-->
    <properties resource="jdbc.properties">
        <property name="jdbc.username" value="root"/>
        <property name="jdbc.password" value="root"/>
    </properties>

    <environments default="dev">
        <environment id="dev">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!--${key}使用-->
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="CarMapper.xml"/>
    </mappers>
</configuration>
```

**properties两个属性：**

​	**resource：这个属性从类的根路径下开始加载。【常用的。】**

​	**url：从指定的url加载，假设文件放在d:/jdbc.properties，这个url可以写成：file:///d:/jdbc.properties。注意是三个斜杠哦。**

注意：如果不知道mybatis-config.xml文件中标签的编写顺序的话，可以有两种方式知道它的顺序：

- 第一种方式：查看dtd约束文件。
- 第二种方式：通过idea的报错提示信息。【一般采用这种方式】

## mapper

mapper标签用来指定SQL映射文件的路径，包含多种指定方式，这里先主要看其中两种：

第一种：resource，从类的根路径下开始加载【比url常用】

```xml
<mappers>
  <mapper resource="CarMapper.xml"/>
</mappers>
```

如果是这样写的话，必须保证类的根下有CarMapper.xml文件。

如果类的根路径下有一个包叫做test，CarMapper.xml如果放在test包下的话，这个配置应该是这样写：

```xml
<mappers>
  <mapper resource="test/CarMapper.xml"/>
</mappers>
```

第二种：url，从指定的url位置加载

假设CarMapper.xml文件放在d盘的根下，这个配置就需要这样写：

```xml
<mappers>
  <mapper url="file:///d:/CarMapper.xml"/>
</mappers>
```