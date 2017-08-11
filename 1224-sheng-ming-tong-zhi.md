# 11.2.4 声明通知

通知与切点表达式相关联，在通过切点匹配到的执行方法之前，之后，或环绕执行。切换表达式既可以是简单的以命名的切点引用，或是一个切点表达式

## 前置通知（Before Advice\)

前置通知使用@Before注解在切面中声明

```java
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

@Aspect
public class BeforeExample{

    @Before("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")
    public void doAccessCheck(){
    }
}
```

如果使用切点表达式我们可以重写上面的例子像下面这样：

```java
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

@Aspect
public class BeforeExample{
    @Before("execution(* com.xyz.myapp.dao.*.*(..))")
    public void doAccessCheck(){
        //...
    }
```

## 返回之后通知（After returning advice\)

当匹配的方法执行并正常的返回时返回后通知会执行。它通过使用@AfterReturning注解执行：

```java
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.AfterReturning;

@Aspect
public class AfterReturningExample{
    @AfterReturning("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")
    public void doAccessCheck(){
        ...
    }
```

> 注：当然可以多个通知声明，其他成员也是一样的，只要在同一个切面内声明就可以了。我们仅展示一个通知在这些例子中

一些时候你需要访问通知体来获取真实的返回值。你可以使用@AfterReturing绑定返回值的形式，像这样：

```java
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.AfterReturing;

@Aspect
public class AfterReturingExample{
    @AfterReturning{pointcut="com.xyz.myapp.SystemArchitecture.dataAccessOperation()",
    returing="retVal")
    public void doAccessCheck(Object retVal){
        //...
    }
}
```

在returing属性使用的名字一定要与通知方法的参数名相对应。当一个方法执行并返回，返回值将会被传递到通知方法对应的参数上。returing严格限制返回值的类型要与对应通知方法的参数类型相匹配，否则通知将不会被执行。（Object 在这种情况下，将会匹配任何返回值）

请注意当使用after-returing通知时不可能返回完全不同的引用

## 抛出异常通知\(After throwing advice\)

当一个匹配的方法执行并通过抛出一个异常退出，此通知会执行。其通过使用@AfterThrowing注解声明

```java
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.AfterThrowing;

@Aspect
public class AfterThrowingExample{
    @AfterThrowing("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")
    public void doRecoveryActions(){
        //..
    }
}
```

在throwing终即用的名字一定要与通知方法中的名字相关联。当一个方法执行并通过抛出异常退出时，此异常将会被传递进通知方法相关的参数。throwing也对类型有限制，如果异常不匹配，则通知不会执行。

## After\(finally\) advice

After\(finally\)通知会运行无论匹配的方法怎样执行并退出。他通过@After注解声明。After通知必须作好对正常退出和异常返回情况的准备。其通常用来释放资源

```
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.After;

@Aspect
public class AfterFinallyExample{
    @After("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")
    public void doReleaseLock(){
    }
}
```

## Around advice

最后一种通知是环绕通知。Around advice runs "around" 匹配到的执行方法。It has the opportunity to do work both before and after the mthod executes, and to determine when, how and even if, the method actually gets to execute at all. Around advice is often used if you nned to share state before and after a method execution in a thread-safe manner\(starting and stopping a timer for example\).Always use the least powerful form of advice that meets your requirements\(i.e. don't use around advice if simple before advice would do\).



