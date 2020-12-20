---
title: "pg_dump"
date: 2020-09-20 15:46
---
[toc]





# pg_dump



## installation

```
sudo tee /etc/yum.repos.d/pgdg.repo<<EOF
[pgdg12]
name=PostgreSQL 12 for RHEL/CentOS 7 - x86_64
baseurl=https://download.postgresql.org/pub/repos/yum/12/redhat/rhel-7-x86_64
enabled=1
gpgcheck=0
EOF

sudo yum makecache
yum search postgresq
yum install postgresql12.x86_64

/usr/pgsql-12/bin/psql --version
```



or 

```
cd /tmp
wget https://ftp.postgresql.org/pub/source/v11.4/postgresql-11.4.tar.gz
tar zxvf postgresql-11.4.tar.gz
cd postgresql-11.4
./configure --without-readline && make && make install
```

> By default, it will install pg_dump into `/usr/local/pgsql/bin/pg_dump`





## -s, --schema-only      

 dump only the schema, no data

```
/usr/pgsql-12/bin/pg_dump -s -h rxutestpostgres.com -p 5432 -U postgres id_generator > abc.txt
```

```
# cat abc.txt 
--
-- PostgreSQL database dump
--

-- Dumped from database version 12.3
-- Dumped by pg_dump version 12.4

SET statement_timeout = 0;
SET lock_timeout = 0;
SET idle_in_transaction_session_timeout = 0;
SET client_encoding = 'UTF8';
SET standard_conforming_strings = on;
SELECT pg_catalog.set_config('search_path', '', false);
SET check_function_bodies = false;
SET xmloption = content;
SET client_min_messages = warning;
SET row_security = off;

--
-- Name: app_history_id_seq; Type: SEQUENCE; Schema: public; Owner: postgres
--

CREATE SEQUENCE public.app_history_id_seq
    START WITH 20600000000001
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.app_history_id_seq OWNER TO postgres;

SET default_tablespace = '';

SET default_table_access_method = heap;

--
-- Name: app_history; Type: TABLE; Schema: public; Owner: postgres
--

CREATE TABLE public.app_history (
    id bigint DEFAULT nextval('public.app_history_id_seq'::regclass),
    text_value text
);


ALTER TABLE public.app_history OWNER TO postgres;

--
-- Name: statuscode_id_seq; Type: SEQUENCE; Schema: public; Owner: postgres
--

CREATE SEQUENCE public.statuscode_id_seq
    START WITH 20600000000001
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.statuscode_id_seq OWNER TO postgres;

--
-- Name: statuscode; Type: TABLE; Schema: public; Owner: postgres
--

CREATE TABLE public.statuscode (
    id bigint DEFAULT nextval('public.statuscode_id_seq'::regclass),
    text_value text
);


ALTER TABLE public.statuscode OWNER TO postgres;

--
-- Name: app_history_index; Type: INDEX; Schema: public; Owner: postgres
--

CREATE UNIQUE INDEX app_history_index ON public.app_history USING btree (text_value);


--
-- Name: statuscode_id; Type: INDEX; Schema: public; Owner: postgres
--

CREATE UNIQUE INDEX statuscode_id ON public.statuscode USING btree (text_value);


--
-- Name: SCHEMA public; Type: ACL; Schema: -; Owner: postgres
--

REVOKE ALL ON SCHEMA public FROM rdsadmin;
REVOKE ALL ON SCHEMA public FROM PUBLIC;
GRANT ALL ON SCHEMA public TO postgres;
GRANT ALL ON SCHEMA public TO PUBLIC;


--
-- PostgreSQL database dump complete
--
```



## -t, --table=TABLE            

dump the named table(s) only



