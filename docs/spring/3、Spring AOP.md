# Spring AOP

## XML

### 1、概念

##### **1.1 AOP （面向切面编程）**

> ​		在软件业，**AOP**为`Aspect Oriented Programming`的缩写，意为：[面向切面编程](https://baike.baidu.com/item/面向切面编程/6016335)，通过[预编译](https://baike.baidu.com/item/预编译/3191547)方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP是[OOP](https://baike.baidu.com/item/OOP)的延续，是软件开发中的一个热点，也是[Spring](https://baike.baidu.com/item/Spring)框架中的一个重要内容，是[函数式编程](https://baike.baidu.com/item/函数式编程/4035031)的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的[耦合度](https://baike.baidu.com/item/耦合度/2603938)降低，提高程序的可重用性，同时提高了开发的效率。 

##### **1.2 AOP 的作用及优势**

> **作用：**
>
> ​		在程序运行期间，不修改源码对已有方法进行增强。
>
> **优势：**
>
> ​		减少重复代码
>
> ​		提高开发效率
>
> ​		维护方便

**1.3 AOP 的实现方式**

> 使用动态代理技术

### 2、spring AOP 配置

 1. 把通知bean交给spring管理
   2. 使用`aop:config`标签表明开始AOP的配置
   3. 使用`aop:aspect`标签配置切面
       `id`：给切面提供一个唯一标识。
       `ref`：引用配置好的通知类 bean 的 id。
   4. 在`aop:aspect`内部使用对应标签来配置通知类型
       -  `aop:before`     用于配置前置通知。指定增强的方法在切入点方法之前执行
               `method`:   用于指定通知类中的增强方法名称
               `pointcut`: 用于指定切入点表达式
        - `aop:after-returning`    用于配置后置通知
        - `aop:after-throwing`     用于配置异常通知
        - `aop:after`      用于配置最终通知
   5. 用 `aop:pointcut` 配置切入点表达式
       切入点表达式写法
       
       - 关键字：`execution(表达式)`
       
       - 表达式：
                 访问修饰符  返回值  包名.包名.包名...类名.方法名(参数列表)
       
       - 表达式写法：
       
         - 标准写法
       
           `public void com.szz.service.impl.AccountService.saveAccount()`
       
         - 访问修饰符可以省略
       
           `void com.szz.service.impl.AccountService.saveAccount()`
       
         - 返回值可以使用通配符，表示返回任意值
       
         ​		`* com.szz.service.impl.AccountService.saveAccount() `
       
         - 包名可以使用通配符，表示任意包。但是有几级包，就需要写几个 `*.`
       
           `* *.*.*.*.AccountService.saveAccount() `
       
         - 包名也可以使用一个 `..` 表示当前包及其子包
       
           `* *..AccountService.saveAccount() `
       
         - 类名和方法名都可以使用`*`来实现通配
       
           `* *..*.*() `
       
         - 参数列表：
       
           > 可以直接写数据类型：
           >         基本类型直接写名称           	`int`
           >         引用类型写包名.类名的方式   `java.lang.String`
           > 可以使用通配符 `*` 表示任意类型，但是必须有参数
           > 可以使用 `..` 表示有无参数均可，有参数可以是任意类型
       
         - 全通配写法：
       
         ​		`* *..*.*(..)`
       
         - 实际开发中切入点表达式的通常写法：
       
           > 切到业务层实现类下的所有方法
           >
           > ​		`* com.szz.service.impl.*.*(..)`

```xml
<!--配置Logger类-->
<bean id="logger" class="com.szz.utils.Logger">
</bean>
<!--配置AOP-->
<aop:config>
<!--配置切面-->
<aop:aspect id="logAdvice" ref="logger">
    <!--配置通知，建立通知方法和切入点方法的关联-->
    <aop:before method="printLog"
                pointcut="execution(public void com.szz.service.impl.AccountService.saveAccount())"/>
    </aop:aspect>
</aop:config>
```

### 3、环绕通知

**配置方式:** 

```xml
<aop:config> <aop:pointcut expression="execution(* com.szz.service.impl.*.*(..))"
id="pt1"/>
    <aop:aspect id="txAdvice" ref="txManager">
    <!-- 配置环绕通知 --> 
    <aop:around method="transactionAround" pointcut-ref="pt1"/>
    </aop:aspect>
</aop:config>
```

**aop:around：**

**作用：**

​		用于配置环绕通知

**属性：**

​		`method`：指定通知中方法的名称。

​		`pointct`：定义切入点表达式

​		`pointcut-ref`：指定切入点表达式的引用

**说明：**

​		它是 spring 框架为我们提供的一种可以在代码中手动控制增强代码什么时候执行的方式。

**注意：**

​		通常情况下，环绕通知都是独立使用的

## 注解

