# javascript js中el表达式的使用和非空判断方法

 注意，这里想说的不是jsp里面嵌套的el表达式的使用，而是在js中使用。

**场景：**

页面跳转后，使用spring mvc向前端页面传过来一个json对象，要在js中获取后，做处理。

**返回的json对象：**

```
{"nodes":[{"contactmobile":"15922208502","orderno":"XNH31918062989476864"},{"id":"12198","group":"11","content":"把考虑考虑","modelname":"Company"}],"links":[{"target":"12198","id":"15016","relationType":"公司","source":"12194"}]} 
```

```
var graph; 
var flag = "${empty jsonData}"; 
if(flag!="true"){ 
  graph = eval('(' + '${jsonData}' + ')'); 
}; 
```

**这里有一个注意点：**

在js中使用el表达式，一定要使用引号括起来。如果返回的json中包括双引号，那么就使用单引号包围el表达式，否则，使用双引号。

另外，注意使用eval函数将json串转为转为对象的写法。

以上这篇js中el表达式的使用和非空判断方法就是小编分享给大家的全部内容了，希望能给大家一个参考，也希望大家多多支持编程小技巧。





https://www.oudahe.com/p/51193/