[TOC]



# javascript encodeURI() 函数

[JavaScript 全局对象](http://www.w3school.com.cn/jsref/jsref_obj_global.asp)

## 定义和用法

encodeURI() 函数可把字符串作为 URI 进行编码。

### 语法

```
encodeURI(URIstring)
```

| 参数      | 描述                                            |
| --------- | ----------------------------------------------- |
| URIstring | 必需。一个字符串，含有 URI 或其他要编码的文本。 |

### 返回值

URIstring 的副本，其中的某些字符将被十六进制的转义序列进行替换。

### 说明

该方法不会对 ASCII 字母和数字进行编码，也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。

该方法的目的是对 URI 进行完整的编码，因此对以下在 URI 中具有特殊含义的 ASCII 标点符号，encodeURI() 函数是不会进行转义的：;/?:@&=+$,#

## 提示和注释

提示：如果 URI 组件中含有分隔符，比如 ? 和 #，则应当使用 encodeURIComponent() 方法分别对各组件进行编码。

## 实例

在本例中，我们将使用 encodeURI() 对 URI 进行编码：

```js
<script type="text/javascript">

document.write(encodeURI("http://www.w3school.com.cn")+ "<br />")
document.write(encodeURI("http://www.w3school.com.cn/My first/"))
document.write(encodeURI(",/?:@&=+$#"))

</script>
```

输出：

```js
http://www.w3school.com.cn
http://www.w3school.com.cn/My%20first/
,/?:@&=+$#
```

[亲自试一试](http://www.w3school.com.cn/tiy/t.asp?f=jseg_encodeURI)

## TIY

- [encodeURI()](http://www.w3school.com.cn/tiy/t.asp?f=jseg_encodeURI)

  如何使用 encodeURI() 来编码不同的 URI。

[JavaScript 全局对象](http://www.w3school.com.cn/jsref/jsref_obj_global.asp)

##### [JavaScript 参考手册](http://www.w3school.com.cn/jsref/index.asp)

##### [JavaScript 实例](http://www.w3school.com.cn/example/jseg_examples.asp)

##### [JavaScript 测验](http://www.w3school.com.cn/js/js_quiz.asp)