[TOC]



# spring 手动提交事务

## 简介

　　在使用Spring声明式事务时，不需要手动的开启事务和关闭事务，但是对于一些场景则需要开发人员手动的提交事务，比如说一个操作中需要处理大量的数据库更改，可以将大量的数据库更改分批的提交，又比如一次事务中一类的操作的失败并不需要对其他类操作进行事务回滚，就可以将此类的事务先进行提交，这样就需要手动的获取Spring管理的Transaction来提交事务。

## 手动提交事务

```java
ClassPathXmlApplicationContext applicationContext = getClassPathXmlApplicationContext();

DataSourceTransactionManager transactionManager = (DataSourceTransactionManager) applicationContext
    .getBean("transactionManager");

DefaultTransactionDefinition transDefinition = new DefaultTransactionDefinition();
//配置事务属性为开启新事物
transDefinition
    .setPropagationBehavior(DefaultTransactionDefinition.PROPAGATION_REQUIRES_NEW);
//这里会获取数据库连接并传入配置事务属性
TransactionStatus transStatus = transactionManager.getTransaction(transDefinition);
try {

    // todo 处理具体需要事务的逻辑

    transactionManager.commit(transStatus);
} catch (Exception e) {
    transactionManager.rollback(transStatus);
}
```



## 使用示例

```java
@Resource(name = "mainTransactionManager")
private DataSourceTransactionManager mainTransactionManager;
    
 
//这里会获取数据库连接并传入配置事务属性,开启事务
TransactionStatus transStatus = mainTransactionManager.getTransaction(new DefaultTransactionDefinition());
 
        
try {

    // todo 处理具体需要事务的逻辑

    
    mainTransactionManager.commit(transStatus);
} catch (Throwable e) {
    mainTransactionManager.rollback(transStatus);
    //异常处理
    throw e;
} 
```

## 多个数据源事务示例

```java
@Resource(name = "mainTransactionManager")
private DataSourceTransactionManager mainTransactionManager;
@Resource(name = "docTransactionManager")
private DataSourceTransactionManager docTransactionManager;



TransactionStatus transStatusByMain = mainTransactionManager.getTransaction(new DefaultTransactionDefinition());
TransactionStatus transStatusByDoc = docTransactionManager.getTransaction(new DefaultTransactionDefinition());
try {


    // todo 处理具体需要事务的逻辑
    billService.insert(bill);
    billDetailService.batchInsert(billDetailList);

    billDetailExtendShippingService.batchInsert(billDetailExtendShippingList);


    docTransactionManager.commit(transStatusByDoc);
    mainTransactionManager.commit(transStatusByMain);
} catch (Throwable throwable) {
    docTransactionManager.rollback(transStatusByDoc);
    mainTransactionManager.rollback(transStatusByMain);
    //异常处理
    throw throwable;
}
```

LIFO/stack behavior的方式进行的，所以在多个事务进行提交时必须按照上述规则进行，否则就会报异常。java.lang.IllegalStateException: Cannot deactivate transaction synchronization - not active



事务的提交和回滚的顺序根据事务开启的顺序进行后进先出提交和回滚





http://www.cnblogs.com/banning/p/6346669.html