---

title: "storage"
date: 2019-12-29 12:56

---

[toc]



# storage





## localStorage

本地缓存

永久有效，除非手动删除

可以多个窗口共享

容量约20M



### getItem/setItem

```
    <script>
        $(function() {
            var initial_sidebar = localStorage.getItem(&#39;sidebar_local&#39;) || &#39;on&#39;;
            alert(&#39;var initial value: &#39; + initial_sidebar);

            if (initial_sidebar === &#39;on&#39;) {
                alert(&#39;current is on&#39;);
            }
            else {
                alert(&#39;current is off&#39;);
            }

        $(&#39;#sideBarLogo&#39;).click(function() {
            var current_initial_sidebar_value = localStorage.getItem(&#39;sidebar_local&#39;);
            if ( current_initial_sidebar_value === &#39;on&#39;) {
                localStorage.setItem(&#39;sidebar_local&#39;, &#39;off&#39;);
            } 
            else {
                localStorage.setItem(&#39;sidebar_local&#39;, &#39;on&#39;);
            }
        })

        });
    </script>
```



### removeItem

删除内容

```
window.localStorage.removeItem(key)
```



### clear

清空内容

```
window.localStorage.clear()
```





## sessionStorage

生命周期为关闭浏览器

在同一个窗口下数据可以共享

容量约为5M



### setItem/getItem

```
window.sessionStorage.setItem(key,value)
window.sessionStorage.getItem(key)
```



### removeItem

```
window.sessionStorage.removeItem(key)
```

### clear

```
window.sessionStorage.clear()
```
