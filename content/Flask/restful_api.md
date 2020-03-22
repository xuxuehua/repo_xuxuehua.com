---
title: "restful_api"
date: 2019-08-03 16:32
---
[TOC]



# Restful API

## 优点

强制输入输出为json，可读性很强

指定版本号，可以放在header的key value 中，或者query里面指定版本号





## 缺点

接口力度比较粗

造成HTTP请求数量大量增加





## 内部开发API

内部调用的API



## 开放性的API

可以公网的访问的API，如Github 







# flask_restful



## HelloWorld

```
from flask import Flask
from flask_restful import Api, Resource

app = Flask(__name__)
api = Api(app)

class UserAPI(Resource):
    def get(self, id):
        pass

    def put(self, id):
        pass

    def delete(self, id):
        pass

api.add_resource(UserAPI, '/users/<int:id>', endpoint = 'user')
```



## RequestParser

This class works in a similar way as `argparse` for command line arguments.

for handling multi-arguments 

```
from flask_restful import reqparse

class TaskListAPI(Resource):
    def __init__(self):
        self.reqparse = reqparse.RequestParser()
        self.reqparse.add_argument('title', type = str, required = True,
            help = 'No task title provided', location = 'json')
        self.reqparse.add_argument('description', type = str, default = "", location = 'json')
        super(TaskListAPI, self).__init__()

    def put(self, id):
        task = filter(lambda t: t['id'] == id, tasks)
        if len(task) == 0:
            abort(404)
        task = task[0]
        args = self.reqparse.parse_args()
        for k, v in args.iteritems():
            if v != None:
                task[k] = v
        return jsonify( { 'task': make_public_task(task) } )

class TaskAPI(Resource):
    def __init__(self):
        self.reqparse = reqparse.RequestParser()
        self.reqparse.add_argument('title', type = str, location = 'json')
        self.reqparse.add_argument('description', type = str, location = 'json')
        self.reqparse.add_argument('done', type = bool, location = 'json')
        super(TaskAPI, self).__init__()

    # ...
```

> the `location` optional argument must be set to indicate that the fields are coming in `request.json`



## Responses

Flask-RESTful automatically handles the conversion to JSON, so instead of this

```
return jsonify( { 'task': make_public_task(task) } )
```

or 

```
return { 'task': make_public_task(task) }
```

or

```
return { 'task': make_public_task(task) }, 201
```



### marshal 

```
task_fields = {
    'title': fields.String,
    'description': fields.String,
    'done': fields.Boolean
}
class UserAPI(Resource):
    def get(self, id):
        task = {'a': 100, 'b': 'foo', 'c': id}
        return {'task': marshal(task, task_fields)}
        
        
>>>
{
    "task": {
        "title": null,
        "description": null,
        "done": null
    }
}
```

> 



## example

```
#!flask/bin/python
import six
from flask import Flask, jsonify, abort, request, make_response, url_for
from flask_httpauth import HTTPBasicAuth

app = Flask(__name__, static_url_path="")
auth = HTTPBasicAuth()


@auth.get_password
def get_password(username):
    if username == 'miguel':
        return 'python'
    return None


@auth.error_handler
def unauthorized():
    # return 403 instead of 401 to prevent browsers from displaying the default
    # auth dialog
    return make_response(jsonify({'error': 'Unauthorized access'}), 403)


@app.errorhandler(400)
def bad_request(error):
    return make_response(jsonify({'error': 'Bad request'}), 400)


@app.errorhandler(404)
def not_found(error):
    return make_response(jsonify({'error': 'Not found'}), 404)


tasks = [
    {
        'id': 1,
        'title': u'Buy groceries',
        'description': u'Milk, Cheese, Pizza, Fruit, Tylenol',
        'done': False
    },
    {
        'id': 2,
        'title': u'Learn Python',
        'description': u'Need to find a good Python tutorial on the web',
        'done': False
    }
]


def make_public_task(task):
    new_task = {}
    for field in task:
        if field == 'id':
            new_task['uri'] = url_for('get_task', task_id=task['id'],
                                      _external=True)
        else:
            new_task[field] = task[field]
    return new_task


@app.route('/todo/api/v1.0/tasks', methods=['GET'])
@auth.login_required
def get_tasks():
    return jsonify({'tasks': [make_public_task(task) for task in tasks]})


@app.route('/todo/api/v1.0/tasks/<int:task_id>', methods=['GET'])
@auth.login_required
def get_task(task_id):
    task = [task for task in tasks if task['id'] == task_id]
    if len(task) == 0:
        abort(404)
    return jsonify({'task': make_public_task(task[0])})


@app.route('/todo/api/v1.0/tasks', methods=['POST'])
@auth.login_required
def create_task():
    if not request.json or 'title' not in request.json:
        abort(400)
    task = {
        'id': tasks[-1]['id'] + 1 if len(tasks) > 0 else 1,
        'title': request.json['title'],
        'description': request.json.get('description', ""),
        'done': False
    }
    tasks.append(task)
    return jsonify({'task': make_public_task(task)}), 201


@app.route('/todo/api/v1.0/tasks/<int:task_id>', methods=['PUT'])
@auth.login_required
def update_task(task_id):
    task = [task for task in tasks if task['id'] == task_id]
    if len(task) == 0:
        abort(404)
    if not request.json:
        abort(400)
    if 'title' in request.json and \
            not isinstance(request.json['title'], six.string_types):
        abort(400)
    if 'description' in request.json and \
            not isinstance(request.json['description'], six.string_types):
        abort(400)
    if 'done' in request.json and type(request.json['done']) is not bool:
        abort(400)
    task[0]['title'] = request.json.get('title', task[0]['title'])
    task[0]['description'] = request.json.get('description',
                                              task[0]['description'])
    task[0]['done'] = request.json.get('done', task[0]['done'])
    return jsonify({'task': make_public_task(task[0])})


@app.route('/todo/api/v1.0/tasks/<int:task_id>', methods=['DELETE'])
@auth.login_required
def delete_task(task_id):
    task = [task for task in tasks if task['id'] == task_id]
    if len(task) == 0:
        abort(404)
    tasks.remove(task[0])
    return jsonify({'result': True})


if __name__ == '__main__':
    app.run(debug=True)

```





# Authentication



## Database 

```
class User(db.Model):
    __tablename__ = 'users'
    id = db.Column(db.Integer, primary_key = True)
    username = db.Column(db.String(32), index = True)
    password_hash = db.Column(db.String(128))
```



## Password Hashing

