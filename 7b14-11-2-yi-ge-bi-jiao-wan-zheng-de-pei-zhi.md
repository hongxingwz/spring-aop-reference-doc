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

**com2.aspect.NotVeryUsefulAspect**

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



