[TOC]



# javascript splice() 方法

 

## 定义和用法

splice() 方法向/从数组中添加/删除项目，然后返回被删除的项目。

注释：该方法会改变原始数组。

### 语法

```
arrayObject.splice(index,howmany,item1,.....,itemX)
```

| 参数              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| index             | 必需。整数，规定添加/删除项目的位置，使用负数可从数组结尾处规定位置。 |
| howmany           | 必需。要删除的项目数量。如果设置为 0，则不会删除项目。       |
| item1, ..., itemX | 可选。向数组添加的新项目。                                   |

### 返回值

| 类型  | 描述                                 |
| ----- | ------------------------------------ |
| Array | 包含被删除项目的新数组，如果有的话。 |

### 说明

splice() 方法可删除从 index 处开始的零个或多个元素，并且用参数列表中声明的一个或多个值来替换那些被删除的元素。

如果从 arrayObject 中删除了元素，则返回的是含有被删除的元素的数组。

### 技术细节

| JavaScript 版本： | 1.2  |
| ----------------- | ---- |
|                   |      |

## 浏览器支持

所有主流浏览器都支持 splice() 方法。

## 提示和注释

注释：请注意，splice() 方法与 slice() 方法的作用是不同的，splice() 方法会直接对数组进行修改。

## 实例

### 例子 1

在本例中，我们将创建一个新数组，并向其添加一个元素：

```
<script type="text/javascript">

var arr = new Array(6)
arr[0] = "George"
arr[1] = "John"
arr[2] = "Thomas"
arr[3] = "James"
arr[4] = "Adrew"
arr[5] = "Martin"

document.write(arr + "<br />")
arr.splice(2,0,"William")
document.write(arr + "<br />")

</script>
```

输出：

```
George,John,Thomas,James,Adrew,Martin
George,John,William,Thomas,James,Adrew,Martin
```

### 例子 2

在本例中我们将删除位于 index 2 的元素，并添加一个新元素来替代被删除的元素：

```
<script type="text/javascript">

var arr = new Array(6)
arr[0] = "George"
arr[1] = "John"
arr[2] = "Thomas"
arr[3] = "James"
arr[4] = "Adrew"
arr[5] = "Martin"

document.write(arr + "<br />")
arr.splice(2,1,"William")
document.write(arr)

</script>
```

输出：

```
George,John,Thomas,James,Adrew,Martin
George,John,William,James,Adrew,Martin
```

### 例子 3

在本例中我们将删除从 index 2 ("Thomas") 开始的三个元素，并添加一个新元素 ("William") 来替代被删除的元素：

```
<script type="text/javascript">

var arr = new Array(6)
arr[0] = "George"
arr[1] = "John"
arr[2] = "Thomas"
arr[3] = "James"
arr[4] = "Adrew"
arr[5] = "Martin"

document.write(arr + "<br />")
arr.splice(2,3,"William")
document.write(arr)

</script>
```

输出：

```
George,John,Thomas,James,Adrew,Martin
George,John,William,Martin
```

## TIY

- [splice()](http://www.w3school.com.cn/tiy/t.asp?f=jseg_splice)

  如何使用 splice() 来更改数组。

[JavaScript Array 对象](http://www.w3school.com.cn/jsref/jsref_obj_array.asp)





http://www.w3school.com.cn/js/jsref_splice.asp