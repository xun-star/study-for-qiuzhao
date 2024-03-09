# Ioc控制反转

- 控制反转是一种思想。
- 控制反转是为了降低程序耦合度，提高程序扩展力，达到OCP原则，达到DIP原则。
- 控制反转，反转的是什么？
  - 将对象的创建权利交出去，交给第三方容器负责。
  - 将对象和对象之间关系的维护权交出去，交给第三方容器负责。

- 控制反转这种思想如何实现呢？
  - DI（Dependency Injection）：依赖注入

# 依赖注入

依赖注入实现了控制反转的思想。

**Spring通过依赖注入的方式来完成Bean管理的。**

**Bean管理说的是：Bean对象的创建，以及Bean对象中属性的赋值（或者叫做Bean对象之间关系的维护）。**

依赖注入：

- 依赖指的是对象和对象之间的关联关系。
- 注入指的是一种数据传递行为，通过注入行为来让对象和对象产生关系。

依赖注入常见的实现方式包括两种：

- 第一种：set注入
- 第二种：构造注入

## set注入

> set注入，基于set方法实现的，底层会通过反射机制调用属性对应的set方法然后给属性赋值。这种方式要求属性必须对外提供set方法。

```java
public class UserDao {
    public void insert(){
        System.out.println("正在保存用户信息。。。。");
    }
}

public class UserService {
    UserDao userDao;

    // 使用set方式注入，必须提供set方法。
    // 反射机制要调用这个方法给属性赋值的。
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void save(){
        userDao.insert();
    }
}

@org.junit.Test
    public void testDI(){
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring.xml");
        UserService userServiceBean = applicationContext.getBean("userServiceBean", UserService.class);
        userServiceBean.save();
        System.out.println(userServiceBean);
    }
```

```xml
<bean id="userDaoBean" class="com.xun.dao.UserDao"></bean>
    <bean id="userServiceBean" class="com.xun.service.UserService">
        <property name="userDao" ref="userDaoBean"></property>
    </bean>
```

![image-20240308204756679](D:\study-for-qiuzhao\spring\assets\image-20240308204756679.png)

**原理：**

通过property标签获取到属性名：userDao

通过属性名推断出set方法名：setUserDao

通过反射机制调用setUserDao()方法给属性赋值

property标签的name是属性名。

property标签的ref是要注入的bean对象的id。**(通过ref属性来完成bean的装配，这是bean最简单的一种装配方式。装配指的是：创建系统组件之间关联的动作)**

通过测试得知，底层实际上调用了setUserDao()方法。所以需要确保这个方法的存在。

**总结：set注入的核心实现原理：通过反射机制调用set方法来给属性赋值，让两个对象之间产生关系**

## 构造注入

> 核心原理：通过调用构造方法来给属性赋值。

```java
public class OrderDao {
    public void deleteById(){
        System.out.println("正在删除。。。");
    }
}

public class OrderService {
    OrderDao orderDao;

    public OrderService(OrderDao orderDao) {
        this.orderDao = orderDao;
    }
    public void deleteById(){
        orderDao.deleteById();
    }
}

@org.junit.Test
    public void testConstructorDI(){
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring.xml");
        OrderService orderServiceBean = applicationContext.getBean("orderServiceBean", OrderService.class);
        orderServiceBean.deleteById();
    }
```

```xml
<bean id="orderDaoBean" class="com.xun.dao.OrderDao"></bean>
    <bean id="orderServiceBean" class="com.xun.service.OrderService">
        <!--如果有多个参数，那么index的值为构造函数中参数的下标-->
        <!--<constructor-arg index="0" ref="orderDaoBean"></constructor-arg>-->
        <!--<constructor-arg index="1" ref="userDaoBean"></constructor-arg>-->
        <!--这里使用了构造方法上参数的名字-->
        <constructor-arg name="orderDao" ref="orderDaoBean"></constructor-arg>
    </bean>
```

![image-20240308205715491](D:\study-for-qiuzhao\spring\assets\image-20240308205715491.png)

通过测试得知，通过构造方法注入的时候：

- 可以通过下标
- 可以通过参数名
- 也可以不指定下标和参数名，可以类型自动推断。

Spring在装配方面做的还是比较健壮的。

# set注入专题

## 注入外部bean

```xml
    <bean id="userDaoBean" class="com.xun.spring6.dao.UserDao"/>
    <bean id="userServiceBean" class="com.xun.spring6.service.UserService">
        <property name="userDao" ref="userDaoBean"/>
    </bean>
```

## 注入内部bean

```xml
<bean id="userServiceBean" class="com.xun.spring6.service.UserService">
        <property name="userDao">
            <bean class="com.xun.spring6.dao.UserDao"/>
        </property>
</bean>
```

## 注入简单类型

```xml
<bean id="userBean" class="com.xun.spring6.beans.User">
        <!--如果像这种int类型的属性，我们称为简单类型，这种简单类型在注入的时候要使用value属性，不能使用ref-->
        <!--<property name="age" value="20"/>-->
        <property name="age">
            <value>20</value>
        </property>
</bean>
```

**需要特别注意：如果给简单类型赋值，使用value属性或value标签。而不是ref。**

## 注入数组

```java
<bean id="person" class="com.xun.spring6.beans.Person">
        <property name="favariteFoods">
            <array>
                <value>鸡排</value>
                <value>汉堡</value>
                <value>鹅肝</value>
            </array>
        </property>
    </bean>
```

**当数组中的元素是非简单类型：一个订单中包含多个商品。**

```xml
<bean id="goods1" class="com.xun.spring6.beans.Goods">
        <property name="name" value="西瓜"/>
    </bean>

    <bean id="goods2" class="com.xun.spring6.beans.Goods">
        <property name="name" value="苹果"/>
    </bean>

    <bean id="order" class="com.xun.spring6.beans.Order">
        <property name="goods">
            <array>
                <!--这里使用ref标签即可-->
                <ref bean="goods1"/>
                <ref bean="goods2"/>
            </array>
        </property>
    </bean>
```

- **如果数组中是简单类型，使用value标签。**
- **如果数组中是非简单类型，使用ref标签。**

## 注入List集合

```xml
<bean id="peopleBean" class="com.xun.spring6.beans.People">
        <property name="names">
            <list>
                <value>铁锤</value>
                <value>张三</value>
                <value>张三</value>
                <value>张三</value>
                <value>狼</value>
            </list>
        </property>
    </bean>
```

## 注入set集合

```xml
<bean id="peopleBean" class="com.xun.spring6.beans.People">
        <property name="phones">
            <set>
                <!--非简单类型可以使用ref，简单类型使用value-->
                <value>110</value>
                <value>110</value>
                <value>120</value>
                <value>120</value>
                <value>119</value>
                <value>119</value>
            </set>
        </property>
</bean>
```

- **使用<set>标签**
- **set集合中元素是简单类型的使用value标签，反之使用ref标签。**

## 注入map集合

```xml
<bean id="peopleBean" class="com.xun.spring6.beans.People">
        <property name="addrs">
            <map>
                <!--如果key不是简单类型，使用 key-ref 属性-->
                <!--如果value不是简单类型，使用 value-ref 属性-->
                <entry key="1" value="北京大兴区"/>
                <entry key="2" value="上海浦东区"/>
                <entry key="3" value="深圳宝安区"/>
            </map>
        </property>
    </bean>
```

- **使用<map>标签**
- **如果key是简单类型，使用 key 属性，反之使用 key-ref 属性。**
- **如果value是简单类型，使用 value 属性，反之使用 value-ref 属性。**

## 注入properties

java.util.Properties继承java.util.Hashtable，所以Properties也是一个Map集合。

```xml
<bean id="peopleBean" class="com.xun.spring6.beans.People">
        <property name="properties">
            <props>
                <prop key="driver">com.mysql.cj.jdbc.Driver</prop>
                <prop key="url">jdbc:mysql://localhost:3306/spring</prop>
                <prop key="username">root</prop>
                <prop key="password">123456</prop>
            </props>
        </property>
</bean>
```

- **使用<props>标签嵌套<prop>标签完成。**

## 注入null和空字符串

注入空字符串使用：<value/> 或者 value=""

注入null使用：<null/> 或者 不为该属性赋值

```xml
<bean id="vipBean" class="com.xun.spring6.beans.Vip">
        <!--空串的第一种方式-->
        <!--<property name="email" value=""/>-->
        <!--空串的第二种方式-->
        <property name="email">
            <value/>
        </property>
</bean>

<bean id="vipBean" class="com.xun.spring6.beans.Vip">
        <property name="email">
            <null/>
        </property>
</bean>

<bean id="vipBean" class="com.xun.spring6.beans.Vip" />
```

## p命名空间注入

目的：简化配置。

使用p命名空间注入的前提条件包括两个：

- 第一：在XML头部信息中添加p命名空间的配置信息：xmlns:p="http://www.springframework.org/schema/p"
- 第二：p命名空间注入是基于setter方法的，所以需要对应的属性提供setter方法。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="customerBean" class="com.powernode.spring6.beans.Customer" p:name="zhangsan" p:age="20"/>

</beans>
```

## c命名空间注入

c命名空间是简化构造方法注入的。

使用c命名空间的两个前提条件：

​	第一：需要在xml配置文件头部添加信息：xmlns:c="http://www.springframework.org/schema/c"

​	第二：需要提供构造方法。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:c="http://www.springframework.org/schema/c"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--<bean id="myTimeBean" class="com.powernode.spring6.beans.MyTime" c:year="1970" c:month="1" c:day="1"/>-->

    <bean id="myTimeBean" class="com.powernode.spring6.beans.MyTime" c:_0="2008" c:_1="8" c:_2="8"/>

</beans>
```

## 基于xml的自动装配

Spring还可以完成自动化的注入，自动化注入又被称为自动装配。它可以根据**名字**进行自动装配，也可以根据**类型**进行自动装配。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userService" class="com.powernode.spring6.service.UserService" autowire="byName"/>
    
    <bean id="aaa" class="com.powernode.spring6.dao.UserDao"/>

</beans>
```

这个配置起到关键作用：

- UserService Bean中需要添加autowire="byName"，表示通过名称进行装配。
- UserService类中有一个UserDao属性，而UserDao属性的名字是aaa，**对应的set方法是setAaa()**，正好和UserDao Bean的id是一样的。这就是根据名称自动装配。

## 根据类型自动装配

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--byType表示根据类型自动装配-->
    <bean id="accountService" class="com.powernode.spring6.service.AccountService" autowire="byType"/>

    <bean class="com.powernode.spring6.dao.AccountDao"/>

</beans>
```

可以看到无论是byName还是byType，在装配的时候都是基于set方法的。所以set方法是必须要提供的。提供构造方法是不行的，当byType进行自动装配的时候，配置文件中某种类型的Bean必须是唯一的，不能出现多个