# 声明一个切面

激活了@Aspect的支持，任何一个在你应用上下文中定义的bean（标注@Aspect注解的类）将会自动被Spring发现，且用来配置Spring AOP,下面的例子展示了最小化的定义，定义了一个不是非常有用的切面：

一个在应用上下文中定义的普通bean， 指向一个bean的class标注了@Aspect注解：

```
<bean id="myAspect" class="org.xyz.NotVeryUsefulAspect">
    <!-- configure properties of aspect here as normal -->
</bean>
```

或者定义NotVeryUsefulAspect类时，标注org.aspectj.lang.annotation.Aspect注解

```
package org.xyz;
import org.aspectj.lang.annotion.Aspect;

@Aspect
public class NotVeryUsefulAspect{
}
```

切面（标注了@Aspect注解的类\)可以有自己的域和方法就像任何别的类一样。切面也可以包涵切点\(pointcut\), 环绕\(advice\)，和introduction\(inter-type\)声明

> 你可以注册切面类像其他普通的beans一样在XML，或通过classpath扫描自动发现 - 就像其他的被Spring管理的bean一样。然而，@Aspect注解对自动扫描无效；为了达到此意图，你需要添加额外的@Component注解（或者自定义的注解）

> 在Spring AOP中，切面被另外的切面环绕是不可以的。一个类上的@Aspect注解标记此类是一个切面，并把其从auto-proxying（自动代理）中排除



