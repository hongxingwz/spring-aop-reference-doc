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
* args
* @target
* @args
* @within
* @annotation





