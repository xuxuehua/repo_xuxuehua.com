---
title: "sql_operation"
date: 2020-09-16 14:45
---
[toc]



# insert 

## into

```
CREATE SEQUENCE IF NOT EXISTS app4_id_seq START 20600000000001;
CREATE TABLE IF NOT EXISTS app4 (id bigint default nextval('app4_id_seq'), original_code text);
CREATE UNIQUE INDEX IF NOT EXISTS app4_id ON app4(original_code);
```



```
insert into app4 (original_code) values  ('c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2d1'), ('3bb48267ca4a47d8de97ad3f45919e883bb48267ca4a47d8de97ad3f45919e883bb48267ca4a47d8de97ad3f45919e883bb2')  on conflict (original_code) do nothing returning id, original_code;
```



```

insert into app4 (original_code)  (

select original_code from (values 
('c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2d'), ('c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c21'), ('c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c22'),
('c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c23'),
('c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c24'),
('c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c25'),
('c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c26'),
('c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c27'),
('c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c28'),
('c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c29') 

) as vt(original_code)

except

select original_code from app4 where original_code in ('c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2d', 'c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c21',
'c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c22',
'c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c23',
'c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c24',
'c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c25',
'c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c26', 
'c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c27',
'c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c28',
'c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c2de9e0cea2fe4569c56d05bbc061697c29') 

) on conflict (original_code) do nothing;
```





### Multi columns

```
insert into app (class, status) select class, 3 as status from (
    select class from (values ('com.sstf.catphotographycoloringbook.colorbynumber111'), ('com.MenuDino.TopfitComidaSaudavel'), ('111'), ('222')) as vt(class)
    except
    select class from app where class in ('com.sstf.catphotographycoloringbook.colorbynumber', 'com.MenuDino.TopfitComidaSaudavel', '111', '222') 
) a on conflict (class) do nothing returning id, class;
```



# select



## describe table

```
select column_name, data_type, character_maximum_length, column_default, is_nullable
from INFORMATION_SCHEMA.COLUMNS where table_name = '<name of table>';
```



```
(in the psql command-line tool):

\d+ tablename
```







# FAQ



## Good SQL

BAD QUERY which will load all  data of 2020-09-30 on each plproxy node, and SLOW DOWN  production DB

```
SELECT date, count(*) FROM plproxy.execute_select(\$proxy\$
    SELECT * FROM mw.domain_x_domain_m WHERE date = '2020-09-30' 
\$proxy\$) t (.......) group by date order by date desc;
```



should do the aggregation(count/sum) in the query inside, and sum the count from outside query

```
SELECT  sum(count_a) FROM plproxy.execute_select(\$proxy\$
    SELECT count(*) as count_a FROM mw.domain_x_domain_m WHERE date = '2020-09-30' 
\$proxy\$) t (count_a bigint) ;
```