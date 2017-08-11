# 11.2.3声明切入点

recall此切入点确定感兴趣的连接点，因此使我们可以管理当通知执行时。Spring AOP仅支持Spring beans的方法执行连接点。因此你可以认为一个切点就是匹配符合规则的Spring beans的执行的方法。

一个切点声明由两个部分组成：

* 一个由名字和任何参数构成的签名\(方法\)
* 一个切点表达式--决定Spring beans的哪些方法是我们感兴趣的

在@AspectJ 注解格式的AOP

* 一个切点签名，由一个正常的方法定义提供（**作为切点签名的方法的返回值必须是void**）

事实证明，具有返回值也是没有任何问题的

```
@Pointcut("execution(* transfer(..))")
public String anyOldTransfer() {
    System.out.println("heihei");

    return "heihei";
}
```

* 一个切点表达式，使用@Pointcut注解声明

一个示例将帮助你清楚的区别一个切点签名和一个切点表达式。下面的例子定义了一个名叫**‘anyOldTransfer’**的切点，其将会匹配执行的任何名为**‘transfer’**的方法：

```
@Pointcut("execution(* transfer(..))")
private void anyOldTransfer(){..}
```

切点表达式也就是@Pointcut注解里的值遵守AspectJ 5 切点表达式。要查看对于Aspect 切点语言完全的讨论，查看[https://www.eclipse.org/aspectj/doc/released/progguide/index.html](https://www.eclipse.org/aspectj/doc/released/progguide/index.html)（扩展， [https://www.eclipse.org/aspectj/doc/released/adk15notebook/index.html](https://www.eclipse.org/aspectj/doc/released/adk15notebook/index.html)）或者介绍AspectJ的一本书籍，例如：“Eclipse AspectJ”， “AspectJ in Action”。

## 支持切点指示符

Spring AOP 支持下面的AspectJ pointcut designators\(PCD\)在切点表达式中使用：

> 完全的AspectJ pointcut language 支持额外的切点指示符但在Spring中并不支持。这些是：
>
> call, get, set, preinitialization, staticinitialization, initialization, handler, adviceexecution, withincode, cflow, cflowbelow, if, @this和@withincode.在切点表达式中使用这些切点指示符，当被Spring AOP拦截时会抛出一个IllegalArgumentException.
>
> Spring AOP支持的切点指示符集合可能在未来的版本中扩展，以支持更多的AspectJ 切点指示符

* execution - 匹配执行方法的连接点，这是你将使用的主要的切点指示符当与Spring AOP一起工作时
* within - 用于匹配指定类型内的方法执行
* this - 用于匹配当前AOP代理对象类型的执行方法；注意是AOP代理对象的类型匹配，这样就可能包括引入接口也类型匹配
* target - 用于匹配当前目标对象类型的执行方法；注意是目标对象的类型匹配，这样就不包括引入接口也类型匹配
* args - 用于匹配当前执行的方法传入的参数为指定类型的执行方法
* @target - 用于匹配当前目标对象类型的执行方法，其中目标对象持有指定的注解
* @args - 用于匹配当前执行的方法传入的参数持有指定注解的执行
* @within - 用于匹配所以持有指定注解类型内的方法
* @annotation - 用于匹配当前执行方法持有指定注解的方法

......



## 组合切点表达式

切点表达式可以使用"&&", "\|\|", "!"结合 ，也支持通过名字来引用切点表达式。下面的例子展示了三个切点表达式：**anyPublicOperation**\(一个执行方法连接点代表任何public 方法的执行\)；**inTrading**\(匹配执行的方法是否在trading模块下\);**tradingOperation**\(匹配如如果一个执行方法代表一个公开的方法并且在trading模块下\)

```
@Pointcut("execution(public * *(..))")
private void anyPublicOperation(){}

@Pointcut("within(com.xyz.someapp.trading..*)")
private void inTrading(){}

@Pointcut("anyPublicOperation() && inTrading()")
private void tradingOperation(){}
```

最好的做法是构建更加复杂的切点表达式通过较小的组件命名上。当通过名字来引用切点时，java的正常的可见性适用于此（你可以在相同的类型里看见私有的切点，保护的切点从继承中，在任何地点看见公开的切点）。可见性不会影响切点的匹配



## 分享常用的切点定义

```java
@Aspect
public class SystemArchitecture{
    @Pointcut("within(com.xyz.someapp.web..*)")
    public void inWebLayer(){}
    
    @Pointcut("within(com.xyz.someapp.service..*)")
    public void inServiceLayer(){}
    
    @Pointcut("execution(* com.xyz.someapp..service.*.*(..))")
    public void businessService(){}
    
    @Pointcut("execution(* com.xyz.someapp.dao.*.*(..)")
```



