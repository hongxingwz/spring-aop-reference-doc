# 一个比较完整的配置

原因：2017-08-11 08:30 配置aop时遇到了坑，特此记录。

**pom.xml**

aspectjweaver.jar 和 aopalliance.jar是要引入的。aopalliance.jar定义了aop的规范，aspectjweaver.jar引入@Aspect支持的条件之一

```xml
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
    </dependency>

    <!--aop-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
    </dependency>

    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.8.10</version>
    </dependency>

    <dependency>
        <groupId>aopalliance</groupId>
        <artifactId>aopalliance</artifactId>
        <version>1.0</version>
    </dependency>
```

**com2.comp.MessageService 定义一个组件**

```
package com2.comp;

import org.springframework.stereotype.Component;

@Component
public class MessageService {

    public void transfer() {
        System.out.println("say transfer");
    }
}
```

**com2.aspect.NotVeryUsefulAspect 定义一个切面**

> 注：**@Aspect**注解只是简单的将class声明为一个切面，不具有@Component的功能。所以要**加上@Component**才能被Spring应用上下文扫描

```
package com2.aspect;

import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class NotVeryUsefulAspect {

    @Pointcut("execution(* transfer(..))")
    public void anyOldTransfer() {
        System.out.println("heihei");
    }

    @After("anyOldTransfer()")
    public void sayHelloAfter() {
        System.out.println("say Hello after");
    }

    @After(value = "anyOldTransfer()")
    public void sayHelloAfter2() {
        System.out.println("say Hello after2");
    }


    @Before("anyOldTransfer()")
    public void sayHelloBefore() {
        System.out.println("say Hello2 before");
    }
}
```

**com2.config 定义一个配置类**

> 注：@EnableAspectJAutoProxy这个注解是必须的，启动自动代理

```
package com2.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@Configuration
@ComponentScan(basePackages = {"com2"})
@EnableAspectJAutoProxy
public class AppConfig {
}

```

这样当在注入MessageService，并调用transfer\(\), 会进行拦截并执行相应的环绕方法

