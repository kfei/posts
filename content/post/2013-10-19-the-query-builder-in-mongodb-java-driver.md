+++
title = "The query builder in MongoDB Java driver"
date = "2013-10-19T14:05:00+08:00"
tags = ["mongodb", "java"]
+++

一直都習慣先在 Mongo Shell 裡邊寫邊測 query, 所以寫進 Java code
時自然也就習慣了用 `DBObject` 這種 document 樣式的 query 寫法. 例如:

```java
DBObject query = new BasicDBObject( "x", new BasicDBObject( "$lt", 1 ) )
                .append( "y", new BasicDBObject( "$gt", 10).append( "$lt", 90 ) )
                .append( "z", new BasicDBObject( "$in", Arrays.asList( 1, 3, 5, 7 ) ) );
```
<!--more-->

在 Mongo Shell 等價於這樣的 query json:

```js
{ x : { $lt : 1 }, y : { $gt : 10, $lt : 90 }, z : { $in : [1, 3, 5, 7] } }
```

然而, 對於更複雜的 query, 會因為更多的 `new BasicDBObject` 而變得更肥大且不可讀. 這或許就是 query builder 上場的好時機吧! 上面的 query 用 builder 的寫法如下, 可以比較直覺的讀懂, 對其他團隊成員或是將來的自己都更友善了些:

```java
QueryBuilder query = QueryBuilder.start( "x" ).lessThan( 10 )
                    .and( "y" ).greaterThan( 10 ).lessThan( 90 )
                    .and( "z" ).in( Arrays.asList( 1, 3, 5, 7 ) );
```
