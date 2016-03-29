+++
title = "Estimating the Size of an Index Before Creation"
date = "2013-10-14T07:27:00+08:00"
tags = ["database"]
+++

最近需要在一個肥大的表上建索引, 考慮到可能會將索引的實體儲存和表分開, 於是有必要在建立索引之前, 先估算其可能會佔用的容量大小, 以方便配置儲存空間.
Oracle 提供了 `dbms_space.create_index_cost` 這個 package 可用. 假設我們要估算在 scott 用戶的 emp 表中的 name 欄位建立索引可能耗用的空間, 可以這樣做：
```sql
set serverout on
exec dbms_stats.gather_table_stats(scott,'emp');
var used_bytes number
var alloc_bytes number
exec dbms_space.create_index_cost('create index name_idx on emp(name)',
                                  :used_bytes,
                                  :alloc_bytes);
print :used_bytes
print :alloc_bytes
```
