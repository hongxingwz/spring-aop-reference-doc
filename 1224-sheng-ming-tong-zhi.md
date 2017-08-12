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

```java
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

环绕通知使用@Around注解声明，环绕方法的第一个参数一定要是**ProceedingJoinPoint**类型。在环绕体内，调用**ProceedingJoinPoint**类的**proceed\(\)方法**将会使下面的方法执行。proceed方法调用时也可以传入**Object\[\] **- 数组内的值将会被用来作为参数传递给执行的方法当该方法被继续执行时

> 当用Object\[\]继续调用方法时的行为与AspectJ编译的环绕通知的行为不一点不样。用传统的AspectJ 语言写的环绕通知，传递给继续处理的方法的参数的数量一定要匹配  ......

```java
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.ProceedingjoinPoint;

@Aspect
public class AroundExample{

    @Around("com.xyz.myapp.SystemArchitecture.businessService()")
    public Object doBasicProfiling(ProceedingJoinPoint pjp) throws Throwable{
        Object retVal = pjp.proceed();

        return retVal;
    }
}
```

环绕通知返回的值将被返回给拦截的方法既而调用者可以看到。一个简单的缓存切面像例子可以从缓存中返回值如果其有一个的话，如果其没有缓存值的话调用proceed\(\)就可以了，注意此proceed可以调用一次，许多次，或者跟本不调用在环绕的体内，这些都是相当合法的。

## 通知参数

Spring提供完全的类型通知 - 意味着你声明你需要的参数在通知签名中（像上面我们看到的returing 和 throwing例子）而不是一直用与Object\[\]一起工作。我们稍后将会看到怎样使参数和其他上下文值在通知体内可用。首先让我们看一看怎样写一个通用的通知可以找出通知方法目前正在通知的方法。

## 访问当前的连接点

任何通知方法都可以声明其第一个参数，一个org.aspectj.lang.JoinPoint类型的参数（请注意环绕通知的需要声明第一个参数的类型为ProceedingJoinPoint,其是JoinPoint的一个子类。JoinPoint接口提供了许多有用的方法例如**getArgs\(\)**\(返回方法的参数），**getThis\(\)**\(返回代理对象\)，**getTarget\(\)**\(返回目标对象\)，**getSignature\(\)**\(返回一个将被通知的方法的描述\) 和 **toString\(\)**\(打印将被通知方法有用的描述\)。请查阅javadocs来看来全部的细节。

## 传递参数给通知

我们已经看到怎么绑定返回值或异常值\(使用after returing 和after throwing通知\)。使参数值对于通知体可用，你可以使用绑定args的绑定形式。如果一个参数名在args的表达式中使用，然后相关联的参数将被传递当通知被调用时。一个例子将会使你清楚。假设你想通知dao操作\(以Account作为其第一个参数\)的执行，然后你需要访问account在通知体内。你可以像下面这样写：

```java
@Before("com.xyz.myapp.SystemArchitecture.dataAccessOperation(..) && args(account, ..)")
public void validateAccount(Account account){
    // ...
}
```

**args\(account, ..\)**的表达式部分服务于两个目的：首先，他限制了匹配至少肯有一个参数的执行的方法，并且传递进的参数是**Account**的实例；第二：他使真实现的Account对象作为account参数在通知体内可用。

另一种方式是声明一个切点提供Account对象值当匹配连接点，然后从通知中引用此命名的切点，这将看起来像下面这样：

```java
@Pointcut("com.xyz.myapp.SystemArchitecture.dataAccessOperation() && args(account,..)")
private void accountDataAccessOperation(Account account){}

@Before("accountDataAccessOperation(account)
public void validateAccount(Account account){
}
```

感兴趣的读者可以读AspectJ编程指导获取得多的细节

代理对象\(**this**\), 目标对象\(**target**\) ,和注解\(@within, @target, @annotation, @args\)都可以类似的方式绑定。下面的例子展示了怎样匹配到一个被标注了@Auditable注解的执行方法，并提取value值。

首先定义一个@Auditable注解：

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Auditable{
    String value();
}
```

然后通知配置标注了@Auditable方法的执行方法

```java
@Before("com.xyz.lib.Pointcuts.anyPublicMethod() && @annotation(auditable)")
public void audit(Auditable auditable){
    String name = auditable.value();
    // ....
}
```

## 通知参数和泛型

Spring AOP 可以处理类定义和方法参数中的泛型，假设你有一个像下面的泛型类：

```java
public interface Sample<T>{
    void sampleGenericMethod(T param);

    void sampleGenericCollectionMethod(Collection<T> param);
}
```

你可以限制拦截方法类型包含必须的参数类型通过简单的输入通知参数--你想要拦截的方法：

```java
@Before("execution(* ..Sample+.sampleGenericMethod(*)) && args(param)")
public void beforeSampleMethod(MyType param){
}
```

此通知向我们上面讨论的一样工作。然而，其不与泛型集合一起工作，因此你不能定义像下面这样的切点：

```
@Before("execution(* ..Sample+.sampleGenericCollectionMethod(*)) && args(param)")
public void beforeSampleMethod(Collection<MyType> param){

}
```

为了使其工作我们可能将会拦截所有类型的集合，这是不合理的我们也不能决定怎么处理null值。为了达到此目的你需要类型参数Collection&lt;?&gt;并且手动的检查元素的类型

## 决定参数的名字

绑定在调用通知的参数依赖于使用在切点表达式声明的参数名。参数名在经过Java返射之后是不可用的， 因此Spring AOP使用下面的策略来决定参数的名字：

* 如果参数名已经被用户显式的指定，然后就使用指定的参数：所有的通知和切点注解都有一个可选的“argNames”属性 - 可以使用指定被标注方法的参数名 - 这些参数在运行时也是可用的。 例如：

```java
@Before(value="com.xyz.lib.Pointcuts.anyPublicMethod() && target(bean) && @annotation(auditable)",
        argNames="bean, auditable")
public void audit(Object bean, Auditable auditable){
        AuditCode code = auditable.value();
        // ...use code and bean
}
```

如果第一个参数是**JoinPoinit**, **PreceedingJoinPoinit**, 或者是 **JoinPoint.StaticPart**类型，你可以离开“argNames"属性指定的参数名。例如，如更改后续的通知来接收连接点对象，”argNames"属性不需要包括它：

```java
@Before(value="com.xyz.lib.Pointcuts.anyPublicMethod() && target(bean) && @annotation(auditable)",
        argNames="bean, auditable")
public void audit(JoinPoint jp, Object bean, Auditable auditable){
        AuditCode code = auditable.value();
        // ... use code, bean, and jp
}
```

对于指定的第一个参数类型是JoinPoint, ProceedingJoinPoint, 和JoinPoint.StaticPart类型会被特别转换为通知而不需要收集另外其他的连接点上下文。在这种情况下，你可以删除“argNames"属性。例如，下面的通知不需要声明”argNames"属性：

```java
@Before("com.xyz.lib.Pointcuts.anyPublicMethod()")
public void audit(JoinPoint jp){
    // ... use jp
}
```

* 使用**“argNames”**有点不美观，因此如果**“argNames”**属性没有被指定，Spring AOP会寻找class的debug信息然后从本地变量表决定参数的名字。此信息会出现只要类在编译时具有debug信息（“-g:vars" at a minimum）。使用此参数编译的后续影响是：\(1\)你的代码会容易理解（reverse engineer\), \(2\)类文件会稍微变大一点\(通常无关紧要\)，\(3\)移除没有使用的变量的优化将不会适用于你的编译，换句话说，你使用经参数时不会遇到任何的不同。

> 如果@AspectJ切面被AspectJ compile\(ajc\)编译及使没有debug信息，也不用添加argNames属性，编译器会保留需要的信息

* 如果代码被编译后没有需要的debug信息，Spring AOP将尝试推断绑定变量到参数（例如，如果仅有一个参数在切点表达式，通知方法也只有一个参数，那么对应的信息很明显！）。如果指定的信息区分不出绑定的变量，然后**AmbiguousBindingException**异常将会抛出。
* 如果上面的策略都失败了IllegalArgumentException将会抛出

## 后续处理时带有参数

我们在早前掉到过，我们将介绍怎么写一个后续处理的调用\(with arguments\)在Spring AOP 和 AspectJ中都工作。解决方法是仅确保通知签名按顺序绑定方法参数。例如：

```java
@Around(execution(List<Account> find*(..)) && " +
    "com.xyz.myapp.SystemArchitecture.inDataAccessLayer() && " +
    "args(accountHolderNamePattern)")
public Object preProcessQueryPattern(ProceedingJoinPoint pjp, String accountHolderNamePattern) throws Throwable{
    String newPattern = preProcess(accountHolderNamePattern);
    
    return pjp.proceed(new Object[]{newPattern});
}
```

在多数情况下你将可以保留任何东西（像上面的例子）

