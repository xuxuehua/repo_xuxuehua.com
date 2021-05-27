---
title: "sql_operation"
date: 2020-09-16 14:45
---
[toc]





# copy



## export to csv

without header

```
\copy (SELECT * FROM persons) to '/tmp/persons_client.csv' with csv
```

with header

```
\copy table_name to 'filename.csv' csv header
```



# insert 

## into

```
CREATE SEQUENCE IF NOT EXISTS app4_id_seq START 20600000000001;
CREATE TABLE IF NOT EXISTS app4 (id bigint default nextval('app4_id_seq'), original_code text);
CREATE UNIQUE INDEX IF NOT EXISTS app4_id ON app4(original_code);
```



```
insert into app4 (original_code) values  ('c56d05bbc06fe4569c56d05bbc061697c2d1'), ('3bb48267ca4a47d8de97ad3f45919e883bb48267ca4a47d8e883bb48267ca4a47d8de97ad3f45919e883bb2')  on conflict (original_code) do nothing returning id, original_code;
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



### current timestamp

To insert the current time use `current_timestamp` [as documented in the manual](https://www.postgresql.org/docs/current/static/functions-datetime.html#FUNCTIONS-DATETIME-CURRENT):

```sql
INSERT into "Group" (name,createddate) 
VALUES ('Test', current_timestamp);
```

To *display* that value in a different format change the configuration of your SQL client or format the value when SELECTing the data:

```sql
select name, to_char(createddate, ''yyyymmdd hh:mi:ss tt') as created_date
from "Group"
```



For `psql` (the default command line client) you can configure the display format through the configuration parameter `DateStyle`: https://www.postgresql.org/docs/current/static/runtime-config-client.html#GUC-DATESTYLE



### 字段含单引号

```
insert into feature_path (id, text_value) values
(11779156, $$featured10+40415+1+top banners #0/45$$),                                                                 
(11779157, $$featured10+40415+1+top banners #0/45 > app top banner #0/45 ##1/9$$),                                    
(11779158, $$featured10+40415+1+top banners #0/45 > app top banner #0/45 ##2/9$$),                                    
(11779159, $$featured10+40415+1+top banners #0/45 > app top banner #0/45 ##3/9$$),                                    
(11779160, $$featured10+40415+1+top banners #0/45 > app top banner #0/45 ##4/9$$),                                    
(11779161, $$featured10+40415+1+top banners #0/45 > app top banner #0/45 ##5/9$$),                                    
(11779162, $$featured10+40415+1+top banners #0/45 > app top banner #0/45 ##7/9$$),                                    
(11779163, $$featured10+40415+1+top banners #0/45 > app top banner #0/45 ##8/9$$),                                    
(11779164, $$featured10+40415+1+One-Click-Love$$),                                                                    
(11779165, $$featured10+40415+1+This Week's Top Picks$$),                                                                                                 
(11779222, $$featured10+33811+1+Jeux grand public > Plus$$),                                                          
(11779223, $$featured10+33811+1+Jeux amusants > Plus$$),                                                              
(11779224, $$featured10+33811+1+Jeux multijoueurs > Plus$$),                                                          
(11779225, $$featured10+33811+1+Jeux hors connexion > Plus$$),                                                        
(11779226, $$featured10+33811+1+top banners #0/30 > L'amour en un clic – Applications Android sur Google Play$$),     
(11779227, $$featured10+33903+1+Palmarès gratuit dans la catégorie Jeux de réflexion$$),                              
(11779228, $$featured10+33876+1+Palmarès gratuit dans la catégorie Jeux grand public$$),                              
(11779229, $$featured10+33903+1+Palmarès gratuit dans la catégorie Jeux de réflexion > En voir plus$$)

on conflict (text_value) do nothing returning id, text_value;
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





## 重复item

```
SELECT task_id, attempt, COUNT(attempt) FROM  prod_usage.task_attempts GROUP BY task_id, attempt HAVING COUNT(attempt) > 1;
```





## specific date

```sql
select * from audit_logs where date(created_date) = '2018-11-28'
```



```
\copy (select application_name, count(application_name) from datapipeline.workflow_instance where DATE_TRUNC('day', create_time) = CURRENT_DATE - interval '27 day'  group by application_name ) To '/tmp/27.csv' With CSV DELIMITER ',' HEADER ;
```



## group by column

```
user_data=> SELECT task_id, attempt, COUNT(attempt) FROM  marketing_bigdata.task_attempts GROUP BY task_id, attempt HAVING COUNT(attempt) > 1;
 task_id | attempt | count
---------+---------+-------
      15 |       0 |     2
     619 |       0 |     2
     621 |       0 |     2
    1113 |       0 |     2
     727 |       0 |     2
     733 |       0 |     2
     707 |       0 |     2
```





考慮表公司有如下記錄：

```
# select * from COMPANY;
 id | name  | age | address   | salary
----+-------+-----+-----------+--------
  1 | Paul  |  32 | California|  20000
  2 | Allen |  25 | Texas     |  15000
  3 | Teddy |  23 | Norway    |  20000
  4 | Mark  |  25 | Rich-Mond |  65000
  5 | David |  27 | Texas     |  85000
  6 | Kim   |  22 | South-Hall|  45000
  7 | James |  24 | Houston   |  10000
(7 rows)
```

如果想了解每個客戶的工資總額，然後GROUP BY查詢將如下：

```
testdb=# SELECT NAME, SUM(SALARY) FROM COMPANY GROUP BY NAME;
```

這將產生以下結果：

```
  name  |  sum
 -------+-------
  Teddy | 20000
  Paul  | 20000
  Mark  | 65000
  David | 85000
  Allen | 15000
  Kim   | 45000
  James | 10000
(7 rows)
```





# update

## set multi line

```
user_data=> update datapipeline.arm_clusters set instance_groups=$$[{'block_devices': [{'ec2_vol_size': 100, 'ec2_vol_type': 'gp2'}], 'group_name': 'master', 'instance_lifecycle': 'spot', 'instances': [{'instance_type': 'm5.xlarge', 'instance_weighted_capacity': 1}], 'target_capacity': 1}, {'block_devices': [{'ec2_vol_size': 100, 'ec2_vol_type': 'gp2'}], 'group_name': 'slave-0', 'instance_lifecycle': 'spot', 'instances': [{'instance_type': 'r5.2xlarge', 'instance_weighted_capacity': 1}], 'target_capacity': 2}]$$ where cluster_name='dump_routine';
UPDATE 1
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





# Appendix

https://www.postgresqltutorial.com/export-postgresql-table-to-csv-file/

http://tw.gitbook.net/postgresql/2013080564.html

https://popsql.com/learn-sql/postgresql/how-to-query-date-and-time-in-postgresql

