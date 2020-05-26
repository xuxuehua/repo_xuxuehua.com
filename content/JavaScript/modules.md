---
title: "modules"
date: 2020-05-05 19:36
---
[toc]



# 模块



## export 

cars.js

```
const carsJson = `[{"year": 2010, "model": "Honda"}, {"year": 2012, "model": "Benz"}]`;

export const cars = JSON.parse(carsJson);
```



index.js

```
import { cars } from 'cars.js';
```






