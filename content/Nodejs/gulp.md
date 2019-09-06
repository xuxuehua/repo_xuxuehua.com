---
title: "gulp"
date: 2019-08-30 08:33
---
[TOC]





# Gulp

**gulp**是工具链、构建工具，可以配合各种插件做js压缩，css压缩，less编译 替代手工实现自动化工作

1. 构建工具

2. 自动化

3. 提高效率用

Gulp任务调度工具



## 特点

优化前端工作流程

自动刷新页面，雪碧图，压缩css，js，编译less，检查语法等

通过使用gulp，配置需要的插件，就可以把手动的工作全部自动化





## 分割gulp任务

Many users start by adding all logic to a gulpfile. If it ever grows too big, it can be refactored into separate files.

Node's module resolution allows you to replace your `gulpfile.js` file with a directory named `gulpfile.js` that contains an `index.js` file which is treated as a `gulpfile.js`. This directory could then contain your individual modules for tasks. If you are using a transpiler, name the folder and file accordingly.



## example

```
const gulp = require('gulp');
const autoprefixer = require('autoprefixer');
const cssnano = require('cssnano');
const atImport = require('postcss-import');

gulp.task('postcss', function() {
		var postcss = require('gulp-postcss');
		return gulp.src('src/plugins-main.css');
				.pipe(postcss([
						atImport,
						autoprefixer({
								browsers:['last 2 versions']
						}),
						cssnano
				]))
				.pipe(gulp.dest('build/'));
});
```

```
gulp postcss
```

