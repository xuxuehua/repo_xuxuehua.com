---
title: "mocha"
date: 2018-08-08 01:25
---

[TOC]



# Mocha



## 单元测试

单元测试是用来对一个模块、一个函数或者一个类来进行正确性检验的测试工作。

以测试为驱动的开发模式最大的好处就是确保一个程序模块的行为符合我们设计的测试用例。在将来修改的时候，可以极大程度地保证该模块行为仍然是正确的。



## Mocha 特点

mocha是JavaScript的一种单元测试框架，既可以在浏览器环境下运行，也可以在Node.js环境下运行。

使用mocha，我们就只需要专注于编写单元测试本身，然后，让mocha去自动运行所有的测试，并给出测试结果。

mocha的特点主要有：

1. 既可以测试简单的JavaScript函数，又可以测试异步代码，因为异步是JavaScript的特性之一；
2. 可以自动运行所有测试，也可以只运行特定的测试；
3. 可以支持before、after、beforeEach和afterEach来编写初始化代码。

 

### assert模块

package.json

```
{
  "name": "hello-mocha",
  "version": "0.0.1",
  "dependencies": {
    "assert": "^1.4.1"
  }
}
```

Hello.js

```
module.exports = function (...rest) {
    var sum = 0;
    for (let n of rest) {
        sum += n;
    }
    return sum;
}
```



test.js

```
const assert = require('assert');
const sum = require('./hello');

assert.strictEqual(sum(), 0);
assert.strictEqual(sum(1), 1);
assert.strictEqual(sum(1, 2), 3);
assert.strictEqual(sum(1, 2, 3), 6);
```

`assert`模块非常简单，它断言一个表达式为true。如果断言失败，就抛出Error。可以在Node.js文档中查看`assert`模块的[所有API](https://nodejs.org/dist/latest/docs/api/assert.html)。

单独写一个`test.js`的缺点是没法自动运行测试，而且，如果第一个assert报错，后面的测试也执行不了了。

如果有很多测试需要运行，就必须把这些测试全部组织起来，然后统一执行，并且得到执行结果。这就是我们为什么要用mocha来编写并运行测试。

 

## mocha test

```
hello-test/
|
+- .vscode/
|  |
|  +- launch.json <-- VSCode 配置文件
|
+- hello.js <-- 待测试js文件
|
+- test/ <-- 存放所有test
｜ ｜
|  +- hello-test.js <-- 测试文件
|
+- package.json <-- 项目描述文件
|
+- node_modules/ <-- npm安装的所有依赖包
```

如果一个模块在运行的时候并不需要，仅仅在开发时才需要，就可以放到`devDependencies`中。这样，正式打包发布时，`devDependencies`的包不会被包含进来。

package.json

```
{
  "name": "hello-mocha",
  "version": "0.0.1",
  "dependencies": {
    "assert": "^1.4.1"
  },
  "devDependencies": {
    "mocha": "^5.2.0"
  }
}
```



使用`npm install`安装, 不要用命令`npm install -g mocha`把mocha安装到全局module中。这是不需要的。尽量不要安装全局模块，因为全局模块会影响到所有Node.js的工程。

 



mocha默认会执行`test`目录下的所有测试，不要去改变默认目录。



test/hello-test.js

```
const assert = require('assert');

const sum = require('../hello');

describe('#hello.js', () => {
    describe('#sum()', () => {
        it('sum() should reture 0', () => {
            assert.strictEqual(sum(), 0);
        });
        it('sum(1) should reture 1', () => {
            assert.strictEqual(sum(1), 1);
        });
        it('sum(1, 2) should reture 3', () => {
            assert.strictEqual(sum(1, 2), 3);
        });
        it('sum(1, 2, 3) should reture 6', () => {
            assert.strictEqual(sum(1, 2, 3), 6);
        });
});
});
```

> 这里我们使用mocha默认的BDD-style的测试。`describe`可以任意嵌套，以便把相关测试看成一组测试。
>
> 每个`it("name", function() {...})`就代表一个测试。



### 运行测试

#### 方法一

切换到`hello-test`目录，然后执行命令：

```
xhxu-mac:hello-mocha xhxu$ ./node_modules/mocha/bin/mocha


  #hello.js
    #sum()
      ✓ sum() should reture 0
      ✓ sum(1) should reture 1
      ✓ sum(1, 2) should reture 3
      ✓ sum(1, 2, 3) should reture 6


  4 passing (11ms)
```

mocha就会自动执行所有测试



#### 方法二

我们在`package.json`中添加npm命令：

```
{
  ...

  "scripts": {
    "test": "mocha"
  },

  ...
}
```

然后在`hello-test`目录下执行命令：

```
xhxu-mac:hello-mocha xhxu$ npm test

> hello-mocha@0.0.1 test /Users/xhxu/nodejs/hello-mocha
> mocha



  #hello.js
    #sum()
      ✓ sum() should reture 0
      ✓ sum(1) should reture 1
      ✓ sum(1, 2) should reture 3
      ✓ sum(1, 2, 3) should reture 6


  4 passing (10ms)
```



### before和after

在测试前初始化资源，测试后释放资源是非常常见的。

把`hello-test.js`改为：

```
const assert = require('assert');
const sum = require('../hello');

describe('#hello.js', () => {
    describe('#sum()', () => {
        before(function () {
            console.log('before:');
        });

        after(function () {
            console.log('after.');
        });

        beforeEach(function () {
            console.log('  beforeEach:');
        });

        afterEach(function () {
            console.log('  afterEach.');
        });

        it('sum() should return 0', () => {
            assert.strictEqual(sum(), 0);
        });

        it('sum(1) should return 1', () => {
            assert.strictEqual(sum(1), 1);
        });

        it('sum(1, 2) should return 3', () => {
            assert.strictEqual(sum(1, 2), 3);
        });

        it('sum(1, 2, 3) should return 6', () => {
            assert.strictEqual(sum(1, 2, 3), 6);
        });
    });
});
```

再次运行，可以看到每个test执行前后会分别执行`beforeEach()`和`afterEach()`，以及一组test执行前后会分别执行`before()`和`after()`：

```
  #hello.js
    #sum()
before:
  beforeEach:
      ✓ sum() should return 0
  afterEach.
  beforeEach:
      ✓ sum(1) should return 1
  afterEach.
  beforeEach:
      ✓ sum(1, 2) should return 3
  afterEach.
  beforeEach:
      ✓ sum(1, 2, 3) should return 6
  afterEach.
after.
  4 passing (8ms)
```



## 异步测试

### 第一种方法



目录结构

```
async-test/
|
+- .vscode/
|  |
|  +- launch.json <-- VSCode 配置文件
|
+- hello.js <-- 待测试js文件
|
+- data.txt <-- 数据文件
|
+- test/ <-- 存放所有test
｜ ｜
|  +- await-test.js <-- 异步测试
|
+- package.json <-- 项目描述文件
|
+- node_modules/ <-- npm安装的所有依赖包
```



在`package.json`中添加依赖包：

```
"dependencies": {
    "mz": "2.4.0"
},
```



hello.js

```
const fs = require('mz/fs');

module.exports = async () => {
    let expression = await fs.readFile('/data.txt', 'utf-8');
    let fn = new Function('return ' + expression);
    let r = fn();
    console.log(`Calculate: ${expression} = ${r}`);
    return r;
};
```



await-test.js

```
const assert = require('assert');

const hello = require('../hello');

describe('#async hello', () => {
    describe('#asyncCalculate()', () => {
    // function(done) {}
    it('#async with done', (done) => {
    (async function () {
        try {
            let r = await hello();
            assert.strictEqual(r, 15);
            done();
        } catch (err) {
            done(err);
        }
    })();
});

    it('#async function', async () => {
        let r = await hello();
    assert.strictEqual(r, 15);
});

    it('#sync function', () => {
        assert(true);
});
});
});
```



Result

```
xhxu-mac:hello-mocha-async xhxu$ ./node_modules/mocha/bin/mocha


  #async hello
    #asyncCalculate()
Calculate: 1 + (2 + 4) * (9 -2) / 3 = 15
      ✓ #async with done
Calculate: 1 + (2 + 4) * (9 -2) / 3 = 15
      ✓ #async function
      ✓ #sync function


  3 passing (17ms)
```



### 第二种方法

在`package.json`中把`script`改为：

```
"scripts": {
    "test": "mocha"
}
```

这样就可以在命令行窗口通过`npm test`执行测试。





## Http测试

思路是在测试前启动koa的app，然后运行async测试，在测试代码中发送http请求，收到响应后检查结果，这样，一个基于http接口的测试就可以自动运行。

结构如下：

```
koa-test/
|
+- .vscode/
|  |
|  +- launch.json <-- VSCode 配置文件
|
+- app.js <-- koa app文件
|
+- start.js <-- app启动入口
|
+- test/ <-- 存放所有test
｜ ｜
|  +- app-test.js <-- 异步测试
|
+- package.json <-- 项目描述文件
|
+- node_modules/ <-- npm安装的所有依赖包
```



app.js