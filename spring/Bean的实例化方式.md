Spring为bean提供了多种实例化方式，通常包括四种：

* 通过构造方法实例化
* 通过简单工厂模式实例化
* 通过factory-bean实例化： 本质上还是通过简单工厂模式实例化的
* 通过FactoryBean接口实例化

## 通过构造方法实例化

```java
package com.xun.bean;

public class User {
    public User() {
        System.out.println("User类的无参数构造方法执行。");
    }
}

```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userBean" class="com.xun.bean.User"/>

</beans>
```

```java
package com.xun.test;

import com.powernode.spring6.bean.User;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class SpringInstantiationTest {
    @Test
    public void testConstructor(){
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring.xml");
        User user = applicationContext.getBean("userBean", User.class);
        System.out.println(user);
    }
}

```

## 通过简单工厂模式实例化

```java
public class VipFactory {
    public static Vip get(){
        return new Vip();
    }
}
```

```xml
<bean id="vipBean" class="com.xun.bean.VipFactory" factory-method="get"/>
```

## 通过FactoryBean接口实例化

当你编写的类直接实现FactoryBean接口之后，factory-bean不需要指定了，factory-method也不需要指定了。factory-bean会自动指向实现FactoryBean接口的类，factory-method会自动指向getObject()方法。

## BeanFactory和FactoryBean的区别

### BeanFactory

Spring IoC容器的顶级对象，BeanFactory被翻译为“Bean工厂”，在Spring的IoC容器中，“Bean工厂”负责创建Bean对象。

BeanFactory是工厂。

###  FactoryBean

FactoryBean：它是一个Bean，是一个能够辅助Spring实例化其它Bean对象的一个Bean。

在Spring中，Bean可以分为两类：

- 第一类：普通Bean
- 第二类：工厂Bean（记住：工厂Bean也是一种Bean，只不过这种Bean比较特殊，它可以辅助Spring实例化其它Bean对象。）