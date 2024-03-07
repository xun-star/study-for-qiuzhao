# spring简介

- Spring是一个开源框架

- Spring是一个轻量级的控制反转(IoC)和面向切面(AOP)的容器框架。

  - Spring实现了控制反转思想，Spring框架可以帮你维护对象和对象之间的关系

  - Spring实现了面向切面编程

  - 由于我们可以将创建对象交给Spring负责，所以Spring也是一个存放对象的容器

- Spring为简化开发而生，让程序员只需关注核心业务的实现，尽可能的不再关注非业务逻辑代码（事务控制，安全日志等）。
- Spring是一个实现了IoC思想的容器。

# spring的特点

1. 轻量 

   1. 完整的Spring框架可以在一个大小只有1MB多的JAR文件里发布，并且Spring所需的处理开销也是微不足道的。

   2. Spring是非侵入式的：spring框架的运行不需要依赖其他框架

2. 控制反转 

​	Spring实现了控制反转思想

3. 面向切面 

   1. Spring提供了面向切面编程的丰富支持

   2. 允许开发者只需专注于业务逻辑的开发，与核心业务无关的代码可以以切面的方式加入核心业务代码的执行过程中，将核心业务的执行流程看成是纵向执行的，与核心业务无关的代码（如事务、日志等）可以以横向切面的方式加入核心业务的执行过程中

4. 容器
   1. Spring 可以包含并管理应用对象的配置和生命周期，即Spring可以负责对象的创建到对象的销毁这整个生命周期过程中对象的维护和管理，Spring就好比一个存放和管理对象的容器

5. 框架 

​	  1. Spring可以将简单的组件配置、组合成为复杂的应用。

# spring入门程序

1. 引入依赖

   ```xml
   <dependencies>
           <!-- spring 基础依赖 -->
           <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
           <dependency>
               <groupId>org.springframework</groupId>
               <artifactId>spring-context</artifactId>
               <version>6.0.11</version>
           </dependency>
   
           <!-- 单元测试依赖 -->
           <dependency>
               <groupId>junit</groupId>
               <artifactId>junit</artifactId>
               <version>4.13.2</version>
               <scope>test</scope>
           </dependency>
   </dependencies>
   ```

2. 定义普通的java类

   ```java
   package com.xun.bean;
   
   public class Student {
       private String name;
       private Integer age;
   
       public Student() {
       }
   
       public Student(String name, Integer age) {
           this.name = name;
           this.age = age;
       }
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public Integer getAge() {
           return age;
       }
   
       public void setAge(Integer age) {
           this.age = age;
       }
   
       @Override
       public String toString() {
           return "Student{" +
                   "name='" + name + '\'' +
                   ", age=" + age +
                   '}';
       }
   }
   
   ```

3. 编写spring配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
       <!-- spring的配置文件 -->
       <!-- 在配置文件中配置bean，spring才能帮助我们管理这个对象 -->
       <!--
           id: bean的唯一标识，对象的唯一标识
           class: 类的全路径，用来指定要创建的java对象的类名
        -->
       <bean id="userBean" class="com.xun.bean.User" />
   </beans>
   ```

4. 编写测试类

   ```java
   public class Test {
       @org.junit.Test
       public void tset1() {
           // 第一步：获取Spring容器对象。
           // ApplicationContext 翻译为：应用上下文。其实就是Spring容器。
           // ApplicationContext 是一个接口。
           // ApplicationContext 接口下有很多实现类。其中有一个实现类叫做：ClassPathXmlApplicationContext
           // ClassPathXmlApplicationContext 专门用于从类路径当中加载spring配置文件的一个Spring上下文对象。
           // 这行代码只要执行，就相当于启动了Spring容器，同时会解析spring.xml（Spring配置文件名）文件，
           // 并且实例化在Spring配置文件中配置的所有的bean对象，创建完成对象后会将其放到spring容器当中。
           ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring.xml");
       
           // 第二步：根据bean的id从Spring容器中获取这个对象。（id Bean的唯一标识）
           Object userBean = applicationContext.getBean("userBean");
           System.out.println(userBean);
       }
   }
   ```

   ![image-20240229162137892](D:\study-for-qiuzhao\spring\assets\image-20240229162137892.png)

# 入门程序分析

- 在spring的配置文件中，每个bean的id是不能重名，因为id是每个bean的唯一表示，用于通过容器对象获取相应的bean

  ```xml
  <bean id="userBean" class="com.xun.bean.User" />
  ```

- spring是通过反射机制调用类的无参数构造方法来创建对象的，所以要想让spring给你创建对象，必须保证无参数构造方法是存在的。

- bean对象最终存储在spring容器中，在spring源码底层就是一个map集合，存储bean的map在**DefaultListableBeanFactory**类

- Spring容器加载到Bean类时 , 会把这个类的描述信息, 以包名加类名的方式存到beanDefinitionMap 中,
  Map<String,BeanDefinition> , 其中 

  - String是Key , 默认是类名首字母小写，bean的id

  - BeanDefinition , 存的是类的定义(描述信息) , 我们通常叫BeanDefinition接口为 : bean的定义对象。

  ```java
  public class DefaultListableBeanFactory 
  	extends AbstractAutowireCapableBeanFactory 
  	implements ConfigurableListableBeanFactory, BeanDefinitionRegistry, Serializable {
      	...
          private final Map<String, BeanDefinition> beanDefinitionMap;
          ...
  }
  ```

- spring配置文件名字是我们负责提供的，spring配置文件的名字是随意的

  - 因为在Spring程序中获取Spring配置文件创建Spring容器中，Spring配置文件是由我们进行指定的，所以spring配置文件的名字可以是任意的

- spring的配置文件可以有多个，在ClassPathXmlApplicationContext构造方法的参数上传递文件路径即可。且配置文件的文件名、文件路径都是任意的

- 在spring配置文件中配置的bean可以是任意类，只要这个类不是抽象的，并且提供了无参数构造方法。

- getBean()方法调用时，id不存在的时候，会出现异常

- getBean()方法返回的类型是Object，如果访问子类的特有属性和方法时，还需要向下转型，有其它办法可以解决这个问题吗？

  ```java
  Date nowTime = applicationContext.getBean("nowTime", Date.class);
  ```

```java
//ApplicationContext接口的超级父接口是：BeanFactory
//BeanFactory翻译为Bean工厂，就是能够生产Bean对象的一个工厂对象
//BeanFactory是IoC容器的顶级接口。
//Spring的IoC容器底层实际上使用了：工厂模式。
//Spring底层的IoC是怎么实现的？XML解析 + 工厂模式 + 反射机制

//ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring6.xml");
BeanFactory beanFactory = new ClassPathXmlApplicationContext("spring6.xml");
User user = applicationContext.getBean("userBean", User.class);
System.out.println(user);
```

* Spring底层的IoC容器是通过：XML解析+工厂模式+反射机制实现的。
* spring不是在调用getBean()方法的时候创建对象，执行`new ClassPathXmlApplicationContext("spring6.xml");`加载配置文件的时候，就会实例化对象。