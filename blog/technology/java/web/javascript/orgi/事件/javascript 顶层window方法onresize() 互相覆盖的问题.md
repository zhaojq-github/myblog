# javascript - 顶层window方法onresize() 互相覆盖的问题

 

### 前言

​    在单网页中使用同时使用window.onresize，发现后面onresize方法会把前面的 window.onresize 覆盖掉，有什么办法使所有组件的 window.onresize 都能执行互不影响？

### 原因

DOM 顶层事件绑定多次事件会被覆盖，我们可以用`addEventListener()`方法添加事件监听，这样就可以避免多次使用后面覆盖前面的情况

### 实例

```html
<script>
    window.onresize = function () {//不执行
        console.log(1111)
    }
    window.onresize = function () {//执行
        console.log(2222)
    }
    window.addEventListener('resize',function () {//执行
        console.log(3333)
    })
</script> 
```

### window.addEventListener()

声明：<https://blog.csdn.net/q6678188/article/details/51781478>

##### 原型

```
public override function addEventListener(type:String, listener:Function, 
useCapture:Boolean = false, priority:int = 0, useWeakReference:Boolean = false):void
```

##### 作用

​    侦听事件并处理相应的函数。

##### 参数

​    1、type:String 　　事件的类型（去掉前面的on）。

​    2、listener:Function 　　侦听到事件后处理事件的函数。 此函数必须接受 Event 对象作为其唯一的参数，并且不能返回任何结果，如以下示例所示： 访问修饰符 function 函数名(evt:Event):void

​    3、useCapture:Boolean (default = false) 　　这里牵扯到“事件流”的概念。侦听器在侦听时有三个阶段：捕获阶段、目标阶段和冒泡阶段。顺序 为：捕获阶段（根节点到子节点检查是否调用了监听函数）→目标阶段（目标本身）→冒泡阶段（目标本身到根节点）。此处的参数确定侦听器是运行于捕获阶段、 目标阶段还是冒泡阶段。 如果将 useCapture 设置为 true，则侦听器只在捕获阶段处理事件，而不在目标或冒泡阶段处理事件。 如果useCapture 为 false，则侦听器只在目标或冒泡阶段处理事件。 要在所有三个阶段都侦听事件，请调用两次 addEventListener，一次将 useCapture 设置为 true，第二次再将useCapture 设置为 false。

​    4、priority:int (default = 0) 　　事件侦听器的优先级。 优先级由一个带符号的 32 位整数指定。 数字越大，优先级越高。 优先级为 n 的所有侦听器会在优先级为 n -1 的侦听器之前得到处理。 如果两个或更多个侦听器共享相同的优先级，则按照它们的添加顺序进行处理。 默认优先级为 0。

​    5、useWeakReference:Boolean (default = false) 　　确定对侦听器的引用是强引用，还是弱引用。 强引用（默认值）可防止您的侦听器被当作垃圾回收。 弱引用则没有此作用。





<https://blog.csdn.net/idomyway/article/details/88729937>