---
title: "ajax"
date: 2019-08-15 07:19
---
[TOC]



# AJAX

指异步Javascript和XML(Asynchronous Java Script And XML) 

基于XMLHttp Request 在页面不重载的情况下和服务器进行数据交换

可以使用jQuery发送AJAX请求





## ajax() 函数



## 异步加载长文章

```
from jinja2.utils import generate_lorem_ipsum

@app.route('/post')
def show_post():
    post_body = generate_lorem_ipsum(n=2)
    return '''
    <h1>Long post</h1>
    <div class="body">%s</div>
    <button id="load">Load more </button>
    <script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
    <script type="text/javascript">
    $(function(){
    	$('#load').click(function() {
    		$.ajax({
    			url: '/more',
    			type: 'get',
    			success: function() {
    				$('.body').append(data);
    			}
    		});
    	});
    })
    </script>''' % post_body
```

