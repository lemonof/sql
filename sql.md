##### 修改表结构

alter table tableName alter column columns set data type varchar(50);

##### 增加字段

alter table tableName add column columns varchar(100);

comment on column tableName.columns is "xxxcolumns"

导数

db2 connect to schame