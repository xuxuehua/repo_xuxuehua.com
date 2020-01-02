---
title: "network"
date: 2019-12-29 12:45

---

[toc]

# Network

## onLine

```
   <script>
      // h5 中的网络状态检测
      var state = window.navigator.onLine;
      if (state) {
         alert("online");
      } else {
         alert("offline");
      }

      window.ononline=function() {
         alert("connected");
      };
      window.onoffline=function() {
         alert("disconnected");
      };
   </script>
```
