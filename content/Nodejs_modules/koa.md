---
title: "koa"
date: 2018-08-01 16:34
---

[TOC]

# koa框架

koa是Express的下一代基于Node.js的web框架，目前有1.x和2.0两个版本。

出于兼容性考虑，目前koa2仍支持generator的写法，但下一个版本将会去掉。



## 安装koa

Koa 必须使用 7.6 以上的版本。如果你的版本低于这个要求，就要先升级 Node。

 



### npm

`npm install koa@2.0.0`



### package.json

目录下创建一个`package.json`，这个文件描述了我们的`hello-koa`工程会用到哪些包。完整的文件内容如下：

```
{
    "name": "hello-koa2",
    "version": "1.0.0",
    "description": "Hello Koa 2 example with async",
    "main": "app.js",
    "scripts": {
        "start": "node app.js"
    },
    "keywords": [
        "koa",
        "async"
    ],
    "author": "Michael Liao",
    "license": "Apache-2.0",
    "repository": {
        "type": "git",
        "url": "https://github.com/michaelliao/learn-javascript.git"
    },
    "dependencies": {
        "koa": "2.0.0"
    }
}
```

其中，`dependencies`描述了我们的工程依赖的包以及版本号。其他字段均用来描述项目信息，可任意填写。

然后，我们在`hello-koa`目录下执行`npm install`就可以把所需包以及依赖包一次性全部装好：

```
C:\...\hello-koa> npm install
```





## 使用Koa

### 最基本http页面

http_testing.js

```
const Koa = require('koa');

const app = new Koa();

app.listen(3000);
```

```
node http_testing.js 运行
```



### 含context的页面

Koa 提供一个 Context 对象，表示一次对话的上下文（包括 HTTP 请求和 HTTP 回复）。通过加工这个对象，就可以控制返回给用户的内容。

```
const Koa = require('koa');

const app = new Koa();

const main = ctx => {
    ctx.response.body = 'Hello World';
}

app.use(main);
app.listen(3000);
```



### HTTP Response 的类型

Koa 默认的返回类型是`text/plain`，如果想返回其他类型的内容，可以先用`ctx.request.accepts`判断一下，客户端希望接受什么数据（根据 HTTP Request 的`Accept`字段），然后使用`ctx.response.type`指定返回类型。

```
const Koa = require('koa');

const app = new Koa();

const main = ctx => {
    if (ctx.request.accepts('xml')) {
        ctx.response.type = 'xml';
        ctx.response.body = '<data>Hello World</data>';
    } else if (ctx.request.accepts('json')) {
        ctx.response.type = 'json';
        ctx.response.body = {data: 'Hello World'};
    } else if (ctx.request.accepts('html')) {
        ctx.response.type = 'html';
        ctx.response.body = '<p>Hello World</p>';
    } else {
        ctx.response.type = 'text';
        ctx.response.body = 'Hello World';
    }
}

app.use(main);
app.listen(3000);
```



这样会返回一个xml文档

![img](/var/folders/rz/7zd4wvf90070rc96bxfc_xf13wzxml/T/abnerworks.Typora/image-20180802114918884.png)

### 网页模板

实际开发中，返回给用户的网页往往都写成模板文件。我们可以让 Koa 先读取模板文件，然后将这个模板返回给用户

./demos/template.html

```
<!doctype html>

<html lang="en">
<head>
  <meta charset="utf-8">

  <title>The HTML5 Herald</title>
  <meta name="description" content="HTML5 Template">
  <meta name="author" content="xxx">
</head>

<body>
  <h1>Hello World</h1>
</body>
</html>
```



http_testing.js

```
const fs = require('fs');
const Koa = require('koa');
const app = new Koa();

const main = ctx => {
    ctx.response.type = 'html';
    ctx.response.body = fs.createReadStream('./demos/template.html');
};

app.use(main);
app.listen(3000);
```



### 路由

网站一般都有多个页面。通过`ctx.request.path`可以获取用户请求的路径，由此实现简单的路由。

```
const fs = require('fs');
const Koa = require('koa');
const app = new Koa();

const main = ctx => {
    if (ctx.request.path !== '/') {
        ctx.response.type = 'html';
        ctx.response.body = '<a href="/">Index Page</a>';
    } else {
        ctx.response.body = 'Hello World';
    }
};

app.use(main);
app.listen(3000);
```



访问其他URL会显示index page， 并可以跳转

![img](https://cdn.pbrd.co/images/HxgdMnb.png)

#### koa-route 模块

启用package.json, 安装koa-route

```
{
  "name": "koa_ruanyifeng",
  "version": "0.0.1",
  "dependencies": {
    "koa-route": "^3.2.0"
  }
}
```



根路径`/`的处理函数是`main`，`/about`路径的处理函数是`about`

```
const fs = require('fs');
const Koa = require('koa');
const app = new Koa();
const route = require('koa-route');

const about = ctx => {
    ctx.response.type = 'html';
    ctx.response.body = '<a href="/">Index Page</a>';
};

const main = ctx => {
    ctx.response.body = "Hello World";
}

app.use(route.get('/', main));
app.use(route.get('/about', about));

app.listen(3000);
```



####  koa-router

为了处理URL，我们需要引入`koa-router`这个middleware，让它负责处理URL映射。

我们把上一节的`hello-koa`工程复制一份，重命名为`url-koa`。

先在`package.json`中添加依赖项：

```
"koa-router": "7.0.0"
```

然后用`npm install`安装。

接下来，我们修改`app.js`，使用`koa-router`来处理URL：

```
const Koa = require('koa');

// 注意require('koa-router')返回的是函数:
const router = require('koa-router')();

const app = new Koa();

// log request URL:
app.use(async (ctx, next) => {
    console.log(`Process ${ctx.request.method} ${ctx.request.url}...`);
    await next();
});

// add url-route:
router.get('/hello/:name', async (ctx, next) => {
    var name = ctx.params.name;
    ctx.response.body = `<h1>Hello, ${name}!</h1>`;
});

router.get('/', async (ctx, next) => {
    ctx.response.body = '<h1>Index</h1>';
});

// add router middleware:
app.use(router.routes());

app.listen(3000);
console.log('app started at port 3000...');
```

注意导入`koa-router`的语句最后的`()`是函数调用：

```
const router = require('koa-router')();
```

相当于：

```
const fn_router = require('koa-router');
const router = fn_router();
```

然后，我们使用`router.get('/path', async fn)`来注册一个GET请求。可以在请求路径中使用带变量的`/hello/:name`，变量可以通过`ctx.params.name`访问。

再运行`app.js`，我们就可以测试不同的URL：

输入首页：<http://localhost:3000/>



### 静态资源

如果网站提供静态资源（图片、字体、样式表、脚本......），为它们一个个写路由就很麻烦，也没必要。[`koa-static`](https://www.npmjs.com/package/koa-static)模块封装了这部分的请求。



package.json

```
{
  "name": "koa_ruanyifeng",
  "version": "0.0.1",
  "dependencies": {
    "koa-route": "^3.2.0",
    "koa-static": "^5.0.0"
  }
}
```



```
const fs = require('fs');
const Koa = require('koa');
const app = new Koa();
const route = require('koa-router');
const path = require('path');
const serve = require('koa-static');

const main = serve(path.join(__dirname));
app.use(main);
app.listen(3000);
```

![img](https://cdn.pbrd.co/images/Hxh7ObeS.png)



### 重定向

有些场合，服务器需要重定向（redirect）访问请求。比如，用户登陆以后，将他重定向到登陆前的页面。`ctx.response.redirect()`方法可以发出一个302跳转，将用户导向另一个路由。



package.json

```
{
  "name": "koa_ruanyifeng",
  "version": "0.0.1",
  "dependencies": {
    "koa-route": "^3.2.0",
    "koa-static": "^5.0.0"
  }
}
```



```
const Koa = require('koa');
const route = require('koa-route');
const app = new Koa();

const redirect = ctx => {
    ctx.response.redirect('/');
};

const main = ctx => {
    ctx.response.body = 'Hello World';
};

app.use(route.get('/', main));
app.use(route.get('/redirect', redirect));

app.use(main);
app.listen(3000);
```



访问 http://127.0.0.1:3000/redirect ，浏览器会将用户导向根路由





## 中间件	

Koa 的最大特色，也是最重要的一个设计，就是中间件（middleware）。



### 中间件实现



```
app.use(async (ctx, next) => {
    await next();
    ctx.response.type = 'text/html';
    ctx.response.body = '<h1>Hello, koa2!</h1>';
});
```

koa把很多async函数组成一个处理链，每个async函数都可以做一些自己的事情，然后用`await next()`来调用下一个async函数。我们把每个async函数称为middleware

如果一个middleware没有调用`await next()`，会怎么办？答案是后续的middleware将不再执行了。这种情况也很常见，例如，一个检测用户权限的middleware可以决定是否继续处理请求，还是直接返回403错误：

```
app.use(async (ctx, next) => {
    if (await checkUserPermission(ctx)) {
        await next();
    } else {
        ctx.response.status = 403;
    }
});

```



```
const Koa = require('koa');
const route = require('koa-route');
const app = new Koa();

const logger = (ctx, next) => {
    console.log(`${Date.now()} ${ctx.request.method} ${ctx.request.url}`);
};

app.use(logger);
app.listen(3000);
```

访问 http://127.0.0.1:3000 ，命令行窗口会显示与上一个例子相同的日志输出。

```
xhxu-mac:koa_ruanyifeng xhxu$ node http_testing.js
1533203867128 GET /
```





### 中间件栈

多个中间件会形成一个栈结构（middle stack），以"先进后出"（first-in-last-out）的顺序执行。

```
最外层的中间件首先执行。
调用next函数，把执行权交给下一个中间件。
...
最内层的中间件最后执行。
执行结束后，把执行权交回上一层的中间件。
...
最外层的中间件收回执行权之后，执行next函数后面的代码。
```

```
xhxu-mac:koa_ruanyifeng xhxu$ node http_testing.js
>> one
>> two
>> three
<< three
<< two
<< one
```

### 

```
const Koa=require('koa');
const app=new Koa();
app.use(async (ctx, next) => {
    console.log('第一1');
    await next(); // 调用下一个middleware
    console.log('第一2');
});

app.use(async (ctx, next) => {
    console.log('第二1');
    await next(); // 调用下一个middleware
    console.log('第二2');
});

app.use(async (ctx, next) => {
    console.log('第三1');
    await next();
    console.log('第三2');
});
app.listen(3000);

>>>
第一1
第二1
第三1
第三2
第二2
第一2
```





如果中间件内部没有调用`next`函数，那么执行权就不会传递下去。`two`函数里面`next()`这一行注释掉再执行结果

```
xhxu-mac:koa_ruanyifeng xhxu$ node http_testing.js
>> one
>> two
<< two
<< one
```



### 异步中间件

如果有异步操作（比如读取数据库），中间件就必须写成 [async 函数](http://es6.ruanyifeng.com/#docs/async)

package.json

```
{
  "name": "koa_ruanyifeng",
  "version": "0.0.1",
  "dependencies": {
    "koa-route": "^3.2.0",
    "koa-static": "^5.0.0", 
    "fs": "^0.0.1-security"
  }
}
```

```
const Koa = require('koa');
const route = require('koa-route');
const app = new Koa();
const fs = require('fs.promised');

const main = async function (ctx, next) {
    ctx.response.type = 'html';
    ctx.response.body = await fs.readFile('./demos/template.html', 'utf8');
}

app.use(main);
app.listen(3000);
```





![img](https://cdn.pbrd.co/images/HxidP57.png)



### 中间件的合成

[`koa-compose`](https://www.npmjs.com/package/koa-compose)模块可以将多个中间件合成为一个

Package.json

```
{
  "name": "koa_ruanyifeng",
  "version": "0.0.1",
  "dependencies": {
    "fs.promised": "^3.0.0",
    "koa-route": "^3.2.0",
    "koa-static": "^5.0.0",
    "koa-compose": "^4.1.0"
  }
}
```

```
const Koa = require('koa');
const app = new Koa();
const compose = require('koa-compose');

const logger = (ctx, next) => {
    console.log(`${Date.now()} ${ctx.request.method} ${ctx.request.url}`);
};

const main = ctx => {
    ctx.response.body = 'Hello World';
};

const middlewares = compose([logger, main]);

app.use(main);
app.listen(3000);
```



访问 http://127.0.0.1:3000 ，就可以在命令行窗口看到日志信息


```
xhxu-mac:koa_ruanyifeng xhxu$ node http_testing.js
1533206455183 GET /
```





## 错误处理

### 500 error

如果代码运行过程中发生错误，我们需要把错误信息返回给用户。HTTP 协定约定这时要返回500状态码。Koa 提供了`ctx.throw()`方法，用来抛出错误，`ctx.throw(500)`就是抛出500错误。

```
const Koa = require('koa');
const app = new Koa();

const main = ctx => {
    ctx.throw(500);
};

app.use(main);
app.listen(3000);
```



访问 http://127.0.0.1:3000，你会看到一个500错误页"Internal Server Error"

```
xhxu-mac:koa_ruanyifeng xhxu$ node http_testing.js

  InternalServerError: Internal Server Error
```

 

### 404 error 

如果将`ctx.response.status`设置成404，就相当于`ctx.throw(404)`，返回404错误。

```
const Koa = require('koa');
const app = new Koa();

const main = ctx => {
    ctx.response.status = 404;
    ctx.response.body = "Page Not Found";
};

app.use(main);
app.listen(3000);
```

访问 http://127.0.0.1:3000 ，你就看到一个404页面"Page Not Found"。



![img](https://cdn.pbrd.co/images/Hxiph8p.png)

### 处理错误的中间件

为了方便处理错误，最好使用`try...catch`将其捕获。但是，为每个中间件都写`try...catch`太麻烦，我们可以让最外层的中间件，负责所有中间件的错误处理。

```
const Koa = require('koa');
const app = new Koa();

const handler = async (ctx, next) => {
    try {
        await next();
    } catch (err) {
        ctx.response.status = err.statusCode || err.status || 500;
        ctx.response.body = {
            message: err.message
        };
    }
};

const main = ctx => {
    ctx.throw(500);
};

app.use(handler);
app.use(main);
app.listen(3000);
```

访问 http://127.0.0.1:3000 ，你会看到一个500页，里面有报错提示 `{"message":"Internal Server Error"}`

![img](https://cdn.pbrd.co/images/HxirnEC.png)



### error 事件的监听

运行过程中一旦出错，Koa 会触发一个`error`事件。监听这个事件，也可以处理错误。

```
const Koa = require('koa');
const app = new Koa();

const handler = async (ctx, next) => {
    try {
        await next();
    } catch (err) {
        ctx.response.status = err.statusCode || err.status || 500;
        ctx.response.body = {
            message: err.message
        };
    }
};

const main = ctx => {
    ctx.throw(500);
};

app.use(handler);
app.use(main);
app.listen(3000);
```



访问 http://127.0.0.1:3000 ，你会在命令行窗口看到"server error xxx"



### 释放 error 事件

如果错误被`try...catch`捕获，就不会触发`error`事件。这时，必须调用`ctx.app.emit()`，手动释放`error`事件，才能让监听函数生效。

```
const Koa = require('koa');
const app = new Koa();

const handler = async (ctx, next) => {
    try {
        await next();
    } catch (err) {
        ctx.response.status = err.statusCode || err.status || 500;
        ctx.response.type = 'html';
        ctx.response.body = '<p>Something wrong, please contact adminstrator.</p>';
        ctx.app.emit('error', err, ctx);
    }
};

const main = ctx => {
    ctx.throw(500);
};

app.on('error', function (err) {
    console.log('logging error', err.message);
    console.log(err);
});

app.use(handler);
app.use(main);
app.listen(3000);
```

> `main`函数抛出错误，被`handler`函数捕获。`catch`代码块里面使用`ctx.app.emit()`手动释放`error`事件，才能让监听函数监听到。



http://127.0.0.1:3000 ，你会在命令行窗口看到`logging error`



## Web App 的功能

### Cookies

`ctx.cookies`用来读写 Cookie。

```
const Koa = require('koa');
const app = new Koa();

const main = function (ctx) {
    const n = Number(ctx.cookies.get('view') || 0) + 1;
    ctx.cookies.set('view', n);
    ctx.response.body = n + 'views';
}

app.use(main);
app.listen(3000);
```

访问 http://127.0.0.1:3000 ，你会看到`1 views`。刷新一次页面，就变成了`2 views`。再刷新，每次都会计数增加1



### 表单

Web 应用离不开处理表单。本质上，表单就是 POST 方法发送到服务器的键值对。[`koa-body`](https://www.npmjs.com/package/koa-body)模块可以用来从 POST 请求的数据体里面提取键值对。

package.json

```
{
  "name": "koa_ruanyifeng",
  "version": "0.0.1",
  "dependencies": {
    "fs.promised": "^3.0.0",
    "koa-compose": "^4.1.0",
    "koa-route": "^3.2.0",
    "koa-static": "^5.0.0",
    "koa-body": "^4.0.4"
  }
}
```

```
const Koa = require('koa');
const app = new Koa();
const koaBody = require('koa-body');


const main = async function(ctx) {
    const body = ctx.request.body;
    if (!body.name) ctx.throw(400, '.name required');
    ctx.body = {name: body.name};
}

app.use(koaBody());
app.use(main);
app.listen(3000);
```



打开另一个命令行窗口，运行下面的命令。

```
$ curl -X POST --data "name=Jack" 127.0.0.1:3000
{"name":"Jack"}

$ curl -X POST --data "name" 127.0.0.1:3000
name required
```

> 代码使用 POST 方法向服务器发送一个键值对，会被正确解析。如果发送的数据不正确，就会收到错误提示。



### 文件上传 (failed verification)

[`koa-body`](https://www.npmjs.com/package/koa-body)模块还可以用来处理文件上传。

package.json

```
{
  "name": "koa_ruanyifeng",
  "version": "0.0.1",
  "dependencies": {
    "fs": "0.0.1-security",
    "fs.promised": "^3.0.0",
    "koa-body": "^4.0.4",
    "koa-compose": "^4.1.0",
    "koa-route": "^3.2.0",
    "koa-static": "^5.0.0",
    "os": "^0.1.1"
  }
}
```

```
const os = require('os');
const path = require('path');
const Koa = require('koa');
const fs = require('fs');
const koaBody = require('koa-body');

const app = new Koa();

const main = async function(ctx) {
    const tmpdir = os.tmpdir();
    const filePaths = [];
    const files = ctx.request.body.files || {};

    for (let key in files) {
        const file = files[key];
        const filePath = path.join(tmpdir, file.name);
        const reader = fs.createReadStream(filePath);
        const writer = fs.createWriteStream(filePath);
        reader.pipe(writer);
        filePaths.push(filePath);
    }

    ctx.body = filePaths;
};

app.use(koaBody({ multipart: true }));
app.use(main);
app.listen(3000);
```

打开另一个命令行窗口，运行下面的命令，上传一个文件。注意，`/path/to/file`要更换为真实的文件路径。

```
$ curl --form upload=@/path/to/file http://127.0.0.1:3000
["/tmp/file"]
```





## Nunjucks 模板引擎

Nunjucks是Mozilla开发的一个纯JavaScript编写的模板引擎，既可以用在Node环境下，又可以运行在浏览器端。但是，主要还是运行在Node环境下，因为浏览器端有更好的模板解决方案，例如MVVM框架。



模板引擎就是基于模板配合数据构造出字符串输出的一个组件。比如下面的函数就是一个模板引擎：

```
function examResult (data) {
    return `${data.name}同学一年级期末考试语文${data.chinese}分，数学${data.math}分，位于年级第${data.ranking}名。`
}
```

如果我们输入数据如下：

```
examResult({
    name: '小明',
    chinese: 78,
    math: 87,
    ranking: 999
});
```

该模板引擎把模板字符串里面对应的变量替换以后，就可以得到以下输出：

```
小明同学一年级期末考试语文78分，数学87分，位于年级第999名。
```



一个模板引擎是非常简单的，因为本质上我们只需要构造这样一个函数：

```
function render(view, model) {
    // TODO:...
}
```

其中，`view`是模板的名称（又称为视图），因为可能存在多个模板，需要选择其中一个。`model`就是数据，在JavaScript中，它就是一个简单的Object。`render`函数返回一个字符串，就是模板的输出。



### 模板引擎工程

`use-nunjucks`的VS Code工程结构如下：

```
use-nunjucks/
|
+- .vscode/
|  |
|  +- launch.json <-- VSCode 配置文件
|
+- views/
|  |
|  +- hello.html <-- HTML模板文件
|
+- app.js <-- 入口js
|
+- package.json <-- 项目描述文件
|
+- node_modules/ <-- npm安装的所有依赖包
```

其中，模板文件存放在`views`目录中。



* package.json

```
{
  "name": "use-nunjucks",
  "version": "0.0.1",
  "dependencies": {
    "nunjucks": "^3.1.3"
  }
}
```



* app.js

```
const nunjucks = require('nunjucks');

function createEnv(path, opts) {
    var
        autoescape = opts.autoescape === undefined ? true : opts.autoescape,
        noCache = opts.noCache || false,
        watch = opts.watch || false,
        throwOnUndefined = opts.throwOnUndefined || false,
        env = new nunjucks.Environment(
            new nunjucks.FileSystemLoader('views', {
                noCache: noCache,
                watch: watch,
            }), {
                autoescape: autoescape,
                throwOnUndefined: throwOnUndefined
            });
    if (opts.filters) {
        for (var f in opts.filters) {
            env.addFilter(f, opts.filters[f]);
        }
    }
    return env;
}

var env = createEnv('views', {
    watch: true,
    filters: {
        hex: function (n) {
            return '0x' + n.toString(16);
        }
    }
});
```
我们就可以用下面的代码来渲染这个模板：

```
var s = env.render('hello.html', { name: '小明' });
console.log(s);
```

获得输出如下：

```
<h1>Hello 小明</h1>
```



定义一个基本的网页框架`base.html`：

```
<html><body>
{% block header %} <h3>Unnamed</h3> {% endblock %}
{% block body %} <div>No body</div> {% endblock %}
{% block footer %} <div>copyright</div> {% endblock %}
</body>
```

`base.html`定义了三个可编辑的块，分别命名为`header`、`body`和`footer`。子模板可以有选择地对块进行重新定义：

```
{% extends 'base.html' %}

{% block header %}<h1>{{ header }}</h1>{% endblock %}

{% block body %}<p>{{ body }}</p>{% endblock %}
```

然后，我们对子模板进行渲染：

```
console.log(env.render('extend.html', {
    header: 'Hello',
    body: 'bla bla bla...'
}));
```

输出HTML如下：

```
<html><body>
<h1>Hello</h1>
<p>bla bla bla...</p>
<div>copyright</div> <-- footer没有重定义，所以仍使用父模板的内容
</body>
```

 

### MVC

通过Nunjucks把数据用指定的模板渲染成HTML，然后输出给浏览器，用户就可以看到渲染后的页面了：

![mvc](https://cdn.liaoxuefeng.com/cdn/files/attachments/0014714802383905a3e19e0a96d4f0cbd2daba921364bba000/l)

这就是传说中的MVC：Model-View-Controller，中文名“模型-视图-控制器”。