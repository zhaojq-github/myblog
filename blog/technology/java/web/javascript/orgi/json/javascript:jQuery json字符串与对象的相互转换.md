[TOC]



# javascript/jQuery json字符串与对象的相互转换

 

## 前言

​       我们知道，如果在**java中json**对象与java对象的转换很简单就能实现，利用**阿里巴巴的fastjson**或者**jackjson**轻松实现，或者利用**json包**中的net包中的工具也可以实现，这里就不再讲，我们讲讲在**js中对象与json字符串之间怎么相互转换,**有时候不仅处理业务上需要这样的转化，而且你灵活运用的话，调试前台的时候很容易，比如一个页面无法跟踪这个页面的js过程,alert又是对象，输出[object]等,就是不知道自己想知道的那个js对象里存的啥数据,这时候利用下边我讲述的2种方法转成json字符串再alert就知道这个对象的各个属性和值了（不过console.log()和直接写入debugger;能起到相同调试的作用）别小看这一点，当你在特定的项目里山穷水尽无法调试对象的时候，这个可是“救命”的法儿，在这就把我的经验分享给大家了。好了，让我们看看以下两种转换方法吧。

## 方法一：json.js实现json与对象相互转换

​        为了方便地处理JSON数据，JSON提供了json.js包,[json.js免费下载地址](http://download.csdn.net/download/chenleixing/8613997)。

### json字符串转换为对象

```js
//这个后台jsp这么传 model.addAttribute("imageUrlListJson", JSON.toJSONString(noteImages, SerializerFeature.UseSingleQuotes));  前台获取用如下 eval
//json转换为对象  
var obj = eval('(' + str + ')');  
或者   
var obj = str.parseJSON(); //json字符串转换为对象  
或者  
var obj = JSON.parse(str); //json字符串转换为对象 
```

**注意：**如果obj本来就是一个JSON对象，那么运用 eval（）函数转换后（哪怕是多次转换）还是JSON对象，但是运用 parseJSON（）函数处理后会有疑问（抛出语法异常）。

### **对象转成json字符串**

```
/对象转成json  
可以运用 toJSONString()或者JSON.stringify()将JSON对象转化为JSON字符串。  
var str=obj.toJSONString(); //将对象转成json  
或者  
var last=JSON.stringify(obj); //将对象转成json  
```

**以上**，除了eval()函数是js自带的之外，其他的多个要领都来自json.js包。新版本的 JSON 修改了 API，将 JSON.stringify() 和 JSON.parse() 两个函数都注入到了 Javascript 的内建对象里面，前者变成了 Object.toJSONString()，而后者变成了 String.parseJSON()。如果提示找不到toJSONString()和parseJSON()，则说明您的json包版本太低。

 

## 方法二：jQuery.json实现json与对象相互转换

​       jQuery.json 是 jQuery 的一个插件，可轻松实现对象和 JSON 字符串之间的转换。可序列化 JavaScript 对象、数值、字符串和数组到 JSON 字符串，同时可转换 JSON 字符串到 JavaScript，[免费下载地址](http://download.csdn.net/download/chenleixing/8614007)。

### 对象转成json字符串

```
//对象转成json  
var thing = {plugin: 'jquery-json', version: 2.3};//js对象  
var str = $.toJSON(thing);//转换为json,结果: '{"plugin":"jquery-json","version":2.3}'  
```

### **json字符串转成对象**

```
//json转成对象  
var  obj= $.evalJSON(str);  
var name=obj.plugin;//js对象.属性,结果: "jquery-json"  
var version =obj.version;//结果: 2.3  
```

以上即为javasript中对象与json串之间转换的总结。

 





**转载请注明—作者：Java我人生（陈磊兴）   原文出处：http://blog.csdn.net/chenleixing/article/details/45331003**

 