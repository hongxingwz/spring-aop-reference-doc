# Understanding AOP代理

Spring AOP是基本代理的。掌握最后一条语句真正代表什么在你编写你自己的切面或使用任何由Spring Framework提供的Sring AOP基本的代理是非常重要的

考虑第一个场景：你有一个普通的香草冰淇淋，未被代理，它没有什么特殊的，直接对象引用，通过下面的代码片段所示。

```
public class SimplePojo implements Pojo{
    public void foo(){
        this.bar();
    }
    
    public void bar(){
        //some logic...
    }
}
```

如果你通过引用对象调用一个方法，方法调用会直接从引用对象调用，像下面看到的那样。

![](/assets/import-08-12-08-53.png)

```
public class Main{
    public static void main(String[] args){
        Pojo pojo = new SimplePojo();
        
        pojo.foo();
    }
}
```

当引用端代码具有一个代理时事情变得不同。考虑到下面的类图和代码片断

![](/assets/import-08-12-08-57.png)

```
public class Main{
    public static void main(String[] args){
        ProxyFactory factory = new ProxyFactory(new SimplePojo());
        factory.addInterface(Pojo.class);
        factory.addAdvice(new RetryAdvice());
        
        Pojo pojo = (Pojo)factory.getProxy();
        pojo.foo();
    }
}
```

理解此代码的主要点在于在Main类的main\(..\)方法引用了一个代理的客户端代码。这竟味着对象引用的方法调用将会调用代理，像这样的代理会委托所有与这个特殊方法有关的的拦截(通知)。然而，只要调用到达目标对象，在此例中是SimplePojo引用，任何自身调用的方法，例如this.bar() 或者this.foo()，会直接调用自己的引用，而不是代理对象的方法。这重要的暗示。这意味着自己的调用不会造成与之相关的通知方法执行。

好的，那我们该怎样作呢？最好的实现是重构你的代码让这样的自身调用不要发生。这会导致一些工作，但这是最好的非入侵式的实现。下一个实现的方法是相当糟糕的，使用Spring AOP


```java
public class SimplePojo implements Pojo{
    public void foo(){
        ((Pojo) AopContext.currentProxy()).bar();
    }
    
    public void bar(){
        // some logic...
    }
}

```
这完全把你的代码与Spring AOP耦合起来，并使此类自身意识到它正在使用AOP上下文的事实。这也需要额外的配置当代理类要创建时：


```java
public class Main{
    public static void main(String[] args){
        ProxyFactory factory = new ProxyFactory(new SimplePojo());
        factory.addInterface(Pojo.class);
        factory.addAdvice(new RetryAdvice());
        factory.setExposeProxy(true);
        
        Pojo pojo = (Pojo) factory.getProxy();
        
        pojo.foo();
    }
}
```
最后，最值得注意的是AspectJ不会有自身调用的问题因为它不是基于代理的AOP框架。





