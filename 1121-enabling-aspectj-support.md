# 11.2.1 Enabling @AspectJ Support

要在spring 配置中使用@AspectJ 切面你需要打开Spring对Spring-aop基于@AspectJ 切面配置的支持，自动代理beans基于这些beans是否是被切面环绕。自动代理的意思是如果Spring确定一个bean被一个或多个切换环绕，其将会自动为这个bean生成一个代理来拦截方法的调用并确保此环绕\(advice\)按需要执行

基于@Aspect的支持可以通过XML或Java 配置激活。无论通过哪种方式你都需要保证AspectJ的**aspectjweaver.jar**库在你的应用程序的classpath中。此库也可以放在'lib'的文件夹下，或作为Maven的依赖引入



## 通过Java配置的方式激活@AspectJ

```java
@Configuration
@EnableAspectJAutoProxy
public class AppConfig{
}
```

## 通过XML配置的方式激活@AspectJ

```xml
<aop:aspectj-autoproxy/>
```

此配置假设你使用的约束支持

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- bean definitions here -->

</beans>
```

并且引入了aop命名空间

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop" xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd"> <!-- bean definitions here -->

</beans>
```



