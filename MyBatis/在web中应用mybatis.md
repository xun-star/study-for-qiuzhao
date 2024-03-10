# 搭建环境

## 数据库表的设计

```sql
create table bank(
    id bigint auto_increment primary key ,
    actno varchar(255) comment "账号",
    balance decimal(15,2) comment "余额"
);
insert into bank values(1,'act001',50000);
insert into bank values(2,'act002',0);
```

## 添加依赖

```xml
 <!--mybatis依赖-->
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
        <!--junit依赖-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
        <!--logback依赖-->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.11</version>
        </dependency>
        <!--servlet依赖-->
        <dependency>
            <groupId>jakarta.servlet</groupId>
            <artifactId>jakarta.servlet-api</artifactId>
            <version>5.0.0</version>
            <scope>provided</scope>
        </dependency>
        
         <build>
        <finalName>mybatis-004-web</finalName>
        <pluginManagement>
            <plugins>
                <plugin>
                    <artifactId>maven-clean-plugin</artifactId>
                    <version>3.1.0</version>
                </plugin>
                <plugin>
                    <artifactId>maven-resources-plugin</artifactId>
                    <version>3.0.2</version>
                </plugin>
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.8.0</version>
                </plugin>
                <plugin>
                    <artifactId>maven-surefire-plugin</artifactId>
                    <version>2.22.1</version>
                </plugin>
                <plugin>
                    <artifactId>maven-war-plugin</artifactId>
                    <version>3.2.2</version>
                </plugin>
                <plugin>
                    <artifactId>maven-install-plugin</artifactId>
                    <version>2.5.2</version>
                </plugin>
                <plugin>
                    <artifactId>maven-deploy-plugin</artifactId>
                    <version>2.8.2</version>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>
```

## 引入配置文件

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/xun
jdbc.username=root
jdbc.password=root
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <properties resource="jdbc.properties"/>

    <environments default="dev">
        <environment id="dev">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!--一定要注意这里的路径哦！！！-->
        <mapper resource="AccountMapper.xml"/>
    </mappers>
</configuration>
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="account">

</mapper>
```

## 前端页面

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>银行账户转账</title>
</head>
<body>
<!--/bank是应用的根，部署web应用到tomcat的时候一定要注意这个名字-->
<form action="/bank/transfer" method="post">
    转出账户：<input type="text" name="fromActno"/><br>
    转入账户：<input type="text" name="toActno"/><br>
    转账金额：<input type="text" name="money"/><br>
    <input type="submit" value="转账"/>
</form>
</body>
</html>
```

# 业务代码编写

## 定义pojo类

```java
package com.xun.bank.pojo;

public class Account {
    private Long id;
    private String actno;
    private Double balance;

    @Override
    public String toString() {
        return "Account{" +
                "id=" + id +
                ", actno='" + actno + '\'' +
                ", balance=" + balance +
                '}';
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getActno() {
        return actno;
    }

    public void setActno(String actno) {
        this.actno = actno;
    }

    public Double getBalance() {
        return balance;
    }

    public void setBalance(Double balance) {
        this.balance = balance;
    }

    public Account(Long id, String actno, Double balance) {
        this.id = id;
        this.actno = actno;
        this.balance = balance;
    }

    public Account() {
    }
}

```

## 编写AccountDao接口，以及AccountDaoImpl实现类

>dao类
>
>是负责对数据进行操作的类，只负责数据的增删改查，不牵扯到任何业务

分析dao中至少要提供几个方法，才能完成转账：

- 转账前需要查询余额是否充足：selectByActno
- 转账时要更新账户：update

```java
package com.xun.bank.dao;

import com.xun.bank.pojo.Account;

public interface AccountDao {
    //根据账号获取用户信息
    Account selectByActno(String actno);
    //更新账户信息
    int update(Account act);
}

```

```java
package com.xun.bank.daoimpl;

import com.xun.bank.dao.AccountDao;
import com.xun.bank.pojo.Account;
import com.xun.bank.utils.SqlSessionUtil;
import org.apache.ibatis.session.SqlSession;

public class AccountDaoImpl implements AccountDao {
    @Override
    public Account selectByActno(String actno) {
        SqlSession sqlSession = SqlSessionUtil.openSession();
        Account act =(Account) sqlSession.selectOne("selectByActno",actno);//进行类型强转
        sqlSession.close();
        return act;
    }

    @Override
    public int update(Account act) {
        SqlSession sqlSession = SqlSessionUtil.openSession();
        int count = sqlSession.update("update", act);
        sqlSession.commit();
        sqlSession.close();
        return count;
    }
}

```

写了dao类，就需要用到SQL映射文件了

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

## 编写AccountService接口以及AccountServiceImpl

>
>
>service类专注于处理业务

```java
package com.xun.bank.service;

import com.xun.bank.exception.AppException;
import com.xun.bank.exception.MoneyNotEnoughException;

public interface AccountService {
    void transfer(String fromActno, String toActno, double money) throws MoneyNotEnoughException, AppException;
}

```

```java
package com.xun.bank.serviceimpl;

import com.xun.bank.dao.AccountDao;
import com.xun.bank.daoimpl.AccountDaoImpl;
import com.xun.bank.exception.AppException;
import com.xun.bank.exception.MoneyNotEnoughException;
import com.xun.bank.pojo.Account;
import com.xun.bank.service.AccountService;

public class AccountServiceImpl implements AccountService {

    AccountDao accountDao = new AccountDaoImpl();
    @Override
    public void transfer(String fromActno, String toActno, double money) throws MoneyNotEnoughException, AppException {
        //查出转出账户余额
        Account fromact = accountDao.selectByActno(fromActno);
        if(fromact.getBalance() < money){
            throw new MoneyNotEnoughException("对不起，账户余额不足");
        }
        try{
            Account toact = accountDao.selectByActno(toActno);
            fromact.setBalance(fromact.getBalance() - money);
            toact.setBalance(toact.getBalance() + money);
            accountDao.update(fromact);
            accountDao.update(toact);
        }catch(Exception e){
            throw new AppException("转账失败，未知原因");
        }
    }
}

```

## 编写异常类

```java
package com.xun.bank.exception;

public class MoneyNotEnoughException extends Exception{
    public MoneyNotEnoughException(){}
    public MoneyNotEnoughException(String msg){ super(msg); }
}

```

```java
package com.xun.bank.exception;

public class AppException extends Exception{
    public AppException(){}
    public AppException(String msg){ super(msg); }
}

```

## 编写AccountController

>
>
>controller类是控制器，接受前端的请求，调用相关的service完成业务

```java
package com.xun.bank.controller;

import com.xun.bank.exception.AppException;
import com.xun.bank.exception.MoneyNotEnoughException;
import com.xun.bank.service.AccountService;
import com.xun.bank.serviceimpl.AccountServiceImpl;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.io.PrintWriter;
@WebServlet("/transfer")
public class AccountController extends HttpServlet {
    private AccountService accountService= new AccountServiceImpl();

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //获取响应流
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        String fromActno = request.getParameter("fromActno");
        String toActno = request.getParameter("toActno");
        double money = Integer.parseInt(request.getParameter("money"));
        try{
            accountService.transfer(fromActno,toActno,money);
            out.println("<h1>转账成功！！！</h1>");
        }catch (MoneyNotEnoughException e){
            out.println(e.getMessage());
        }catch (AppException e){
            out.println(e.getMessage());
        }

    }
}

```

# 项目展示

数据库

![image-20240310172532253](D:\study-for-qiuzhao\MyBatis\assets\image-20240310172532253.png)

![image-20240310172544328](D:\study-for-qiuzhao\MyBatis\assets\image-20240310172544328.png)

![image-20240310172556395](D:\study-for-qiuzhao\MyBatis\assets\image-20240310172556395.png)

![image-20240310172606632](D:\study-for-qiuzhao\MyBatis\assets\image-20240310172606632.png)

