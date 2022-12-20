SELECT (select description from pg_description where objoid = oid and objsubid = 0),b.relname,a.attname,pg_catalog.format_type(a.atttypid, a.atttypmod) AS data_type,(case when atttypmod - 4 > 0 then atttypmod - 4 else 0 end) as 长度,
       (case
      when (select count(*)
         from pg_constraint
         where conrelid = a.attrelid and conkey[1] = attnum and contype = 'p') > 0 then 'Y'
      else 'N' end)                     as 主键约束,
    (case
      when (select count(*)
         from pg_constraint
         where conrelid = a.attrelid and conkey[1] = attnum and contype = 'u') > 0 then 'Y'
      else 'N' end)                     as 唯一约束,
    (case
      when (select count(*)
         from pg_constraint
         where conrelid = a.attrelid and conkey[1] = attnum and contype = 'f') > 0 then 'Y'
      else 'N' end)                     as 外键约束,
    (case when a.attnotnull = true then 'Y' else 'N' end)    as 可否为空,
    col_description(a.attrelid, a.attnum)            as 描述
 FROM  pg_catalog.pg_attribute a,
    (
     SELECT c.oid  ,c.relname,n.nspname
      FROM  pg_catalog.pg_class c
      LEFT JOIN pg_catalog.pg_namespace n
       ON n.oid = c.relnamespace
     WHERE 1= 1
     and relkind = 'r'
    and c.relname = 'pg_class'
      AND n.nspname = 'auth'
     )b
 WHERE a.attrelid = b.oid
 AND a.attnum > 0
 AND NOT a.attisdropped ORDER BY b.relname,a.attnum;