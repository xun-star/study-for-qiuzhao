# 环境准备

1. 引入依赖

   ```xml
   <!-- SpringMVC -->
           <dependency>
               <groupId>org.springframework</groupId>
               <artifactId>spring-webmvc</artifactId>
               <version>5.3.1</version>
           </dependency>
   
           <!-- 日志 -->
           <dependency>
               <groupId>ch.qos.logback</groupId>
               <artifactId>logback-classic</artifactId>
               <version>1.2.3</version>
           </dependency>
           <!-- Spring5和Thymeleaf整合包 -->
           <dependency>
               <groupId>org.thymeleaf</groupId>
               <artifactId>thymeleaf-spring5</artifactId>
               <version>3.1.2.RELEASE</version>
           </dependency>
   
   
          <!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
           <dependency>
               <groupId>javax.servlet</groupId>
               <artifactId>javax.servlet-api</artifactId>
               <version>4.0.1</version>
               <scope>provided</scope>
           </dependency>
   ```

2. 配置web.xml文件

   注册SpringMVC的前端控制器DispatcherServlet

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
       <!-- 配置SpringMVC的前端控制器，对浏览器发送的请求统一进行处理 -->
       <servlet>
           <servlet-name>springMVC</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <!-- 通过初始化参数指定SpringMVC配置文件的位置和名称 -->
           <init-param>
               <!-- contextConfigLocation为固定值 -->
               <param-name>contextConfigLocation</param-name>
               <!-- 使用classpath:表示从类路径查找配置文件，例如maven工程中的src/main/resources -->
               <param-value>classpath:springMVC.xml</param-value>
           </init-param>
           <!--
                作为框架的核心组件，在启动过程中有大量的初始化操作要做
               而这些操作放在第一次请求时才执行会严重影响访问速度
               因此需要通过此标签将启动控制DispatcherServlet的初始化时间提前到服务器启动时
           -->
           <load-on-startup>1</load-on-startup>
       </servlet>
       <servlet-mapping>
           <servlet-name>springMVC</servlet-name>
           <!--
               设置springMVC的核心控制器所能处理的请求的请求路径
               /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
               但是/不能匹配.jsp请求路径的请求
           -->
           <url-pattern>/</url-pattern>
       </servlet-mapping>
   </web-app>
   ```

   ><url-pattern>标签中使用/和/*的区别：
   >
   >/所匹配的请求可以是/login或.html或.js或.css方式的请求路径，但是/不能匹配.jsp请求路径的请求
   >
   >因此就可以避免在访问jsp页面时，该请求被DispatcherServlet处理，从而找不到相应的页面
   >
   >/*则能够匹配所有请求，例如在使用过滤器时，若需要对所有请求进行过滤，就需要使用/*的写法

   

3. 创建请求控制器

   由于前端控制器对浏览器发送的请求进行了统一的处理，但是具体的请求有不同的处理过程，因此需要创建处理具体请求的类，即请求控制器

   请求控制器中每一个处理请求的方法成为控制器方法

   因为SpringMVC的控制器由一个POJO（普通的Java类）担任，因此需要通过@Controller注解将其标识为一个控制层组件，交给Spring的IoC容器管理，此时SpringMVC才能够识别控制器的存在

4. 创建springmvc配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
   
       <!-- 自动扫描包 -->
       <context:component-scan base-package="controller"/>
   
       <!-- 配置Thymeleaf视图解析器 -->
       <bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
           <property name="order" value="1"/>
           <property name="characterEncoding" value="UTF-8"/>
           <property name="templateEngine">
               <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
                   <property name="templateResolver">
                       <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
   
                           <!-- 视图前缀 -->
                           <property name="prefix" value="/WEB-INF/templates/"/>
   
                           <!-- 视图后缀 -->
                           <property name="suffix" value=".html"/>
                           <property name="templateMode" value="HTML5"/>
                           <property name="characterEncoding" value="UTF-8" />
                       </bean>
                   </property>
               </bean>
           </property>
       </bean>
   
       <!--
          处理静态资源，例如html、js、css、jpg
         若只设置该标签，则只能访问静态资源，其他请求则无法访问
         此时必须设置<mvc:annotation-driven/>解决问题
        -->
       <mvc:default-servlet-handler/>
   
       <!-- 开启mvc注解驱动 -->
       <mvc:annotation-driven>
           <mvc:message-converters>
               <!-- 处理响应中文内容乱码 -->
               <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                   <property name="defaultCharset" value="UTF-8" />
                   <property name="supportedMediaTypes">
                       <list>
                           <value>text/html</value>
                           <value>application/json</value>
                       </list>
                   </property>
               </bean>
           </mvc:message-converters>
       </mvc:annotation-driven>
   </beans>
   ```

5. 总结

   浏览器发送请求，若请求地址符合前端控制器的url-pattern，该请求就会被前端控制器DispatcherServlet处理。前端控制器会读取SpringMVC的核心配置文件，通过扫描组件找到控制器，将请求地址和控制器中@RequestMapping注解的value属性值进行匹配，若匹配成功，该注解所标识的控制器方法就是处理请求的方法。处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会被视图解析器解析，加上前缀和后缀组成视图的路径，通过Thymeleaf对视图进行渲染，最终转发到视图所对应页面