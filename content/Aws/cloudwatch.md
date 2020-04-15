---
title: "cloudwatch"
date: 2019-06-25 09:59
---
[TOC]



# CloudWatch



## Supported Services

![image-20200316144155634](cloudwatch.assets/image-20200316144155634.png)





# CloudWatch Insight

If `@message` is

```
I, [2018-12-11T13:20:27] INFO -- : {"method":"GET"}
```

Then you extract the fields like so:

```
fields @timestamp, @message
| parse "I, [*T*] INFO -- : {"method":"*"}" as @date, @time, @method
| filter method=GET
| sort @timestamp desc
| limit 20
```





## message content 

```
{"message": "Exception on /bdp/cluster/yarn/submit_spark_job/BDP-PROD-BIGDATA-ANA/ios_report/qgong_sp [POST]", "exc_info": "Traceback (most recent call last):\n File \"/opt/bdp/venv/lib/python3.7/site-packages/flask/app.py\", line 1813, in full_dispatch_request\n rv = self.dispatch_request()\n File \"/opt/bdp/venv/lib/python3.7/site-packages/flask/app.py\", line 1799, in dispatch_request\n return self.view_functions[rule.endpoint](**req.view_args)\n File \"/opt/bdp/venv/lib/python3.7/site-packages/flask_restful/__init__.py\", line 458, in wrapper\n resp = resource(*args, **kwargs)\n File \"/opt/bdp/venv/lib/python3.7/site-packages/flask/views.py\", line 88, in view\n return self.dispatch_request(*args, **kwargs)\n File \"/opt/bdp/venv/lib/python3.7/site-packages/flask_restful/__init__.py\", line 573, in dispatch_request\n resp = meth(*args, **kwargs)\n File \"/opt/bdp/bdp_service/api/views/base.py\", line 145, in post\n return self.__process_request__(self.process_post, role_name, **path_kwargs)\n File \"/opt/bdp/bdp_service/api/views/base.py\", line 142, in __process_request__\n return proc(auth_token, username, role_name, **request_args)\n File \"/opt/bdp/bdp_service/api/views/yarn_application.py\", line 148, in process_post\n if result.get(\"msg\", None):\nAttributeError: 'str' object has no attribute 'get'"}
```



```
fields @timestamp, @message
| parse @message '{"message": *' as @content
| filter @content like 'Traceback'
| sort @timestamp desc
| limit 10
```



## like or

```
fields @timestamp, @message
| filter (@message like 'error' or @message like 'exception')
| sort @timestamp desc
```

