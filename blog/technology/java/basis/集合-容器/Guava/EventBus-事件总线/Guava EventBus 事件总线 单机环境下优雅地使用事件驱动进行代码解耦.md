---
name: guava-eventbus
title: 单机环境下优雅地使用事件驱动进行代码解耦
date: 2019-07-01 22:17:44
tags: 
categories: 
---
[TOC]

# 单机环境下优雅地使用事件驱动进行代码解耦

虽然现在的各种应用都是集群部署，单机部署的应用越来越少了，但是不可否认的是，市场上还是存在许多单机应用的。本文要介绍的是 Guava 中的 EventBus 的使用。

EventBus 处理的事情类似观察者模式，基于事件驱动，观察者们监听自己感兴趣的特定事件，进行相应的处理。

本文想要介绍的内容是，在 Spring 环境中优雅地使用 Guava 包中的 EventBus，对我们的代码进行一定程度的解耦。当然，本文不介绍 EventBus 的原理，我所说的优雅也只是我觉得优雅，也许读者有更漂亮的代码，欢迎在评论区留言。

<!-- toc -->

## Step 0：添加 Guava 依赖

```xml
<dependency>
   <groupId>com.google.guava</groupId>
   <artifactId>guava</artifactId>
   <version>22.0</version>
</dependency>
```

作为 java 程序员，如果你还没有使用过 Google Guava，请从现在开始将它加到你的每一个项目中。

## Step 1：定义一个注解用于标记 listener

```java
/**
 * 用于标记 listener
 */
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface EventBusListener {
}
```

## Step 2：定义注册中心

```java
package com.javadoop.eventbus;

import java.util.List;
import java.util.concurrent.Executors;
import javax.annotation.PostConstruct;
import org.springframework.stereotype.Component;
import com.google.common.eventbus.AsyncEventBus;
import com.google.common.eventbus.EventBus;
import com.hongjiev.javadoop.util.SpringContextUtils;

@Component
public class EventBusCenter {

    // 管理同步事件
    private EventBus syncEventBus = new EventBus();

    // 管理异步事件
    private AsyncEventBus asyncEventBus = new AsyncEventBus(Executors.newCachedThreadPool());

    public void postSync(Object event) {
        syncEventBus.post(event);
    }

    public void postAsync(Object event) {
        asyncEventBus.post(event);
    }

    @PostConstruct
    public void init() {

        // 获取所有带有 @EventBusListener 的 bean，将他们注册为监听者
        List<Object> listeners = SpringContextUtils.getBeansWithAnnotation(EventBusListener.class);
        for (Object listener : listeners) {
            asyncEventBus.register(listener);
            syncEventBus.register(listener);
        }
    }
}
```

## Step 3：定义各种事件

举个例子，我们定义一个订单创建事件：

```java
package com.javadoop.eventbus.event;

public class OrderCreatedEvent {
    private long orderId;
    private long userId;
    public OrderCreatedEvent(long orderId, long userId) {
        this.setOrderId(orderId);
        this.setUserId(userId);
    }
    // getter、setter
}
```

## Step 4：定义事件监听器

首先，类上面需要加我们之前定义的注解：@EventBusListener，然后监听方法需要加注解 @Subscribe，方法参数为具体事件。

```java
package com.javadoop.eventbus.listener;

import org.springframework.stereotype.Component;
import com.google.common.eventbus.Subscribe;
import com.javadoop.eventbus.EventBusListener;
import com.javadoop.eventbus.event.OrderCreatedEvent;

@Component
@EventBusListener
public class OrderChangeListener {

    @Subscribe
    public void created(OrderCreatedEvent event) {
        long orderId = event.getOrderId();
        long userId = event.getUserId();
        // 订单创建成功后的各种操作，如发短信、发邮件等等。
        // 注意，事件可以被订阅多次，也就是说可以有很多方法监听 OrderCreatedEvent 事件，
        // 所以没必要在一个方法中处理发短信、发邮件、更新库存等
    }

    @Subscribe
    public void change(OrderChangeEvent event) {
        // 处理订单变化后的修改
        // 如发送提醒、更新物流等
    }
}
```

## Step 5：发送事件

```java
@Service
public class OrderService {

    @Autowired
    private EventBusCenter eventBusCenter;

    public void createOrder() {
        // 处理创建订单
        // ...
        // 发送异步事件
        eventBusCenter.postAsync(new OrderCreatedEvent(1L, 1L));
    }
}
```

## 总结

EventBus 的好处在于，它将发生事件的代码和事件处理的代码进行了解耦。

比如系统中很多地方都会修改订单，用户可以自己修改、客服也可以修改、甚至可能是团购没成团系统进行的订单修改，所有这些触发订单修改的地方都要发短信、发邮件，假设以后还要增加其他操作，那么需要修改的地方就比较多。

而如果采用事件驱动的话，只要这些地方抛出事件就可以了，后续的维护是比较简单的。

而且，EventBus 支持同步事件和异步事件，可以满足我们不同场景下的需求。比如发短信，系统完全没必要等在那边，完全是可以异步做的。

## 附录：SpringContextUtils

上面的代码使用到了 SpringContextUtils，我想大部分的 Spring 应用都会写这么一个工具类来从 Spring 容器中获取 Bean，用于一些不方便采用注入的地方。

```java
@Component
public class SpringContextUtils implements BeanFactoryPostProcessor {

    private static ConfigurableListableBeanFactory beanFactory;

    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory configurableListableBeanFactory) throws BeansException {
        SpringContextUtils.beanFactory = configurableListableBeanFactory;
    }

    public static <T> T getBean(String name) throws BeansException {
        return (T) beanFactory.getBean(name);
    }

    public static <T> T getBean(Class<T> clz) throws BeansException {
        T result = beanFactory.getBean(clz);
        return result;
    }

    public static <T> List<T> getBeansOfType(Class<T> type) {
        return beanFactory.getBeansOfType(type).entrySet().stream().map(entry->entry.getValue()).collect(Collectors.toList());
    }

    // 上面的例子用到了这个
    public static List<Object> getBeansWithAnnotation(Class<? extends Annotation> annotationType) {
        Map<String, Object> beansWithAnnotation = beanFactory.getBeansWithAnnotation(annotationType);
      
        // java 8 的写法，将 map 的 value 收集起来到一个 list 中
        return beansWithAnnotation.entrySet().stream().map(entry->entry.getValue()).collect(Collectors.toList());
      
        // java 7
        List<Object> result = new ArrayList<>();
        for (Map.Entry<String, Object> entry : beansWithAnnotation.entrySet()) {
            result.add(entry.getValue());
        }
        return result;
    }
}
```

## 附录：同步事件的异常处理

**更新于 2019-01-17**

这次重新又用上了这个 eventbus，碰到一个新的问题：在使用同步事件的时候，怎样将事件处理过程中抛出来的异常抛回给客户端？

首先我们要明白，AsyncEventBus 是异步模式的，EventBus 是同步模式的，在使用同步模式的时候，线程 post 一个 event 以后，还是由当前线程来处理各个 Subscriber 中的操作的。

> 所以在调用 `void eventbus.post(event)` 这个方法后，线程会先去处理 Subscribers 中的操作，处理完了以后，post(event) 方法才会返回。

StackOverflow 上有很多人都碰到了这个问题，不过我没有找到合适的解决方案，就自己造了一个。

解决方法很简单，就是使用 ThreadLocal 来传递异常：

```java

ThreadLocal<ServiceException> threadLocal = new ThreadLocal();

/**
 * 管理同步事件
 */
private EventBus syncEventBus = new EventBus(new SubscriberExceptionHandler() {

    @Override
    public void handleException(Throwable exception, SubscriberExceptionContext context) {
        if (exception instanceof ServiceException) {
            threadLocal.set((ServiceException) exception);
        }
    }
});

public void postSync(Object event) {
    syncEventBus.post(event);
    ServiceException ex = threadLocal.get();
    if (ex != null) {
        // 记得 remove
        threadLocal.remove();
        throw ex;
    }
}
```

> ps: 在多个 Subscriber 的场景中，在一个 Subscriber 中抛出异常，不会阻止线程执行下一个 Subscriber 中的操作。在上面的代码中，如果有多个 Subscriber 抛出异常，就是 threadLocal 会被设置多次，最终得到的是最后一个 ex 的值。

（全文完）





<https://www.javadoop.com/post/guava-eventbus>