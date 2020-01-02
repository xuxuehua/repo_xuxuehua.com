---
title: "location"
date: 2019-12-29 12:46

---

[toc]

# Location 定位



## geolocation



```
   <script>
      // 通过网络定位位置
      window.navigator.geolocation.getCurrentPosition(function(position) {
         console.log(position.coords.latitude); //纬度
         console.log(position.coords.longitude); //经度
      },function (msg) {
         console.log(msg);
      });
   </script>
```





## watchPosition 实时位置
