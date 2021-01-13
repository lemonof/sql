----DB2----

##### 修改表结构

alter table tableName alter column columns set data type varchar(50);

##### 增加字段

alter table tableName add column columns varchar(100);

comment on column tableName.columns is "xxxcolumns"

##### 数据导入导出

db2 connect to schema

db2 "export to tableName.ixf of ixf select * from schema.tableName"

db2 "export to tableName.del of del select * from schema.tableName"

db2 "load from tableName.ixf of ixf replace into schema.tableName"

db2 "load from tableName.del of del replace into schema.tableName"

##### 数据迁移

db2 connect to cbrcdb

db2look -d cbrcdb -z schema -e -o schema.sql  --不带drop

db2look -d cbrcdb -z schema -e -dp -o schema.sql  --带drop

db2 -tvf schema.sql | tee schema.log

db2 disconnect all

##### 增加字段

alter table tableName add column columns varchar(100);

comment on column tableName.columns is "xxxcolumns"

##### 查询一条或抽样查询

select * from table fetch first 1 rows only;

select * from table order by rand() fetch first 1 rows only;

select * from staff tablesample bernoulli(8) repeatable(586) order by id;

--说明：从staff 表中，采用bernoulli 抽样方法，抽取8%的样本数据，repeatable表示多次执行相同的语句返回相同的结果。

采样方法：

1、bernoulli（行级别伯努利采样）：它检查每一行，准确率高，但是性能差。

2、system（系统页级采样）：它检查每一数据页（一个数据页包含若干行），性能高，但是准确率差。

##### 查找解锁

select * from sysibmadm.locks_held;  ---查看锁表情况

db2 "force applications(56001)"   --解锁

##### 重组表

db2 connect to cbrcdb

db2 reorg table schema.tableName

##### 创建索引

普通索引

create indes "schema"."indexName" on "schema"."tableName"

  ("字段1" asc, "字段2" asc)

  compress no allow reverse scans;

唯一索引

create unique index "schema"."indexName" on "schema"."tableName"

  ("字段" asc)

  xompress no allow reverse scans;

主键约束索引

alter table "schema"."tableName"

  add constraint "pk_indexName" primary key

  ("inst_id");

##### 删除schema

drop schema "schemaName" restrict

db2 "drop schema |"schemaName"| restrict"

##### 字符集

modified by codepage = 1208

##### 分页查询

select  * from 

(

   select b.*,rownumber()  over()  as rn from

  ( select * from <tableName> ) as b

) as a where a.rn between <start_number> and <end_number>;

##### 并集、交集

union

用来求两个集合的并集；

intersect

用来求两个集合的交集；

except

用来求在第一个集合中存在，而在第二个集合中不存在的记录。

关键字后面都可以接 all   如果不接all 操作集合将会去除重复值。

##### 更新语句

update tableName set  age = 18 where condition;

update 

(

​     select * from tableName where condition

)  set age = 18 ;

##### 删除语句

delete from tableName where condition;

delect from 

(

   select * from tableName where condition

);

alter table tableName activate not logged initially with empty table;

##### 更新并查询更新前后的数据

select * from final table

(

​    update schema.tableName set   age = 'xxx' where condition

);  ---更新后的数据

select * from oldtable

(

​    update schema.tableName set   age = 'xxx' where condition

);   --更新前的数据

##### 复制表

create table newTableName like oldTableName in 表空间；

create table newTableName as ( select * from oldTableName ) definition only in 表空间；

##### 重启DB2

1、查看是否有活动的连接

db2 list applications for db db_name

2、关闭连接

db2 force application all 

3、再执行步骤1 查看是否全部关闭

4、停止实例

db2stop 

5、启动实例

db2start

6、如果此时连接不了，则需要激活目标数据库

​      查看是否有活跃的数据库

​     db2 list active database

​     如果没有，则需要对目标数据库进行激活设置

​     db2 active database db_name

快速停止实例

db2stop force

