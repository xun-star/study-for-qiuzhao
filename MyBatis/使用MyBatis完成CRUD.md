# Insert

## Map

```xml
<mapper namespace="car">
    <!--insert sql：保存一个汽车信息-->
    <insert id="insertCar">
        insert into t_car
            (id,car_num,brand,guide_price,produce_time,car_type)
        values
            (null,'102','丰田mirai',40.30,'2014-10-05','氢能源')
    </insert>
</mapper>
```

上述代码的问题是，sql语句中的值不应该是写死的，而是动态的。

在java程序中，我们可以将数据存到一个map集合中，在sql语句中，使用#{map集合的key}来完成传参，`#{map集合的key}`相当于占位符

修改java程序

```java
Map<String, Object> map = new HashMap<>();
map.put("car_num","103");
map.put("brand","奔驰E300");
map.put("guide_price","50.3");
map.put("produce_time","2022-10-11");
map.put("car_type","燃油车");
int count = sqlSession.insert("insertCar",map);
System.out.println("插入了" + count + "条数据");
```

xml文件如下：

```xml
<insert id="insertCar">
        insert into t_car
            (car_num,brand,guide_price,produce_time,car_type)
        values
            (#{car_num},#{brand},#{guide_price},#{produce_time},#{car_type})
</insert>           
```

**注意：#{}中必须填写map的key，否则会插入null**

## POJO类

```java
package pojo;

public class Car {
    private Long id;
    private String carNum;
    private String brand;
    private Double guidePrice;
    private String produceTime;
    private String carType;

    public Car() {
    }

    public Car(Long id, String carNum, String brand, Double guidePrice, String produceTime, String carType) {
        this.id = id;
        this.carNum = carNum;
        this.brand = brand;
        this.guidePrice = guidePrice;
        this.produceTime = produceTime;
        this.carType = carType;
    }

    @Override
    public String toString() {
        return "Car{" +
                "id=" + id +
                ", carNum='" + carNum + '\'' +
                ", brand='" + brand + '\'' +
                ", guidePrice=" + guidePrice +
                ", produceTime='" + produceTime + '\'' +
                ", carType='" + carType + '\'' +
                '}';
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getCarNum() {
        return carNum;
    }

    public void setCarNum(String carNum) {
        this.carNum = carNum;
    }

    public String getBrand() {
        return brand;
    }

    public void setBrand(String brand) {
        this.brand = brand;
    }

    public Double getGuidePrice() {
        return guidePrice;
    }

    public void setGuidePrice(Double guidePrice) {
        this.guidePrice = guidePrice;
    }

    public String getProduceTime() {
        return produceTime;
    }

    public void setProduceTime(String produceTime) {
        this.produceTime = produceTime;
    }

    public String getCarType() {
        return carType;
    }

    public void setCarType(String carType) {
        this.carType = carType;
    }
}

```

java程序

```java
Car car = new Car();
car.setCarNum("103");
car.setCarType("燃油车");
car.setGuidePrice(33.33);
car.setProduceTime("2024-02-02");
car.setBrand("奔驰C200");
int count = sqlSession.insert("insertCar",car);
```

xml代码

```xml
    <insert id="insertCar">
        insert into t_car
            (car_num,brand,guide_price,produce_time,car_type)
        values
        <!--这里填写的是pojo类的属性名-->
            (#{carNum},#{brand},#{guidePrice},#{produceTime},#{carType})
    </insert>
```

**经过测试得出结论：**

**如果采用map集合传参，#{} 里写的是map集合的key，如果key不存在不会报错，数据库表中会插入NULL。**

**如果采用POJO传参，#{} 里写的是get方法的方法名去掉get之后将剩下的单词首字母变小写（例如：getAge对应的是#{age}，getUserName对应的是#{userName}），如果这样的get方法不存在会报错。**

注意：其实传参数的时候有一个属性parameterType，这个属性用来指定传参的数据类型，不过这个属性是可以省略的

```xml
<insert id="insertCar" parameterType="pojo.Car">
        insert into t_car
            (car_num,brand,guide_price,produce_time,car_type)
        values
            (#{carNum},#{brand},#{guidePrice},#{produceTime},#{carType})
</insert>
```

# Delete

需求根据car_num删除

```java
@Test
    public void testDelete(){
        SqlSession sqlSession = null;
        try{
            sqlSession = SqlSessionUtil.openSession();
            int count = sqlSession.delete("deleteByCarNum","102");
            System.out.println("删除了" + count + "条数据");
            sqlSession.commit();
        }catch (Exception e){
            if(sqlSession != null){
                sqlSession.rollback();
            }
            e.printStackTrace();
        }finally {
            if(sqlSession != null){
                sqlSession.close();;
            }
        }
    }
```

```xml
    <delete id="deleteByCarNum">
        delete from t_car where car_num = #{car_num}
    </delete>
```

**注意： 当占位符只有一个的时候，#{}里面的值可以随便写 **

# Update

需求：修改id=1的Car信息，car_num为102，brand为比亚迪汉，guide_price为30.23，produce_time为2018-09-10，car_type为电车

```java
@Test
    public void testUpdate(){
        SqlSession sqlSession = null;
        try{
         sqlSession = SqlSessionUtil.openSession();

         Car car = new Car();
         car.setId(1l);
         car.setBrand("比亚迪宋");
         car.setCarNum("102");
         car.setGuidePrice(30.23);
         car.setProduceTime("2024-03-03");
         car.setCarType("电车");
         sqlSession.update("updateCarByPojo",car);
        }catch (Exception e){
            if(sqlSession != null){
                sqlSession.rollback();
            }
        }finally {
            if(sqlSession != null){
                sqlSession.close();
            }
        }
    }
```

```xml
<update id="updateCarByPojo">
        update t_car set 
        	car_num = #{carNum},brand = #{brand},guide_price = #{guidePrice},produce_time = #{produceTime},car_type = #{carType}
        where id = #{id}
</update>
```

# Select

## 查询一条数据

```java
@Test
    public void testSelectByOne(){
        SqlSession sqlSession = null;
        try{
            sqlSession = SqlSessionUtil.openSession();
            Car car = (Car)sqlSession.selectOne("selectByOne",2l);
            System.out.println(car);
        }catch (Exception e){
            if(sqlSession != null){
                sqlSession.rollback();
            }
        }finally {
            if(sqlSession != null){
                sqlSession.close();
            }
        }
    }
```

```xml
<select id="selectByOne" resultType="pojo.Car">
        select * from t_car where id = #{id};
</select>
```

**想让mybatis查询之后返回一个Java对象的话，至少你要告诉mybatis返回一个什么类型的Java对象，可以在<select>标签中添加resultType属性，用来指定查询要转换的类型**

运行结果如下：

![image-20240308201043739](D:\study-for-qiuzhao\MyBatis\assets\image-20240308201043739.png)

我们发现有些值是null与数据库中的值不匹配

```xml
<select id="selectByOne" resultType="pojo.Car">
        select id, car_num as carNum, brand, guide_price as guidePrice, produce_time as produceTime, car_type as carType
        from t_car where id = #{id};
</select>
```

![image-20240308201256806](D:\study-for-qiuzhao\MyBatis\assets\image-20240308201256806.png)

原因是因为，查询结果的字段名和java类的属性名对应不上，这里采用`as`关键字处理

## 查询多条数据

```xml
    <select id="selectCarByAll" resultType="pojo.Car">
        select id, car_num as carNum, brand, guide_price as guidePrice, produce_time as produceTime, car_type as carType
        from t_car;
    </select>
```

```java
public void testSelectByAll(){
        SqlSession sqlSession = null;
        try{
            sqlSession = SqlSessionUtil.openSession();
            List<Objects> cars = sqlSession.selectList("selectCarByAll");
            cars.forEach(car-> System.out.println(car));
        }catch (Exception e){
            if(sqlSession != null){
                sqlSession.rollback();
            }
        }finally {
            if(sqlSession != null){
                sqlSession.close();
            }
        }
    }
```

# 关于SQL Mapper中的namespace

在SQL Mapper配置文件中<mapper>标签的namespace属性可以翻译为命名空间，这个命名空间主要是为了防止sqlId冲突的。

CarMapper.xml和CarMapper2.xml文件中都有 id="selectCarAll"

将CarMapper2.xml配置到mybatis-config.xml文件中。

此时就会报错：

selectCarAll在Mapped Statements集合中不明确（请尝试使用包含名称空间的全名，或重命名其中一个条目）
selectCarAll重名了，你要么在selectCarAll前添加一个名称空间，要有你改个其它名字。

```java
    List<Object> cars = sqlSession.selectList("car2.selectCarAll");
```

