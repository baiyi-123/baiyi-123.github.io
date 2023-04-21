# 第3章-第3节

## 一、知识点

面向接口编程、什么是spring、什么是IOC、IOC的使用、依赖注入

## 二、目标

- 了解什么是spring

- 理解IOC的思想和使用

- 了解IOC的bean的声明周期

- 理解什么是依赖注解


## 三、内容分析

- 重点
  - 了解什么是spring
  - 理解IOC的思想
  
  - 掌握IOC的使用
- 难点
  - 理解IOC的思想
  - 掌握IOC的使用

## 四、内容

### 1、Spring

#### 1.1 简介

spring是分层的java SE/EE应用full-stack轻量级开源框架，以IOC（Inverse Of Control：控制反转）和AOP（Aspect Oriented Programming：面向切面编程）为内核，提供了展现层Spring MVC和持久层Spring JDBC以及业务层事务管理等众多的企业级应用技术，还能整合开源世界众多著名的第三方框架和类库，逐渐成为使用最多的Java EE企业级应用开源框架。



#### 1.2 优势

- 开源免费的轻量级框架
- 低侵入式设计，代码污染极低
- 支持事务的处理，对框架整合的支持
- 以控制反转（IOC），面向切面编程（AOP）为内核
- 方便解耦，简化开发：将对象的创建交给spring 无需new
- 提供了展现层SpringMVC和持久层Spring JDBCTemplate以及业务层事务管理等众多企业级应用技术
- 能整合开源世界众多第三方框架和类库

#### 1.3 引入依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.1.11.RELEASE</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.6</version>
</dependency>
```

### 2、Ioc

#### 2.1 概念

**控制反转**（IoC，Inversion of Control）是一个概念，是一种思想。指将传统上由程序代码直接操控的对象调用权交给容器，通过容器来实现对象的装配和管理。[控制反转](https://so.csdn.net/so/search?q=控制反转&spm=1001.2101.3001.7020)就是对对象控制权的转移，从程序代码本身反转到了外部容器。通过容器实现对象的创建，属性赋值，依赖的管理。

**以前我们在代码中，使用new 构造方法创建对象，现在不用了， 由容器代替开发人员管理对象。**

#### 2.2 传统方式和ioc方式的区别

- #### 传统

  我们要实现某一个功能或者说是完成某个业务逻辑时至少需要两个或以上的对象来协作完成，每个对象在需要使用他的合作对象时，自己均要使用像new object() 这样的语法来将合作对象创建出来，这个合作对象是由自己主动创建出来的，创建合作对象的主动权在自己手上，自己需要哪个合作对象，就主动去创建，创建合作对象的主动权和创建时机是由自己把控的，而这样就会使得对象间的耦合度高了，A对象需要使用合作对象B来共同完成一件事，A要使用B，那么A就对B产生了依赖，也就是A和B之间存在一种耦合关系，并且是紧密耦合在一起。

- #### ioc

  创建合作对象B的工作是由Spring来做的，Spring创建好B对象，然后存储到一个容器里面，当A对象需要使用B对象时，Spring就从存放对象的那个容器里面取出A要使用的那个B对象，然后交给A对象使用，至于Spring是如何创建那个对象，以及什么时候创建好对象的，A对象不需要关心这些细节问题(你是什么时候生的，怎么生出来的我可不关心，能帮我干活就行)，A得到Spring给我们的对象之后，两个人一起协作完成要完成的工作即可。

下面用制造汽车需要引擎、轮胎等零件的场景解释含义

- 假设现在要生产汽车、货车、客车、出租车，它们都是使用【1代引擎】

  <img src="assets/image-20220615171322298.png" alt="image-20220615171322298" style="zoom:50%;" />

代码如下：

```java
public class EngineV1 {

    private String name = "1代引擎";

    public String getName() {
        return name;
    }
}
```

```java
/**
 * 汽车
 */
public class Car {

    private String carName = "小汽车";

    private EngineV1 engineV1;

    public Car(EngineV1 engineV1) {
        this.engineV1 = engineV1;
    }

    public void info() {
        System.out.println(carName + "使用的是" + engineV1.getName());
    }
}
```

```java
/**
 * 货车
 */
public class Van {

    private String carName = "货车";

    private EngineV1 engineV1;

    public Van(EngineV1 engineV1) {
        this.engineV1 = engineV1;
    }

    public void info() {
        System.out.println(carName + "使用的是" + engineV1.getName());
    }
}
```

测试

```java
Car car = new Car(new EngineV1());
car.info();

Van van = new Van(new EngineV1());
van.info();

// 结果
// 小汽车使用的是1代引擎
// 货车使用的是1代引擎
```

总结：现在可以看出不管是汽车还是货车，都是依赖于【1代引擎】的，耦合度很高，没有【1代引擎】就没有这些车。假如我们还有很多其他类型的车都是依赖于【1代引擎】，有一天我想**把所有的【1代引擎】换成 【2代引擎】我该怎么做**？全局搜索【1代引擎】修改为 【2代引擎】，想想都有点麻烦。



- 我们需要换一种思路来实现，解决上述问题。

  <img src="assets/image-20220615172210336.png" alt="image-20220615172210336" style="zoom:50%;" />

```java
/**
 * 引擎接口
 */
public interface IEngine {
    String getName();
}
```

```java
public class EngineV1 implements IEngine{

    private String name = "1代引擎";

    public String getName() {
        return name;
    }
}
```

```java
public class EngineV2 implements IEngine{

    private String name = "2代引擎";

    public String getName() {
        return name;
    }
}
```

```java
/**
 * 汽车
 */
public class Car {

    private String carName = "小汽车";

    // 现在不依赖于具体哪个引擎，直接对接引擎接口
    private IEngine engine;

    public Car(IEngine engine) {
        this.engine = engine;
    }

    public void info() {
        System.out.println(carName + "使用的是" + engine.getName());
    }
}
```

```java
/**
 * 货车
 */
public class Van {

    private String carName = "货车";

    private IEngine engine;

    public Van(IEngine engine) {
        this.engine = engine;
    }

    public void info() {
        System.out.println(carName + "使用的是" + engine.getName());
    }
}
```

测试

```java
Car car = new Car(new EngineV1());
car.info();

Van van = new Van(new EngineV1());
van.info();

// 结果
// 小汽车使用的是1代引擎
// 货车使用的是1代引擎
```

```java
Car car = new Car(new EngineV2());
car.info();

Van van = new Van(new EngineV2());
van.info();

// 结果
// 小汽车使用的是2代引擎
// 货车使用的是2代引擎
```

总结：代码中不再依赖于具体，而是依赖于抽象容器，即要针对接口编程，不针对实现编程。过去思维中想要什么依赖，需要自己去 “拉” 改为抽象容器主动 “推” 给你，你只管使用实体就可以了。这是**依赖倒转** (DIP) 的一种表现形式。由原来的**汽车依赖引擎**转化成了**引擎依赖引擎接口**了，进行了反转。现在生产汽车的时候，给汽车什么引擎它就用什么引擎，汽车就脱离了只能用【1代引擎】绝对绑定形式了。

我们还可以接着优化，现在是使用引擎的时候我们手动去new的。假如项目中有很多的地方使用了【1代引擎】，要换成【2代引擎】，即把全部的```new EngineV1()  ```改成```new EngineV2()```。



- 针对上面的问题进行优化。我们可以先创建一个仓库出来，把引擎先创建好放在容器里面。车辆要用引擎的时候直接从仓库里面获取。

  <img src="assets/image-20220615172352639.png" alt="image-20220615172352639" style="zoom:50%;" />

```java
/**
 * 容器
 */
public class Container {

    private Map<String, Object> map = new HashMap<String, Object>();

    public Container() {
        // 此处利用可以利用读取配置的方式，利用反射动态创建出对象
        map.put("engine", new EngineV1());
    }

    public Object getBean(String name) {
        return map.get(name);
    }
}
```

测试 

```java
// 创建容器
Container container = new Container();

Car car = new Car((IEngine) container.getBean("engine"));
car.info();

Van van = new Van((IEngine) container.getBean("engine"));
van.info();

// 结果
// 小汽车使用的是1代引擎
// 货车使用的是1代引擎
```

现在要把【1代引擎】化成【2代引擎】，我们只需要修改容器里面的引擎即可，只要修改一处地方。

```java
map.put("engine", new EngineV2());
```

上面的测试结果就变成了

```java
// 创建容器
Container container = new Container();

Car car = new Car((IEngine) container.getBean("engine"));
car.info();

Van van = new Van((IEngine) container.getBean("engine"));
van.info();

// 结果
// 小汽车使用的是2代引擎
// 货车使用的是2代引擎
```

总结：这就是IOC要做的事情。把创建对象的过程交给spring管理，我们需要用的时候直接拿就行。

### 3、基本使用

#### 3.1 方式一：配置文件

- ### 创建xml文件 resources/文件名.xml

  ![image-20220610144531728](assets/image-20220610144531728.png)

- ### 创建类

- ### xml配置

  ```xml
  <!--  id：spring生成对象的时候，放到容器里面，会给每一个对象生成一个id，用于区分对象  -->
  <!--  class：告诉它使用哪个类  -->
  <bean id="student" class="com.company.eneity.Student">
  ```

- ### 获取对象

  - ##### 通过id获取

    ```java
    public static void main(String[] args) {
        // 生成spring容器
        // 读取spring的xml文件，根据内容生成对应的对象，放到容器里面
        ApplicationContext ctx = new ClassPathXmlApplicationContext("spring.xml");
        // 获取对象，传入id，通过id获取对象
        Student student = (Student) ctx.getBean("student");
        System.out.println(student);
    }
    ```

  - ##### 通过类获取

    ```java
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("spring.xml");
        Student student1 = ctx.getBean(Student.class);
        System.out.println(student1);
    }
    ```

#### 3.2 方式二：注解

- #### 创建类

  ```java
  // spring扫描到这个注解的时候，会自动生成对象
  @Component
  public class Teacher {}
  ```

- #### 配置xml的component-scan

  ```xml
  <!-- 扫描包，会去扫描注解，有对应注解的会自动生成对象 -->
  <context:component-scan base-package="com.company"></context:component-scan>
  ```

- #### 获取对象

  ```java
  @Component
  public class TeacherDao {
      // 自动从容器里面获取对应类型的对象
      @Autowired
      private Teacher teacher;
      public void test(){
          System.out.println(teacher);
      }
  }
  
  ApplicationContext ctx = new ClassPathXmlApplicationContext("spring.xml");
  TeacherDao teacherDao = ctx.getBean(TeacherDao.class);
  teacherDao.test();
  ```

#### 3.3 bean的生命周期

spring默认的是单例对象可以通过scope修改

- ### 单例对象

  scope="singleton"

  创建bean的时候使用默认的无参构造函数

  一个应用只有一个对象的实例，它的作用范围就是整个引用

  生命周期

  ​		对象出生：当应用加载，创建容器时，对象就被创建

  ​		对象活着：只要容器在，对象一直活着

  ​		对象死亡：当应用卸载，销毁容器时，对象就被销毁了

  #### 测试

  ```java
  // 创建spring容器
  // 创建容器的时候，对象已经生成
  ApplicationContext ctx = new ClassPathXmlApplicationContext("spring.xml");
  
  // 获取对象只是从容器里面拿出来
  Student student1 = (Student) ctx.getBean("student");
  Student student2 = (Student) ctx.getBean("student");
  
  System.out.println(student1 == student2);
  // 结果 true
  ```

  

- ### 多例对象

  scope="prototype"

  每次访问对象时，都会被重新创建对象实例

  生命周期

  ​		对象出生：当使用对象时，创建新的对象实例

  ​		对象活着：对象在使用中，对象就一直活着

  ​		对象死亡：当对象长时间不用，被java垃圾回收机制回收

  #### 测试

  ```java
  // 创建spring容器
  // 创建容器的时候对象还没有生成
  ApplicationContext ctx = new ClassPathXmlApplicationContext("spring.xml");
  
  // 获取对象的时候才创建对象
  Student student1 = (Student) ctx.getBean("student");
  Student student2 = (Student) ctx.getBean("student");
  
  System.out.println(student1 == student2);
  
  // 结果 false
  ```

  

### 4、依赖注入

IoC的一个重点是在系统运行中，动态的向某个对象提供它所需要的其他对象。这一点是通过DI（Dependency Injection，依赖注入）来实现的。

#### 4.1 使用构造函数

前提：类中需要提供一个对应参数列表的构造函数

标签：constructor-arg

标签属性：

​		index：指定参数在构造函数参数列表中的索引位置

​		type：指定参数在构造函数中的数据类型

​		name：指定参数在构造函数中的名称，用这个找给谁赋值

​		value：它能赋的值是基本数据类型和String类型

​		ref：它能赋的值是其他bean类型，也就是说，必须得是在配置文件中配置过的bean

- #### 基本数据类型

  基本数据类型直接用value赋值

  ```xml
  <bean id="student" class="com.company.eneity.Student">
      <!-- constructor-arg调用有参否则函数，必须有写构造函数 -->
      <constructor-arg name="name" value="张三"></constructor-arg>
      <constructor-arg name="age" value="18"></constructor-arg>
  </bean>
  ```

- #### 引用数据类型

  引用数据类型使用ref赋值

  ```xml
  <bean id="student" class="com.company.eneity.Student">
      <!-- constructor-arg调用有参否则函数，必须有写构造函数 -->
      <constructor-arg name="name" value="张三"></constructor-arg>
      <constructor-arg name="age" value="18"></constructor-arg>
      <constructor-arg name="studentMarjor" ref="studentMarjor"></constructor-arg>
  </bean>
  ```

#### 4.2 使用set赋值

标签：property

标签属性：

​		name：找的是类中set方法后面的部分

​		value：给属性赋值的是基本数据类型和String类型

​		ref：给属性赋值是其他bean类型的

- #### 基本数据类型

  ```xml
  <bean id="student2" class="com.company.eneity.Student2">
      <!-- property调用set的方法，必须set方法 -->
      <property name="name" value="张三"></property>
      <property name="age" value="18"></property>
  </bean>
  ```

- #### 引用数据类型

  ```xml
  <bean id="student" class="com.company.eneity.Student">
      <property name="name" value="张三"></property>
      <property name="age" value="18"></property>
      <property name="studentMarjor" ref="studentMarjor"></property>
  </bean>
  <bean id="studentMarjor" class="com.company.eneity.StudentMarjor">
      <property name="name" value="软件工程"></property>
      <property name="studentNum" value="80"></property>
  </bean>
  ```

### 5、小结

本章节中我们学习了什么是Spring，spring的优势是什么，为什么要用spring，同时学习了spring的一个核心功能IOC(控制反转)，理解了控制反转的作用以及为什么要用控制反转，同时理解了什么是依赖注入，学会了通过XML文件配置IOC和不同方式的依赖注入。

下一节中，我们将会学习一些Spring的常用注解，学会使用注解进行开发，去理解注解开发和配置文件开发的区别和优势。

## 五、练习

- IoC 是什么？

  IOC是一种编程思想，由主动的编程变成被动的接收，就是对象由Spring来创建，管理，装配

- 常见的 ApplicationContext 实现方式有哪些？

  ClassPathXmlApplicationContext， FileSystemXmlApplicationContext，XmlWebApplicationContext

- SpringBean的作用域分为(Singleton)、(prototype)，其中(singleton)是默认的作用域，如果要改变的话应该使用(scope="prototype")

- 要对应用数据类型进行依赖注入需要使用标签中的哪个属性:

- index:指定参数在构造函数的位置

- value:基本数据类型

- ref:bean容器中存的其他类型

- 使用配置文件来实现ioc

- 

## 六、预习

- Spring的注解
- 创建对象的注解
- 注入对象的注解
- 其他常用注解