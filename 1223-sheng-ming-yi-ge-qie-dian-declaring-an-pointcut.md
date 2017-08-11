# 11.2.3声明切入点

recall此切入点确定感兴趣的连接点，因此使我们可以管理当通知执行时。Spring AOP仅支持Spring beans的方法执行连接点。因此你可以认为一个切点就是匹配符合规则的Spring beans的执行的方法。

一个切点声明由两个部分组成：

* 一个由名字和任何参数构成的签名\(方法\)
* 一个切点表达式--决定Spring beans的哪些方法是我们感兴趣的

在@AspectJ 注解格式的AOP

* 一个切点签名，由一个正常的方法定义提供（**作为切点签名的方法的返回值必须是void**）
* 一个切点表达式，使用@Pointcut注解声明

一个示例将帮助你清楚的区别一个切点签名和一个切点表达式。下面的例子定义了一个名叫**‘anyOldTransfer’**的切点，其将会匹配执行的任何名为**‘transfer’**的方法：

```
@Pointcut("execution(* transfer(..))")
private void anyOldTransfer(){..}
```



