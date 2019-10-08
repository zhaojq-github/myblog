[TOC]



# mysql 5.7的JSON类型

2016.12.29 00:16* 字数 433 阅读 1087评论 0喜欢 1

MySQL在5.7.8开始提供的对json的原生支持[[doc](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/mysql-nutshell.html)]，本文是对MySQL的json类型的用法的简单整理。

## JSON函数完整列表

MySQL官方列出json相关的函数，完整列表如下[[doc](https://link.jianshu.com/?t=https://dev.mysql.com/doc/refman/5.7/en/json-function-reference.html)]：

| 分类                                                         | 函数                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [创建json](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-creation-functions.html) | [json_array](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-creation-functions.html#function_json-array) | 创建json数组                                                 |
| -                                                            | [json_object](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-creation-functions.html#function_json-object) | 创建json对象                                                 |
| -                                                            | [json_quote](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-creation-functions.html#function_json-quote) | 将json转成json字符串类型                                     |
| [查询json](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html) | [json_contains](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#function_json-contains) | 判断是否包含某个json值                                       |
| -                                                            | [json_contains_path](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#function_json-contains-path) | 判断某个路径下是否包json值                                   |
| -                                                            | [json_extract](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#function_json-extract) | 提取json值                                                   |
| -                                                            | [column->path](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#operator_json-column-path) | json_extract的简洁写法，MySQL 5.7.9开始支持                  |
| -                                                            | [column->>path](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#operator_json-inline-path) | json_unquote(column -> path)的简洁写法                       |
| -                                                            | [json_keys](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#function_json-keys) | 提取json中的键值为json数组                                   |
| -                                                            | [json_search](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#function_json-search) | 按给定字符串关键字搜索json，返回匹配的路径                   |
| [修改json](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html) | [json_append](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-append) | 废弃，MySQL 5.7.9开始改名为json_array_append                 |
| -                                                            | [json_array_append](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-array-append) | 末尾添加数组元素，如果原有值是数值或json对象，则转成数组后，再添加元素 |
| -                                                            | [json_array_insert](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-array-insert) | 插入数组元素                                                 |
| -                                                            | [json_insert](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-insert) | 插入值（插入新值，但不替换已经存在的旧值）                   |
| -                                                            | [json_merge](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-merge) | 合并json数组或对象                                           |
| -                                                            | [json_remove](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-remove) | 删除json数据                                                 |
| -                                                            | [json_replace](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-replace) | 替换值（只替换已经存在的旧值）                               |
| -                                                            | [json_set](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-set) | 设置值（替换旧值，并插入不存在的新值）                       |
| -                                                            | [json_unquote](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-modification-functions.html#function_json-unquote) | 去除json字符串的引号，将值转成string类型                     |
| [返回json属性](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-attribute-functions.html) | [json_depth](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-attribute-functions.html#function_json-depth) | 返回json文档的最大深度                                       |
| -                                                            | [json_length](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-attribute-functions.html#function_json-length) | 返回json文档的长度                                           |
| -                                                            | [json_type](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-attribute-functions.html#function_json-type) | 返回json值得类型                                             |
| -                                                            | [json_valid](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json-attribute-functions.html#function_json-valid) | 判断是否为合法json文档                                       |

官方文档对全部函数都作了充分解释并提供一定的示例代码。下文挑选了**部分函数**，演示它们的使用方法。

## 插入和查询数据

```
mysql> CREATE TABLE employees (data JSON);
Query OK, 0 rows affected (0.17 sec)
 
mysql> INSERT INTO employees VALUES ('{"id": 1, "name": "Jane"}');
Query OK, 1 row affected (0.04 sec)
 
mysql> INSERT INTO employees VALUES ('{"id": 2, "name": "Joe"}');
Query OK, 1 row affected (0.00 sec)
 
mysql> SELECT * FROM employees WHERE data->'$.id'= 2;  -- json路径表达式
+--------------------------+
| data                     |
+--------------------------+
| {"id": 2, "name": "Joe"} |
+--------------------------+
1 row in set (0.00 sec)
 
mysql> SELECT * FROM employees WHERE json_extract(data,'$.id') = 2;
+--------------------------+
| data                     |
+--------------------------+
| {"id": 2, "name": "Joe"} |
+--------------------------+
1 row in set (0.00 sec)
 
mysql> SET @j = '["a", "b"]';
Query OK, 0 rows affected (0.00 sec)
 
mysql> SELECT @j -> '$[0]'; -- 语法错误
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '-> '$[0]'' at line 1
 
mysql> SELECT json_extract(@j, '$[0]');
+--------------------------+
| json_extract(@j, '$[0]') |
+--------------------------+
| "a"                      |
+--------------------------+
1 row in set (0.00 sec)
```

json路径表达式是json_extract的简洁写法，但存在以下限制[[ref](https://link.jianshu.com/?t=http://mysqlserverteam.com/inline-json-path-expressions-in-mysql-5-7/)]

![img](https://upload-images.jianshu.io/upload_images/2876271-8613b1ae3dd7171c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/616)

图片.png

即，1. 数据源必须是表字段，2. 路径表达式必须为字符串，3. SQL语句中最多只支持一个。
**json_contains示例：**

```
mysql> SET @j = '{"a": 1, "b": 2, "c": {"d": 4}}';
Query OK, 0 rows affected (0.00 sec)
 
mysql> SELECT json_contains(@j, '{"a": 1}');
+-------------------------------+
| json_contains(@j, '{"a": 1}') |
+-------------------------------+
|                            1  |
+-------------------------------+
1 row in set (0.00 sec)
```

更新数据
**json_array_append和json_array_insert示例：**

```
mysql> SET @j = '["a", "b"]';
Query OK, 0 rows affected (0.00 sec)
 
mysql> SELECT json_array_append(@j, '$', 'c');
+---------------------------------+
| json_array_append(@j, '$', 'c') |
+---------------------------------+
| ["a", "b", "c"]                 |
+---------------------------------+
1 row in set (0.00 sec)
 
mysql> SET @scalar = '1';
Query OK, 0 rows affected (0.00 sec)
 
mysql> SELECT json_array_append(@scalar, '$', 'c');
+---------------------------------+
| json_array_append(@scalar, '$', 'c') |
+---------------------------------+
| [1, "c"]                        |
+---------------------------------+
1 row in set (0.00 sec)
 
mysql> SELECT json_array_insert(@j, '$[1]', 'c');
+------------------------------------+
| json_array_insert(@j, '$[1]', 'c') |
+------------------------------------+
| ["a", "c", "b"]                    |
+------------------------------------+
1 row in set (0.00 sec)
```

**json_replace、json_set和json_insert示例**

- json_replace：只替换已经存在的旧值
- json_set：替换旧值，并插入不存在的新值
- json_insert：插入新值，但不替换已经存在的旧值

替换值，json_replace示例

```
mysql> UPDATE employees SET data->'$.name' = 'Andy' where data->'$.id' = 2;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '->'$.name' = 'Andy' where data->'$.id' = 2' at line 1
 
mysql> UPDATE employees
    -> SET data = json_replace(data, '$.name', 'Andy')
    -> WHERE data->'$.id' = 2;
Query OK, 1 row affected (0.03 sec)
Rows matched: 1  Changed: 1  Warnings: 0
 
mysql> SELECT * FROM employees WHERE data->'$.id'= 2;
+---------------------------+
| data                      |
+---------------------------+
| {"id": 2, "name": "Andy"} |
+---------------------------+
1 row in set (0.00 sec)
```

设置值，json_set示例

```
mysql> UPDATE employees
    -> SET data = json_set(data, '$.name', 'Bill', '$.city', '北京')
    -> WHERE data->'$.id'= 2;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
 
mysql> SELECT * FROM employees WHERE data->'$.id'= 2;
+---------------------------------------------+
| data                                        |
+---------------------------------------------+
| {"id": 2, "city": "北京", "name": "Bill"}   |
+---------------------------------------------+
1 row in set (0.00 sec)
```

插入值，json_insert示例

```
mysql> UPDATE employees
    -> SET data = json_insert(data, '$.name', 'Will', '$.address', '故宫')
    -> WHERE data->'$.id'= 2;
Query OK, 1 row affected (0.04 sec)
Rows matched: 1  Changed: 1  Warnings: 0
 
mysql> SELECT * FROM employees WHERE data->'$.id'= 2;
+---------------------------------------------------------------------+
| data                                                                |
+---------------------------------------------------------------------+
| {"id": 2, "city": "北京", "name": "Bill", "address": "故宫"}        |
+---------------------------------------------------------------------+
1 row in set (0.00 sec)
```

## 参考资料

**官方文档**
MySQL 5.7 Reference Manual

1. 12 Data Types, 12.6 The JSON Data Type [http://dev.mysql.com/doc/refman/5.7/en/json.html](https://link.jianshu.com/?t=http://dev.mysql.com/doc/refman/5.7/en/json.html)
2. 13 Functions and Operators, 13.16 JSON Functions [https://dev.mysql.com/doc/refman/5.7/en/json-functions.html](https://link.jianshu.com/?t=https://dev.mysql.com/doc/refman/5.7/en/json-functions.html)

**官方博客**

1. 2015-04 JSON Labs Release: Native JSON Data Type and Binary Format [http://mysqlserverteam.com/json-labs-release-native-json-data-type-and-binary-format/](https://link.jianshu.com/?t=http://mysqlserverteam.com/json-labs-release-native-json-data-type-and-binary-format/)
2. 2015-04 JSON Labs Release: JSON Functions, Part 1 — Manipulation JSON Data [http://mysqlserverteam.com/json-labs-release-json-functions-part-1-manipulation-json-data/](https://link.jianshu.com/?t=http://mysqlserverteam.com/json-labs-release-json-functions-part-1-manipulation-json-data/)
3. 2015-04 JSON Labs Release: JSON Functions, Part 2 — Querying JSON Data [http://mysqlserverteam.com/mysql-5-7-lab-release-json-functions-part-2-querying-json-data/](https://link.jianshu.com/?t=http://mysqlserverteam.com/mysql-5-7-lab-release-json-functions-part-2-querying-json-data/)
4. 2015-10 Inline JSON Path Expressions in MySQL 5.7 [http://mysqlserverteam.com/inline-json-path-expressions-in-mysql-5-7/](https://link.jianshu.com/?t=http://mysqlserverteam.com/inline-json-path-expressions-in-mysql-5-7/)





https://www.jianshu.com/p/d294baa873ff