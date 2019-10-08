

[TOC]

# TimeUnit 使用

## 说明

TimeUnit是java.util.concurrent包下面的一个类，表示给定单元粒度的时间段

主要作用

- 时间颗粒度转换
- 延时

 

常用的颗粒度

```
TimeUnit.DAYS          //天
TimeUnit.HOURS         //小时
TimeUnit.MINUTES       //分钟
TimeUnit.SECONDS       //秒
TimeUnit.MILLISECONDS  //毫秒
```

　　

## 1、时间颗粒度转换 

```
	public long toMillis(long d)    //转化成毫秒
    public long toSeconds(long d)  //转化成秒
    public long toMinutes(long d)  //转化成分钟
    public long toHours(long d)    //转化成小时
    public long toDays(long d)     //转化天
```

　　例子

```
package com.app;
 
import java.util.concurrent.TimeUnit;
 
public class Test {
 
    public static void main(String[] args) {
        //1天有24个小时    1代表1天：将1天转化为小时
        System.out.println( TimeUnit.DAYS.toHours( 1 ) );
         
        //结果： 24
         
 
        //1小时有3600秒
        System.out.println( TimeUnit.HOURS.toSeconds( 1 ));
         
        //结果3600
         
         
        //把3天转化成小时
        System.out.println( TimeUnit.HOURS.convert( 3 , TimeUnit.DAYS ) );
        //结果是：72
 
    }
}
```

　　

##  2、延时

-  一般的写法

```
package com.app;
 
public class Test2 {
 
    public static void main(String[] args) {
 
        new Thread( new Runnable() {
 
            @Override
            public void run() {
                try {
                    Thread.sleep( 5 * 1000 );
                    System.out.println( "延时完成了");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();  ;
    }
     
}
```

　　

- TimeUnit 写法

```
package com.app;
 
import java.util.concurrent.TimeUnit;
 
public class Test2 {
 
    public static void main(String[] args) {
 
        new Thread( new Runnable() {
 
            @Override
            public void run() {
                try {
                    TimeUnit.SECONDS.sleep( 5 );
                    System.out.println( "延时5秒，完成了");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();  ;
    }
     
}
```

　　





<https://www.cnblogs.com/zhaoyanjun/p/5486726.html>

