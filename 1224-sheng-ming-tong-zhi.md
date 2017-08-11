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

```
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

