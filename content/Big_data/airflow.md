---
title: "airflow"
date: 2021-01-07 10:45
---
[toc]



# Airflow

Airflow 是一个编排、调度和监控workflow的平台，由Airbnb开源，现在在Apache Software Foundation 孵化。AirFlow 将workflow编排为tasks组成的DAGs，调度器在一组workers上按照指定的依赖关系执行tasks。同时，Airflow 提供了丰富的命令行工具和简单易用的用户界面以便用户查看和操作，并且Airflow提供了监控和报警系统。

Airflow 使用 DAG (有向无环图) 来定义工作流，配置作业依赖关系非常方便，从管理方便和使用简单角度来讲，AirFlow远超过其他的任务调度工具。



## expectation

1. How Airflow handle logs. Could it  sync and display spark task log in real time? Where the log store.
2. How Airflow handle kill task. Could it kill the spark task?
3. How Airflow handle task rerun. Could it show each task attempts?
4. Cal Airflow skip task?
5. Is it possible to adapt the loop executor in BDP on Airflow? By inherit an operator?
6. Could Airflow construct and run a Graph like current BDP?





1. We can only see livy operator logs in web including the process of calling livy API, but no logs in spark task.
2. A task can be marked as FAILED status or be cleared in the web, and both can trigger a kill. It also can kill the yarn application of it.
3. Click CLEAR button in web can trigger to rerun a task with some options. Each task attempts can be recorded and review. The picture shows what operations we can do via the web to control a task.
4. Airflow can skip a task by marking it FAILED or SUCCESS. but as far as I know, it dose not have a skip status.
5. We can pass input parameters like start_date and end_date which can be used in our task to loop historical days. Also we can inherit an operator to handle this but maybe not easy.
6. Airflow can construct and run a Graph like current BDP. The logic of composing workflow from tasks is the same. There is a picture of complex DAG below. It looks like more irregular and disorder than BDP workflow.



## 特点

- 灵活易用，AirFlow 本身是 Python 编写的，且工作流的定义也是 Python 编写，有了 Python胶水的特性，没有什么任务是调度不了的，有了开源的代码，没有什么问题是无法解决的，你完全可以修改源码来满足个性化的需求，而且更重要的是代码都是 –human-readable 。
- 功能强大，自带的 Operators 都有15+，也就是说本身已经支持 15+ 不同类型的作业，而且还是可自定义 Operators，什么 shell 脚本，python，mysql，oracle，hive等等，无论不传统数据库平台还是大数据平台，统统不在话下，对官方提供的不满足，完全可以自己编写 Operators。
- 优雅，作业的定义很简单明了, 基于 jinja 模板引擎很容易做到脚本命令参数化，web 界面更是也非常 –human-readable ，谁用谁知道。
- 极易扩展，提供各种基类供扩展, 还有多种执行器可供选择，其中 CeleryExcutor 使用了消息队列来编排多个工作节点(worker), 可分布式部署多个 worker ，AirFlow 可以做到无限扩展。
- 丰富的命令工具，你甚至都不用打开浏览器，直接在终端敲命令就能完成测试，部署，运行，清理，重跑，追数等任务，想想那些靠着在界面上不知道点击多少次才能部署一个小小的作业时，真觉得AirFlow真的太友好了。





# 架构

Airflow由多个部分组成。 并非必须部署所有这些工具（例如，对于Flower和Webserver 可不做部署）。但是在设置和调试系统时，它们都会派上了用场。这些组成部分有以下几种 –

| Name               | Description                           |
| ------------------ | ------------------------------------- |
| Flower 与Webserver | 用于监测和与Airflow集群交互的用户前端 |
| Scheduler          | 连续轮询DAG并安排任务。 监视执行      |
| Workers            | 从队列执行任务，并报告回队列          |
| Shared Filesystem  | 在所有群集节点之间同步DAG             |
| Queue              | 可靠地调度计划任务和任务结果          |
| Metadb             | 存储执行历史记录，工作流和其他元数据  |



![image-20210110113413227](airflow.assets/image-20210110113413227.png)









## Scheduler

The Airflow scheduler monitors all tasks and DAGs, then triggers the task instances once their dependencies are complete. 

Behind the scenes, the scheduler spins up a subprocess, which monitors and stays in sync with all DAGs in the specified DAG directory. Once per minute, by default, the scheduler collects DAG parsing results and checks whether any active tasks can be triggered.

The Airflow scheduler is designed to run as a persistent service in an Airflow production environment. 

To kick it off, all you need to do is execute the airflow scheduler 

```
airflow scheduler
```

It uses the configuration specified in `airflow.cfg`





**Taskinstance** dagrun下面的一个任务实例。具体来说，对于每个dagrun实例，算子（operator）都将转成对应的Taskinstance。由于任务可能失败，根据定义调度器决定是否重试。不同的任务实例由 dagid/执行时间（execution date）/算子/执行时间/重试次数进行区分。

**Executor** 任务执行器。每个任务都需要由任务执行器完成。BaseExecutor是所有任务执行器的父类。

**LocalTaskJob** 负责监控任务与行，其中包含了一个重要属性taskrunner。

**TaskRunner** 开启子进程，执行任务。



### 触发机制

The first DAG Run is created based on the minimum `start_date` for the tasks in your DAG. 

Subsequent DAG Runs are created by the scheduler process, based on your DAG’s `schedule_interval`, sequentially.

A job with `schedule_interval` set as `@daily` runs after the day has ended. 







### More Than One Scheduler

Airflow supports running more than one scheduler concurrently -- both for performance reasons and for resiliency.



### DB requirements

To maintain performance and throughput there is one part of the scheduling loop that does a number of calculations in memory (because having to round-trip to the DB for each TaskInstance would be too slow) so we need to ensure that only a single scheduler is in this critical section at once - otherwise limits would not be correctly respected. To achieve this we use database row-level locks (using `SELECT ... FOR UPDATE`).



This critical section is where TaskInstances go from scheduled state and are enqueued to the executor, whilst ensuring the various concurrency and pool limits are respected. The critical section is obtained by asking for a row-level write lock on every row of the Pool table (roughly equivalent to `SELECT * FROM slot_pool FOR UPDATE NOWAIT` but the exact query is slightly different).





## Executor

Executors are the mechanism by which task instances get run.

Airflow has support for various executors. Current used is determined by the `executor` option in the `[core]` section of the configuration file. 

If it is to load your own executor, then you should specify the full path to the module e.g. `my_acme_company.executors.MyCustomExecutor`.

Executor （执行器）是Airflow 中一个重要的概念。在Airflow中执行器（Executor）是运行任务实例的机制。一旦一个DAG被定义，为了在DAG中执行和完成单个或一组“任务”，需要执行以下操作:

- 在元数据数据库(例如：PostgreSQL)中记录一个DAG中所有的任务以及它们在后台的相应状态(排队、调度、运行、成功、失败等)
- scheduler从元数据库读取数据，检查每个任务的状态，并决定需要完成什么(以及按照什么顺序执行)。这就是executor的传统作用
- Executor与scheduler密切合作，以确定哪些资源在排队时将实际完成这些任务



执行器之间的差别在于他们拥有资源，以及它们如何选择利用这些资源来分配工作(或者根本不分配工作)。这里有六种常见的执行器 –

| Name                | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| Sequential Executor | 顺序逐个地启动任务                                           |
| Local Executor      | 在本地并行启动任务                                           |
| Celery Executor     | Celery是执行程序首选种类型。实际上，它被用于在多个节点上并行分布处理 |
| Dask Executor       | 这种执行器允许Airflow在Python集群Dask中启动不同的任务        |
| Kubernetes Executor | 这种执行器允许Airflow在Kubernetes集群中创建或分组任务。 （注意：该功能要求Airflow最小的版本为1.10） |
| Debug Executor      | DebugExecutor设计为调试工具，可以从IDE中使用                 |





## lyft airflow architecture

![image-20210201151205863](airflow.assets/image-20210201151205863.png)



**Configuration**: Apache Airflow 1.8.2 with cherry-picks, and numerous in-house Lyft customized patches.

**Scale**: Three sets of Amazon auto scaling group (ASG) for celery workers, each of which is associated with one celery queue:

- ASG #1: 15 worker nodes each of which is the *r5.4xlarge* type. This fleet of workers is for processing low-priority memory intensive tasks.
- ASG #2: 3 worker nodes each of which is the *m4.4xlarge* type. This fleet of workers is dedicated for those DAGs with a strict SLA.
- ASG #3: 1 worker node which is the *m4.10xlarge* type. The single node is used to process the compute-intensive workloads from a critical team’s DAGs.

**Numbers of DAGs / Tasks**: **500+** DAGs, **800+** DagRuns, **25000+** TaskInstances running on Airflow platform at Lyft daily.



### monitor

At Lyft, we leverage various technologies including Datadog, Statsd, Grafana, and PagerDuty to monitor the Airflow system.



# 工作流程

![image-20210202185029690](airflow.assets/image-20210202185029690.png)



# DAG

a `DAG` -- or a Directed Acyclic Graph -- is a collection of all the tasks you want to run, organized in a way that reflects their relationships and dependencies.

A DAG is defined in a Python script, which represents the DAGs structure (tasks and their dependencies) as code.



有向无环图。有向无环图用于定义任务的任务依赖关系。任务的定义由算子operator进行，其中，BaseOperator是所有算子的父类。



## Dagrun

有向无环图任务实例。在调度器的作用下，每个有向无环图都会转成任务实例。不同的任务实例之间用dagid/ 执行时间（execution date）进行区分。



## SubDAG (不推荐)

subdag可以使用自己的executor类型（默认为SequentialExecutor)，但其复杂，容易出现死锁现象

`dags/subdags/subdag_parallel_dag.py`

```
from airflow import DAG
from airflow.operators.bash import BashOperator

def subdag_parallel_dag(parent_dag_id, child_dag_id, default_args):
    with DAG(dag_id=f'{parent_dag_id}.{child_dag_id}', default_args=default_args) as dag:
        task_2 = BashOperator(
            task_id='task_2',
            bash_command='sleep 3'
        )

        task_3 = BashOperator(
            task_id='task_3',
            bash_command='sleep 3'
        )

        return dag
```



`dags/parallel_dag.py/`

```
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.subdag import SubDagOperator
from subdags.subdag_parallel_dag import subdag_parallel_dag

from datetime import datetime

default_args = {
    'start_date': datetime(2021, 2, 7)
}

with DAG('parallel_dag', schedule_interval='@daily', default_args=default_args) as dag:

    task_1 = BashOperator(
        task_id='task_1',
        bash_command='sleep 3'
    )

    processing = SubDagOperator(
        task_id='processing_tasks',
        subdag=subdag_parallel_dag(
            parent_dag_id='parallel_dag', 
            child_dag_id='processing_tasks', 
            default_args=default_args
        )
    )

    task_4 = BashOperator(
        task_id='task_4',
        bash_command='sleep 3'
    )

    task_1 >> processing >> task_4
```



## TaskGroup （推荐）

![image-20210207233119639](airflow.assets/image-20210207233119639.png)

```
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.subdag import SubDagOperator
from airflow.utils.task_group import TaskGroup

from datetime import datetime

default_args = {
    'start_date': datetime(2021, 2, 7)
}

with DAG('parallel_dag', schedule_interval='@daily', default_args=default_args) as dag:

    task_1 = BashOperator(
        task_id='task_1',
        bash_command='sleep 3'
    )
    with TaskGroup('processing_task') as processing_tasks:
        task_2 = BashOperator(
            task_id='task_2',
            bash_command='sleep 3'
        )

        task_3 = BashOperator(
            task_id='task_3',
            bash_command='sleep 3'
        )

    task_4 = BashOperator(
        task_id='task_4',
        bash_command='sleep 3'
    )

task_1 >> processing_tasks >> task_4

```





![image-20210207233853671](airflow.assets/image-20210207233853671.png)



```
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.subdag import SubDagOperator
from airflow.utils.task_group import TaskGroup

from datetime import datetime

default_args = {
    'start_date': datetime(2021, 2, 7)
}

with DAG('parallel_dag', schedule_interval='@daily', default_args=default_args) as dag:

    task_1 = BashOperator(
        task_id='task_1',
        bash_command='sleep 3'
    )
    with TaskGroup('processing_task') as processing_tasks:
        task_2 = BashOperator(
            task_id='task_2',
            bash_command='sleep 3'
        )

        with TaskGroup('spark_tasks') as spark_tasks:
            task_3 = BashOperator(
                task_id='task_3',
                bash_command='sleep 3'
            )

        with TaskGroup('flink_tasks') as flink_tasks:
            task_3 = BashOperator(
                task_id='task_3',
                bash_command='sleep 3'
            )

    task_4 = BashOperator(
        task_id='task_4',
        bash_command='sleep 3'
    )

task_1 >> processing_tasks >> task_4
```



# Worker

Process or subprocess to execute your task



# Operator

A template for a specific type of work to be executed. 

For example, BashOperator represents how to execute a bash script while PythonOperator represents how to execute a python function, etc.



一个operator只做一件事情





## Sensor

a type of special operator which will only execute if a certain condition is met.



## BranchPythonOperator

![image-20210208133503792](airflow.assets/image-20210208133503792.png)



```
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.python import PythonOperator, BranchPythonOperator
from airflow.operators.subdag import SubDagOperator
from airflow.utils.task_group import TaskGroup
from airflow.operators.dummy import DummyOperator

from random import uniform
from datetime import datetime

default_args = {
    'start_date': datetime(2020, 1, 1)
}

def _training_model(ti):
    accuracy = uniform(0.1, 10.0)
    print(f'model\'s accuracy: {accuracy}')
    ti.xcom_push(key='model_accuracy', value=accuracy)


def _choose_best_model(ti):
    print('choose best model')
    accuracies = ti.xcom_pull(key='model_accuracy', task_ids=[
        'processing_tasks.training_model_a',
        'processing_tasks.training_model_b',
        'processing_tasks.training_model_c'
    ])
    print(accuracies)

def _is_accurate():
    return('accurate')

with DAG('xcom_dag', schedule_interval='@daily', default_args=default_args, catchup=False) as dag:

    downloading_data = BashOperator(
        task_id='downloading_data',
        bash_command='sleep 3',
        do_xcom_push=False
    )

    with TaskGroup('processing_tasks') as processing_tasks:
        training_model_a = PythonOperator(
            task_id='training_model_a',
            python_callable=_training_model
        )

        training_model_b = PythonOperator(
            task_id='training_model_b',
            python_callable=_training_model
        )

        training_model_c = PythonOperator(
            task_id='training_model_c',
            python_callable=_training_model
        )

    choose_model = PythonOperator(
        task_id='task_4',
        python_callable=_choose_best_model
    )

    is_accurate = BranchPythonOperator(
        task_id='is_accurate',
        python_callable=_is_accurate
    )

    accurate = DummyOperator(
        task_id='accurate'
    )

    inaccurate = DummyOperator(
        task_id='inaccurate'
    )

    downloading_data >> processing_tasks >> choose_model
    choose_model >> is_accurate >> [accurate, inaccurate]

```



## DockerOperator



# Task

A parameterized instance of an operator/sensor which represents a unit of actual work to be executed.



## Xcoms （share data within tasks)

Data limitation within tasks

```
Sqlite -> 2GB
Postgresql -> 1GB
MySQL -> 64KB
```



```
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.python import PythonOperator
from airflow.operators.subdag import SubDagOperator
from airflow.utils.task_group import TaskGroup

from random import uniform
from datetime import datetime

default_args = {
    'start_date': datetime(2020, 1, 1)
}

def _training_model(ti):
    accuracy = uniform(0.1, 10.0)
    print(f'model\'s accuracy: {accuracy}')
    ti.xcom_push(key='model_accuracy', value=accuracy)


def _choose_best_model(ti):
    print('choose best model')
    accuracies = ti.xcom_pull(key='model_accuracy', task_ids=[
        'processing_tasks.training_model_a',
        'processing_tasks.training_model_b',
        'processing_tasks.training_model_c'
    ])
    print(accuracies)

with DAG('xcom_dag', schedule_interval='@daily', default_args=default_args, catchup=False) as dag:

    downloading_data = BashOperator(
        task_id='downloading_data',
        bash_command='sleep 3',
        do_xcom_push=False
    )

    with TaskGroup('processing_tasks') as processing_tasks:
        training_model_a = PythonOperator(
            task_id='training_model_a',
            python_callable=_training_model
        )

        training_model_b = PythonOperator(
            task_id='training_model_b',
            python_callable=_training_model
        )

        training_model_c = PythonOperator(
            task_id='training_model_c',
            python_callable=_training_model
        )

    choose_model = PythonOperator(
        task_id='task_4',
        python_callable=_choose_best_model
    )

    downloading_data >> processing_tasks >> choose_model
```





## Trigger_rule (monitor task)

可选参数

```
all_success: (default) all parents have succeeded

all_failed: all parents are in a failed or upstream_failed state

all_done: all parents are done with their execution

one_failed: fires as soon as at least one parent has failed, it does not wait for all parents to be done

one_success: fires as soon as at least one parent succeeds, it does not wait for all parents to be done

none_failed: all parents have not failed (failed or upstream_failed) i.e. all parents have succeeded or been skipped

none_failed_or_skipped: all parents have not failed (failed or upstream_failed) and at least one parent has succeeded.

none_skipped: no parent is in a skipped state, i.e. all parents are in a success, failed, or upstream_failed state

dummy: dependencies are just for show, trigger at will
```



```
from airflow import DAG
from airflow.operators.bash import BashOperator

from datetime import datetime

default_args = {
    'start_date': datetime(2021, 2, 8)
}

with DAG('trigger_rule', schedule_interval='@daily', default_args=default_args, catchup=False) as dag:
    task_1 = BashOperator(
        task_id='task_1',
        bash_command='exit 0',
        do_xcom_push=False
    )

    task_2 = BashOperator(
        task_id='task_2',
        bash_command='exit 1',
        do_xcom_push=False
    )

    task_3 = BashOperator(
        task_id='task_3',
        bash_command='exit 0',
        do_xcom_push=False,
        trigger_rule='one_failed'
    )
    
    [task_1, task_2] >> task_3
```





## args

catchup 解决loop executor 问题



depends_on_past 解决task 乱序的问题





# plugins

an extension to allow users to easily extend Airflow with various custom hooks, operators, sensors, macros, and web views.

Airflow has a simple plugin manager built-in that can integrate external features to its core by simply dropping files in your `$AIRFLOW_HOME/plugins` folder



The python modules in the `plugins` folder get imported, and **macros** and web **views** get integrated to Airflow's main collections and become available for use.

This means that if you make any changes to plugins and you want the webserver or scheduler to use that new code you will need to restart those processes.

By default, task execution will use forking to avoid the slow down of having to create a whole new python interpreter and re-parse all of the Airflow code and start up routines -- this is a big benefit for shorter running tasks. This does mean that if you use plugins in your tasks, and want them to update you will either need to restart the worker (if using CeleryExecutor) or scheduler (Local or Sequential executors). The other option is you can accept the speed hit at start up set the `core.execute_tasks_new_python_interpreter` config setting to True, resulting in launching a whole new python interpreter for tasks.

(Modules only imported by DAG files on the other hand do not suffer this problem, as DAG files are not loaded/parsed in any long-running Airflow process.)



## elasticsearch



### amazon linux installation

```
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```



```
cat > /etc/yum.repos.d/es.repo <<EOF
[elasticsearch]
name=Elasticsearch repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=0
autorefresh=1
type=rpm-md
EOF
```



```
sudo yum install -y --enablerepo=elasticsearch elasticsearch

systemctl start elasticsearch
```



Install in airflow PATH

```
pip install elasticsearch==7.10.1
```



test ES if it works

```
# curl -X GET 'http://localhost:9200'
{
  "name" : "ip-172-31-47-244.ec2.internal",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "Td05yfJyT1qoSj3guK_gCw",
  "version" : {
    "number" : "7.10.2",
    "build_flavor" : "default",
    "build_type" : "rpm",
    "build_hash" : "747e1cc71def077253878a59143c1f785afa92b9",
    "build_date" : "2021-01-13T00:42:12.435326Z",
    "build_snapshot" : false,
    "lucene_version" : "8.7.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```



### elasticsearch_plugin



`airflow/plugins/elasticsearch_plugin/hooks/elastic_hook.py`

```
from airflow.hooks.base import BaseHook

from elasticsearch import Elasticsearch

class ElasticHook(BaseHook):

    def __init__(self, conn_id='elasticsearch_default', *args, **kwargs):
        super().__init__(*args, **kwargs)
        conn = self.get_connection(conn_id)

        conn_config = {}
        hosts = []

        if conn.host:
            hosts = conn.host.split(',')

        if conn.port:
            conn_config['port'] = int(conn.port)
        
        if conn.login:
            conn_config['http_auth'] = (conn.login, conn.password)

        self.es = Elasticsearch(hosts, **conn_config)
        self.index = conn.schema

    def info(self):
        return self.es.info()

    def set_index(self, index):
        self.index = index

    def add_doc(self, index, doc_type, doc):
        self.set_index(index)
        res = self.es.index(index=index, doc_type=doc_type, doc=doc)
        return res

```



`airflow/dags/elasticsearch_dag.py`

```
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime
from elasticsearch_plugin.hooks.elastic_hook import ElasticHook


default_args = {
    'start_date': datetime(2021, 2, 8)
}

def _print_es_info():
    hook = ElasticHook()
    print(hook.info())


with DAG('elasticsearch_dag', schedule_interval='@daily', default_args=default_args, catchup=False) as dag:

    print_es_info = PythonOperator(
        task_id='print_es_info',
        python_callable=_print_es_info
    )

```



# Pools

concurrency limit configuration for a set of Airflow tasks.





# installation



## Amazon linux 

```
yum -y install libffi-devel
```

```
# but you can lay foundation somewhere else if you prefer
# (optional)
export AIRFLOW_HOME=~/airflow

# install from pypi using pip
pip install apache-airflow

# initialize the database
airflow db init

airflow users create \
    --username rxu \
    --password 1234@abcd \
    --firstname Rick \
    --lastname Xu \
    --role Admin \
    --email rickxu1989@gmail.com

# start the web server, default port is 8080
airflow webserver --port 8080

# start the scheduler
# open a new terminal or else run webserver with ``-D`` option to run it as a daemon
airflow scheduler

# visit localhost:8080 in the browser and use the admin account you just
# created to login. Enable the example_bash_operator dag in the home page
```





## docker

# airflow.cfg



## sql_alchemy_conn

After setting up Postgres

```
pip install 'apache-airflow[postgres]'
```



change airflow.cfg

```
sql_alchemy_conn = postgresql+psycopg2://postgres:postgres@127.0.0.1/postgres
```



check the connectivity 

```
# airflow db check
[2021-02-06 13:57:51,442] {cli_action_loggers.py:105} WARNING - Failed to log action with (psycopg2.errors.UndefinedTable) relation "log" does not exist
LINE 1: INSERT INTO log (dttm, dag_id, task_id, event, execution_dat...
                    ^

[SQL: INSERT INTO log (dttm, dag_id, task_id, event, execution_date, owner, extra) VALUES (%(dttm)s, %(dag_id)s, %(task_id)s, %(event)s, %(execution_date)s, %(owner)s, %(extra)s) RETURNING log.id]
[parameters: {'dttm': datetime.datetime(2021, 2, 6, 13, 57, 51, 430402, tzinfo=Timezone('UTC')), 'dag_id': None, 'task_id': None, 'event': 'cli_check', 'execution_date': None, 'owner': 'root', 'extra': '{"host_name": "ip-172-31-38-91.ec2.internal", "full_command": "[\'/root/.pyenv/versions/v3.9.0/bin/airflow\', \'db\', \'check\']"}'}]
(Background on this error at: http://sqlalche.me/e/13/f405)
[2021-02-06 13:57:51,443] {db.py:756} INFO - Connection successful.
```



re-init airflow db

```
airflow db init
```



create admin user rxu

```
# airflow users create -u rxu -p 1234@abcd -r Admin -f Rick -l Xu -e rickxu1989@gmail.com 
[2021-02-06 14:19:54,053] {manager.py:727} WARNING - No user yet created, use flask fab command to do it.
Admin user rxu created
```





## executor

```
# The executor class that airflow should use. Choices include
# ``SequentialExecutor``, ``LocalExecutor``, ``CeleryExecutor``, ``DaskExecutor``,
# ``KubernetesExecutor``, ``CeleryKubernetesExecutor`` or the
# full import path to the class when using a custom executor.
```



```
executor = LocalExecutor
```





### celery

```
pip install apache-airflow[celery]
```



install redis-server and then

```
executor = CeleryExecutor
broker_url = redis://127.0.0.1:6379/0
result_backend = db+postgresql://postgres:postgres@localhost/postgres
```

```
pip install apache-airflow[redis]
```



```
# airflow celery flower
[2021-02-06 15:30:00,569] {command.py:135} INFO - Visit me at http://0.0.0.0:5555
[2021-02-06 15:30:00,589] {command.py:142} INFO - Broker: redis://127.0.0.1:6379/0
[2021-02-06 15:30:00,592] {command.py:143} INFO - Registered tasks: 
['airflow.executors.celery_executor.execute_command',
 'celery.accumulate',
 'celery.backend_cleanup',
 'celery.chain',
 'celery.chord',
 'celery.chord_unlock',
 'celery.chunks',
 'celery.group',
 'celery.map',
 'celery.starmap']
[2021-02-06 15:30:00,607] {mixins.py:229} INFO - Connected to redis://127.0.0.1:6379/0
[2021-02-06 15:30:01,766] {inspector.py:42} WARNING - Inspect method active failed
[2021-02-06 15:30:01,768] {inspector.py:42} WARNING - Inspect method stats failed
[2021-02-06 15:30:01,769] {inspector.py:42} WARNING - Inspect method scheduled failed
[2021-02-06 15:30:01,792] {inspector.py:42} WARNING - Inspect method registered failed
[2021-02-06 15:30:01,824] {inspector.py:42} WARNING - Inspect method active_queues failed
[2021-02-06 15:30:02,796] {inspector.py:42} WARNING - Inspect method reserved failed
[2021-02-06 15:30:02,798] {inspector.py:42} WARNING - Inspect method conf failed
[2021-02-06 15:30:02,808] {inspector.py:42} WARNING - Inspect method revoked failed
```



enable celery worker

```
airflow celery worker
```

Go to web browser with 5555 port, there is a worker at there





## parallelism

```
# The amount of parallelism as a setting to the executor. This defines
# the max number of task instances that should run simultaneously
# on this airflow installation
parallelism = 32
```



## dag_concurrency

```
# The number of task instances allowed to run concurrently by the scheduler
# in one DAG. Can be overridden by ``concurrency`` on DAG level.
dag_concurrency = 16
```





## load_examples

```
# Whether to load the DAG examples that ship with Airflow. It's good to
# get started, but you probably want to set this to ``False`` in a production
# environment
load_examples = True
```



# airflow cli



## config



### get-value

get specific config value in airflow.cfg

```
# airflow config get-value core sql_alchemy_conn
sqlite:////root/airflow/airflow.db
```



### list

list config of airflow.cfg





# AWS Managed Apache Airflow

![image-20210223122433221](airflow.assets/image-20210223122433221.png)

the S3 bucket and the folder to load my **DAG code**. The bucket name must start with `airflow-`

Optionally, I can specify a plugins file and a requirements file:

- The **plugins** file is a ZIP file containing the plugins used by my DAGs.
- The **requirements** file describes the Python dependencies to run my DAGs.









## networking

![image-20210223122252158](airflow.assets/image-20210223122252158.png)



```
Description:  This template deploys a VPC, with a pair of public and private subnets spread
  across two Availability Zones. It deploys an internet gateway, with a default
  route on the public subnets. It deploys a pair of NAT gateways (one in each AZ),
  and default routes for them in the private subnets.

Parameters:
  EnvironmentName:
    Description: An environment name that is prefixed to resource names
    Type: String
    Default: mwaa-

  VpcCIDR:
    Description: Please enter the IP range (CIDR notation) for this VPC
    Type: String
    Default: 10.192.0.0/16

  PublicSubnet1CIDR:
    Description: Please enter the IP range (CIDR notation) for the public subnet in the first Availability Zone
    Type: String
    Default: 10.192.10.0/24

  PublicSubnet2CIDR:
    Description: Please enter the IP range (CIDR notation) for the public subnet in the second Availability Zone
    Type: String
    Default: 10.192.11.0/24

  PrivateSubnet1CIDR:
    Description: Please enter the IP range (CIDR notation) for the private subnet in the first Availability Zone
    Type: String
    Default: 10.192.20.0/24

  PrivateSubnet2CIDR:
    Description: Please enter the IP range (CIDR notation) for the private subnet in the second Availability Zone
    Type: String
    Default: 10.192.21.0/24

Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: !Ref VpcCIDR
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: !Ref EnvironmentName

  InternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          Value: !Ref EnvironmentName

  InternetGatewayAttachment:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      InternetGatewayId: !Ref InternetGateway
      VpcId: !Ref VPC

  PublicSubnet1:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 0, !GetAZs '' ]
      CidrBlock: !Ref PublicSubnet1CIDR
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName} Public Subnet (AZ1)

  PublicSubnet2:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 1, !GetAZs  '' ]
      CidrBlock: !Ref PublicSubnet2CIDR
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName} Public Subnet (AZ2)

  PrivateSubnet1:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 0, !GetAZs  '' ]
      CidrBlock: !Ref PrivateSubnet1CIDR
      MapPublicIpOnLaunch: false
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName} Private Subnet (AZ1)

  PrivateSubnet2:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 1, !GetAZs  '' ]
      CidrBlock: !Ref PrivateSubnet2CIDR
      MapPublicIpOnLaunch: false
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName} Private Subnet (AZ2)

  NatGateway1EIP:
    Type: AWS::EC2::EIP
    DependsOn: InternetGatewayAttachment
    Properties:
      Domain: vpc

  NatGateway2EIP:
    Type: AWS::EC2::EIP
    DependsOn: InternetGatewayAttachment
    Properties:
      Domain: vpc

  NatGateway1:
    Type: AWS::EC2::NatGateway
    Properties:
      AllocationId: !GetAtt NatGateway1EIP.AllocationId
      SubnetId: !Ref PublicSubnet1

  NatGateway2:
    Type: AWS::EC2::NatGateway
    Properties:
      AllocationId: !GetAtt NatGateway2EIP.AllocationId
      SubnetId: !Ref PublicSubnet2

  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName} Public Routes

  DefaultPublicRoute:
    Type: AWS::EC2::Route
    DependsOn: InternetGatewayAttachment
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway

  PublicSubnet1RouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref PublicRouteTable
      SubnetId: !Ref PublicSubnet1

  PublicSubnet2RouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref PublicRouteTable
      SubnetId: !Ref PublicSubnet2


  PrivateRouteTable1:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName} Private Routes (AZ1)

  DefaultPrivateRoute1:
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref PrivateRouteTable1
      DestinationCidrBlock: 0.0.0.0/0
      NatGatewayId: !Ref NatGateway1

  PrivateSubnet1RouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref PrivateRouteTable1
      SubnetId: !Ref PrivateSubnet1

  PrivateRouteTable2:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName} Private Routes (AZ2)

  DefaultPrivateRoute2:
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref PrivateRouteTable2
      DestinationCidrBlock: 0.0.0.0/0
      NatGatewayId: !Ref NatGateway2

  PrivateSubnet2RouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref PrivateRouteTable2
      SubnetId: !Ref PrivateSubnet2

  NoIngressSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupName: "no-ingress-sg"
      GroupDescription: "Security group with no ingress rule"
      VpcId: !Ref VPC

Outputs:
  VPC:
    Description: A reference to the created VPC
    Value: !Ref VPC

  PublicSubnets:
    Description: A list of the public subnets
    Value: !Join [ ",", [ !Ref PublicSubnet1, !Ref PublicSubnet2 ]]

  PrivateSubnets:
    Description: A list of the private subnets
    Value: !Join [ ",", [ !Ref PrivateSubnet1, !Ref PrivateSubnet2 ]]

  PublicSubnet1:
    Description: A reference to the public subnet in the 1st Availability Zone
    Value: !Ref PublicSubnet1

  PublicSubnet2:
    Description: A reference to the public subnet in the 2nd Availability Zone
    Value: !Ref PublicSubnet2

  PrivateSubnet1:
    Description: A reference to the private subnet in the 1st Availability Zone
    Value: !Ref PrivateSubnet1

  PrivateSubnet2:
    Description: A reference to the private subnet in the 2nd Availability Zone
    Value: !Ref PrivateSubnet2

  NoIngressSecurityGroup:
    Description: Security group with no ingress rule
    Value: !Ref NoIngressSecurityGroup
```





## launch via Lambda

```
import boto3

client = boto3.client('mwaa')

def lambda_handler(event, context):
    response = client.create_environment(
		    AirflowVersion='2.0.2',
        DagS3Path='s3://rxu-test-airflow-by-lambda/dags',
        ExecutionRoleArn='arn:aws:iam::111:role/service-role/AmazonMWAA-aa-poc-mwaa-airflow-xx1X81',
        LoggingConfiguration={
            'DagProcessingLogs': {
                'Enabled': True,
                'LogLevel': 'DEBUG'
            },
            'SchedulerLogs': {
                'Enabled': True,
                'LogLevel': 'DEBUG'
            },
            'TaskLogs': {
                'Enabled': True,
                'LogLevel': 'DEBUG'
            },
            'WebserverLogs': {
                'Enabled': True,
                'LogLevel': 'DEBUG'
            },
            'WorkerLogs': {
                'Enabled': True,
                'LogLevel': 'DEBUG'
            }
        },
        Name='rxu_test_airflow_env_by_lambda',
        NetworkConfiguration={
            'SecurityGroupIds': [
                'sg-11',
            ],
            'SubnetIds': [
                'subnet-11',
                'subnet-11'
            ]
        },
        SourceBucketArn='arn:aws:s3:::rxu-test-airflow-by-lambda'
    )
    print('success', response)
```



## MWAA local runner

https://github.com/aws/aws-mwaa-local-runner





# Appendix

https://eng.lyft.com/running-apache-airflow-at-lyft-6e53bb8fccff



https://docs.aws.amazon.com/mwaa/latest/userguide/vpc-create.html



https://www.astronomer.io/guides/airflow-databricks

