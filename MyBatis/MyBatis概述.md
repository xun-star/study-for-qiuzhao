# 三层架构

* 表现层：直接和前端交互，接受AJAX请求，返回json数据
* 业务层：一是处理前端的请求，二是返回持久层获取的数据
* 持久层(数据访问层)：直接操作数据库，完成CRUD，返回数据给业务层

# 了解MyBatis

* Mybatis本质上是对JDBC的封装，实现CRUD
* Mybatis在三层框架中属于持久层

## ORM对象关系映射

* O(object)：是java虚拟机中的对象

* R(relational)：关系型数据库

* M(Mapping)：将java虚拟机中的对象映射到数据库中的一行记录，或者将数据库中的一行记录，映射为java虚拟机对象

  ![image-20240228135347293](D:\study-for-qiuzhao\MyBatis\assets\image-20240228135347293.png)

# MyBatis框架的特点

- 支持定制化 SQL、存储过程、基本映射以及高级映射
- 避免了几乎所有的 JDBC 代码中手动设置参数以及获取结果集
- 支持XML开发，也支持注解式开发。【为了保证sql语句的灵活，所以mybatis大部分是采用XML方式开发。】
- 将接口和 Java 的 POJOs(Plain Ordinary Java Object，简单普通的Java对象)映射成数据库中的记录
- 体积小好学：两个jar包，两个XML配置文件。
- 完全做到sql解耦合。
- 提供了基本映射标签。
- 提供了高级映射标签。
- 提供了XML标签，支持动态SQL的编写。