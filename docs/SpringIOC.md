# Spring IOC 注解

> ***Spring三大核心思想***
>
>  	1. **控制反转(IOC)**
>  	2. **依赖注入**
>       	1. 属性注入 
>               	2. 构造方法注入 
>                   	3. 工厂方法注入 
>                       	4. 注解注入
>  	3. **面向切面编程(AOP)**

---

### spring中的注解

如果用注解需要在配置文件中设置

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <!--告知spring在创建容器时要扫描的包，配置所需要的标签不是在beans的约束中，而是一个名称为
    context名称空间和约束中-->
    <context:component-scan base-package="com.itheima"/>
</beans>
```

#### 1、用于创建对象

> 其作用和在xml配置文件中编写一个`<bean>`标签的实现的功能一样

- **@Component**：

  > 作用：用于把当前对象存入spring容器中
  >
  > 属性： 
  >
  > ​		value ：用来指定bean的id。不写时，默认值为当前类名，且首字母改小写

下面三个注解的作用和属性和Component一样

- **@Controller**:

  > 一般用于表现层

- **@Service**:

  > 一般用于服务层

- **@Repository**：

  > 一般用于持久层

#### 2、用于注入数据

> 其作用和在xml配置文件中编写一个`<property>`标签的实现的功能一样

- **@Autowired**：

  > 作用：自动按照类型注入。只要容器中有唯一一个bean对象类型和要注入的变量类型匹配
  >
  > ​			如果IOC有多个类型匹配时：会根据变量名匹配IOC容器中的key值
  >
  > 出现位置：
  >
  > ​		可以是成员，也可以是方法上
  >
  > 细节：
  >
  > ​		set方法办的不是必须的 

- **@Qualifier**：

  > 作用：在按照类中注入的基础之上再按照名称注入。它在给类成员注入时不能单独使用。但是在给方法参数注入时可以
  >
  > 属性：
  >
  > ​			value：用于指定注入bean的id。

- **@Resource**：

  > 作用：直接按照bean的id注入。它可以独立使用
  >
  > 属性：
  >
  > ​	name：用于指定bean的id。

**以上三个注入都只能注入其他bean类型的数据，而基本类型和String类型无法使用上述注解实现。**

**另外，集合类型的注入只能通过XML来实现。**

- **==@Value==**

  > 作用：用于注入基本类型和String类型的数据
  >
  > 属性：
  >
  > ​		value：用于指定数据的值。它可以使用spring中SpEL(也就是spring的el表达式）
  >
  > ​				SpEL的写法：`${表达式}`

#### 3、用于改变作用范围

> 他们的作用就和在bean标签中使用scope属性实现的功能是一样的

- **@Scope** 

  > 作用：用于指定bean的作用范围
  >
  > 属性：
  >
  > ​		value：指定范围的取值。常用取值：`singleton` `prototype`

#### 4、和生命周期相关 (了解)

> 他们的作用就和在bean标签中使用 `init-method` 和 `destroy-methode` 的作用是一样的

- **@PreDestroy**

  > 作用：用于指定销毁方法
 - **@PostConstruct**

   > 作用：用于指定初始化方法

### spring 中的新注解(配置)

- **@Configuration**

  > 作用：指定当前类为一个配置类
  >
  > 细节：当配置类作为AnnotationConfigApplicationContext对象创建的参数时，该注解可以不写。

- **@ComponentScan**

  > 作用：用于通过注解指定spring在创建容器时要扫描的包
  >
  > value：
  >
  > ​		它和basePackages的作用是一样的，都是用于指定创建容器时要扫描的包。我们使用此注解就等同于在xml中配置了:`<context:component-scan base-package="com.itheima"></context:component-scan>`

- **@Bean**

> 作用：用于把当前方法的返回值作为bean对象存入spring的ioc容器中
>
> 属性:
>
> ​		name:用于指定bean的id。当不写时，默认值是当前方法的名称
>
> 细节：
>
> > 当我们使用注解配置方法时，如果方法有参数，spring框架会去容器中查找有没有可用的bean对象。
> >
> > 查找的方式和Autowired注解的作用是一样的

- **@Import**

> 作用：用于导入其他的配置类
>
> 属性：
>
> ​		value：用于指定其他配置类的字节码。
>
> ​		当我们使用`Import`的注解之后，有`Import`注解的类就父配置类，而导入的都是子配置类

- **@PropertySource**

  > 作用：用于指定properties文件的位置
  >
  > 属性：
  >
  > ​		value：指定文件的名称和路径。
  >
  > ​				关键字：`classpath`，表示类路径下

### spring整合junit

```java
/**
 * 使用Junit单元测试：测试我们的配置
 * Spring整合junit的配置
 *      1、导入spring整合junit的jar(坐标)
 *      2、使用Junit提供的一个注解把原有的main方法替换了，替换成spring提供的
 *             @Runwith
 *      3、告知spring的运行器，spring和ioc创建是基于xml还是注解的，并且说明位置
 *          @ContextConfiguration
 *                  locations：指定xml文件的位置，加上classpath关键字，表示在类路径下
 *                  classes：指定注解类所在地位置
 *
 *   当我们使用spring 5.x版本的时候，要求junit的jar必须是4.12及以上
 */
```

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.2.2.RELEASE</version>
</dependency>
```

