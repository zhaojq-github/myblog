[TOC]



# javascript array 数组删除指定元素

2018年01月26日 15:12:12

摘要：我记得js提供的方法中是没有直接删除指定元素的方法的，只有通过index下表来删除。

> 但是我们可以根据其已提供的可用方法，写一个remove(str)的方法。



## 一、写第一个js方法

```
Array.prototype.indexOf = function(val) {
	for (var i = 0; i < this.length; i++) {
		if (this[i] == val) return i;
	}
	return -1;
};
```

## 二、由上一个方法，实现remove(str)方法

```
Array.prototype.remove = function(val) {
	var index = this.indexOf(val);
	if (index > -1) {
		this.splice(index, 1);
	}
};
```

## 三、直接调用即可

```
var arrays = ["a","b","c","d"];
arrays.remove("a");
```

## 四、多嘴说一句

js提供的数组删除元素的方法是splice()，这个方法中可以传入三个参数，以实现删除、替换元素的功能。

与之对应的，js提供的为数据添加元素的方法是push()。



详细博文请看参考文章：[js操作数组中元素的方法](http://caibaojian.com/js-splice-element.html)

 



<https://blog.csdn.net/qq_36769100/article/details/79172472>