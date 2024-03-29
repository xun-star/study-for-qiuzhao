# 异常介绍

>
>
>java语言中，将程序执行过程中，发生的不正常行为称为“异常”。(开发过程中的语法错误和逻辑错误不是异常)
>
>执行过程中的异常可以分为两大类：
>
>* Error(错误)：java虚拟机无法解决的严重问题。如：JVM系统内部错误，资源耗尽等情况。Error是严重错误，程序 会 崩溃
>* Exception：其他因 编程错误或偶然的外部因素，导致的一般性问题，可以使用针对性的代码进行处理，例如空指针访问，试图读取不存在的文件。Exception分为两大类`运行时异常`(程序运行时，发生的异常)和`编译时异常`(编程时，编译器检查出的异常)

异常体系图

![image-20240117160820942](D:\study-for-qiuzhao\java异常处理\assets\image-20240117160820942.png)

>
>
>运行时异常：编译器检查不出来，一般指编程序时的逻辑异常，是程序员应该避免其出现的异常
>
>对于运行时异常，可以不作处理，因为这类异常普遍存在，若全部处理，可能对程序的可读性和运行效率有影响
>
>编译时异常：时编译器要求必须处理的异常

# 常见的运行时异常

>
>
>* NullPointerException 空指针异常：当应用程序试图在需要对象的地方使用null时
>* ArithmeticException 数字运算异常：一个整数“除以零”时
>* ArrayIndexOutOfBoundsException   数组下标越界异常：用非法索引访问数组时抛出的异常
>* ClassCastException 类型转换异常：试图将对象强制转换为不是实例的子类时
>* NumberFormatException 数字格式不正确异常：将字符串转换成一种数值类型

# 编译异常

>
>
>编译异常时指在编译期间，就必须处理的异常，否则代码不能通过编译
>
>* SQLException 操作数据库时，查询表时可能发生的异常
>* IOException 操作文件时，发生的异常
>* FileNotFoundException 当操作一个不存在的文件时，发生的异常
>* ClassNotFoundException 加载类，而该类不存在时，发生的异常
>* EOFException 操作文件，到文件末尾，发生异常
>* IllegalArguementException 参数异常

# 异常处理

>
>
>当异常发生时，对异常处理的方式
>
>* try-catch-finally 程序员在代码中捕获发生的异常，自行处理
>* throws 将发生的异常抛出，进啊哥哥调用者(方法)来处理，最顶级的处理者就是JVM

```JAVA
try{
    //代码/可能有异常
    //将异常生成对应的异常对象，传递给catch块
}catch(Exception e){
    //捕获到异常,对异常进行处理
    //1.当异常发生时，系统将异常封装成Exception对象e，传递给catch，得到异常对象后，程序员自己处理
    //2.注意，如果没有发生异常，catch代码块不执行
    
}finally{
    //不管try代码块是否有异常发生，始终要执行finally，所以，通常将释放资源的代码放在finally中
    //如果没有finally，语法时可以通过的
}
```

* 如果异常发生了，则异常发生后面的代码不会执行，直接进入到catch块
* 如果异常没有发生，则顺序执行try的代码块，不会进入到catch
* 如果希望不管是否有异常发生，都执行某代码，则使用finally块

可以有多个catch语句，捕获不同的异常(进行不同的业务处理)，要求父类异常在后，子类异常在前。

**如果没有出现异常，则执行try块中所有的语句，不执行catch块中的语句，如果有finally，最后还要执行finally里面的语句**

**如果出现异常，则try块中异常发生后，try块剩下的语句不在执行，将执行catch块中的语句，如果有finally，最后还要执行finally里面的语句**

# throws异常处理

>
>
>如果一个方法可能发生某种异常，但是并不确定如何处理这种异常，则此方法可以显示的声明抛出异常，表明该方法将不对这些异常处理，而由该方法的调用者负责处理。
>
>* 对于编译异常，程序中必须处理，比如try-catch或者throws
>* 对于运行时异常，程序中如果没有处理，默认就是throws的方法处理
>* 子类重写父类的方法时，对抛出异常的规定：子类重写的方法，所抛出的异常类型要么和父类抛出的异常一致，要么为父类抛出异常的类型的子类型
>* 在throws中，如果有方法try-catch，就相当于处理异常，就可以不必throws

# 自定义异常

>
>
>当程序中出现了某些“错误”，但该错误信息并没有在Throwable子类中描述处理，这个时候就可以自己设计异常类，用于描述该错误信息

步骤：

* 定义类：自定义异常类名(程序员自己定义)继承Exception或者RuntimeException
* 如果继承Exception，属于编译异常
* 如果继承RuntimeException，属于运行异常(一般继承RuntimeException)

throw和throws的区别

|        | 意义                     | 位置       | 后面跟的东西 |
| ------ | ------------------------ | ---------- | ------------ |
| throws | 异常处理的一种方式       | 方法申明处 | 异常类型     |
| throw  | 手动生成异常对象的关键字 | 方法体中   | 异常对象     |

