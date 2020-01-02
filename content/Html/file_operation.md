---
title: "file_operation"
date: 2019-12-29 12:26

---

[toc]

# 文件操作

## FileReader

### readAsText

文件读取为文本

```
   <input type="file" id="f1">
   <input type="file" id="f2">

   <script>
      // 读取文件，获取上传文件标签的这个对象
      var f1 = document.querySelector("#f1");
      // 上传文件的触发事件
      f1.onchange=function () {
         var fl1=this.files[0];
         // 创建读取文件的对象
         var fReader = new FileReader();
         fReader.readAsText(fl1);
         //开始读取文件的加载事件
         fReader.onload=function () {
            //获取读取的结果
            var result = fReader.result;
            console.log(result);
         }
      }
```

### readAsBinaryString

文件读取为二进制编码

### readAsDataURL

文件读取为DataURL

## FileReader事件触发

### onabort 中断时触发

### onerror 出错时触发

### onload 文件读取完成时触发

#### 上传图片显示

```html
<input type="file" />
<img src="" alt="" />


<script>
      var fl = document.querySelector("input");
      fl.onchange=function() {
         // 创建读取文件的对象
         var fReader = new FileReader();
         // 读取图片
         fReader.readAsDataURL(fl.files[0]);
         // 图片读取的加载过程
         fReader.onload=function() {
            document.querySelector("img").src=this.result;
         };
      }
</script> 
```

### onloadend 读取完成触发，无论成功失败

### onloadstart 读取开始时触发

### onprogress 读取中

### onchange 上传事件

```html
<script>
   var fl = document.querySelector("input");
   // 上传事件
   fl.onchange=function() {
      // 获取文件
      var file= fl.files[0];
      var fRead = new FileReader();
      fRead.readAsText(file);
      fRead.onload=function() {
         var txtJS=this.result;
         var st=document.createElement("script");
         st.innerHTML=txtJS;
         document.querySelector("head").appendChild(st);
      }
   }
</script>
```
