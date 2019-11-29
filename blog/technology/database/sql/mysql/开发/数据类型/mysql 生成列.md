[TOC]



# mysql 生成列		

  					作者： 						初生不惑 					Java技术QQ群：227270512 / Linux QQ群：479429477 				 					 					 					 				

在本教程中，您将学习如何使用MySQL生成的列来存储从表达式或其他列计算的数据。

## MySQL生成列简介

创建新表时，可以在[CREATE TABLE](http://www.yiibai.com/mysql/create-table.html)语句中指定表列。 然后，使用[INSERT](http://www.yiibai.com/mysql/insert-statement.html)，[UPDATE](http://www.yiibai.com/mysql/update-data.html)和[DELETE](http://www.yiibai.com/mysql/delete-statement.html)语句直接修改表列中的数据。

*MySQL 5.7*引入了一个名为*生成列*的新功能。它之所以叫作*生成列*，因为此列中的数据是基于预定义的表达式或从其他列计算的。

例如，假设有以下结构的一个`contacts`表：

```sql
CREATE TABLE IF NOT EXISTS contacts (
    id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL
);
```

要获取联系人的全名，请使用[CONCAT()](http://www.yiibai.com/sql-concat-in-mysql.html)函数，如下所示：

```sql
SELECT 
    id, CONCAT(first_name, ' ', last_name), email
FROM
    contacts;
```

这不是最优的查询。

通过使用MySQL生成的列，可以重新创建`contacts`表，如下所示：

```sql
DROP TABLE IF EXISTS contacts;

CREATE TABLE contacts (
    id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    fullname varchar(101) GENERATED ALWAYS AS (CONCAT(first_name,' ',last_name)),
    email VARCHAR(100) NOT NULL
);
```

`GENERATED ALWAYS as(expression)`是创建生成列的语法。

要测试“全名”列，请在`contacts`表中[插入](http://www.yiibai.com/mysql/insert-statement.html)一行。

```sql
INSERT INTO contacts(first_name,last_name, email)
VALUES('john','doe','john.doe@yiibai.com');
 
```

现在，可以从`contacts`表中查询数据。

当从`contacts`表中查询数据时，`fullname`列中的值将立即计算。

MySQL提供了两种类型的生成列：存储和虚拟。每次读取数据时，虚拟列都将在运行中计算，而存储的列在数据更新时被物理计算和存储。

基于此定义，上述示例中的`fullname`列是虚拟列。

## MySQL生成列的语法

定义生成列的语法如下：

```sql
column_name data_type [GENERATED ALWAYS] AS (expression)
   [VIRTUAL | STORED] [UNIQUE [KEY]]
```

首先，指定列名及其数据类型。

接下来，添加`GENERATED ALWAYS`子句以指示列是生成的列。

然后，通过使用相应的选项来指示生成列的类型：`VIRTUAL`或`STORED`。 默认情况下，如果未明确指定生成列的类型，MySQL将使用`VIRTUAL`。

之后，在`AS`关键字后面的大括号内指定表达式。 该表达式可以包含文字，内置函数，无参数，操作符或对同一表中任何列的引用。 如果你使用一个函数，它必须是标量和确定性的。

最后，如果生成的列被存储，可以为它定义一个[唯一约束](http://www.yiibai.com/mysql/unique-constraint.html)。

## MySQL存储列示例

我们来看一下[示例数据库(yiibaidb)](http://www.yiibai.com/mysql/sample-database.html)中的`products`表。

```sql
mysql> desc products;
+--------------------+---------------+------+-----+---------+-------+
| Field              | Type          | Null | Key | Default | Extra |
+--------------------+---------------+------+-----+---------+-------+
| productCode        | varchar(15)   | NO   | PRI |         |       |
| productName        | varchar(70)   | NO   |     | NULL    |       |
| productLine        | varchar(50)   | NO   | MUL | NULL    |       |
| productScale       | varchar(10)   | NO   |     | NULL    |       |
| productVendor      | varchar(50)   | NO   |     | NULL    |       |
| productDescription | text          | NO   |     | NULL    |       |
| quantityInStock    | smallint(6)   | NO   |     | NULL    |       |
| buyPrice           | decimal(10,2) | NO   |     | NULL    |       |
| MSRP               | decimal(10,2) | NO   |     | NULL    |       |
+--------------------+---------------+------+-----+---------+-------+
9 rows in set 
```

使用`quantityInStock`和`buyPrice`列的数据，通过以下表达式计算每个`SKU`的股票值：

```sql
quantityInStock * buyPrice 
```

但是，可以使用以下[ALTER TABLE … ADD COLUMN](http://www.yiibai.com/mysql/add-column.html)语句将名为`stock_value`的存储的生成列添加到`products`表：

```sql
ALTER TABLE products
ADD COLUMN stockValue DOUBLE 
GENERATED ALWAYS AS (buyprice*quantityinstock) STORED; 
```

通常，`ALTER TABLE`语句需要完整的表重建，因此，如果更改大表是耗时的。 但是，虚拟列并非如此。

现在，我们可以直接从`products`表中查询库存值。

```sql
SELECT 
    productName, ROUND(stockValue, 2) AS stock_value
FROM
    products; 
```

执行上面查询语句，得到以下结果 - 

```shell
+---------------------------------------------+-------------+
| productName                                 | stock_value |
+---------------------------------------------+-------------+
| 1969 Harley Davidson Ultimate Chopper       |   387209.73 |
| 1952 Alpine Renault 1300                    |   720126.90 |
| 1996 Moto Guzzi 1100i                       |   457058.75 |
| 2003 Harley-Davidson Eagle Drag Bike        |   508073.64 |
| 1972 Alfa Romeo GTA                         |   278631.36 |
| 1962 LanciaA Delta 16V                      |   702325.22 |
| 1968 Ford Mustang                           |     6483.12 |
|************** 省略了一大波数据 ****************************|
| The Queen Mary                              |   272869.44 |
| American Airlines: MD-11S                   |   319901.40 |
| Boeing X-32A JSF                            |   159163.89 |
| Pont Yacht                                  |    13786.20 |
+---------------------------------------------+-------------+
110 rows in set


Shell
```

在本教程中，我们向您介绍了MySQL生成的列以存储从表达式或其他列计算的数据。





//原文出自【易百教程】，商业转载请联系作者获得授权，非商业转载请保留原文链接：https://www.yiibai.com/mysql/generated-columns.html  





