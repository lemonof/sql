<<<<<<< HEAD
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

##### 只查询一条

select * from table fetch first 1 rows only;

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



