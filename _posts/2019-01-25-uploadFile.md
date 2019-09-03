---
layout: post
title: 多文件上传进度监测
categories: javascript
description: 多文件上传进度监测
keywords: javascript, Progress, uoloadFile
---


Progress Events


多文件上传的代码，#uploadBtn是inputtype=file，监听它的改变来获取文件对象

```
    var file = null;
    var dataArr = [];
    $('#uploadBtn').change(function (e) {
        // 文件对象，是一个类数组对象，多个文件也只是多几个对象，不是数组，不过可以遍历
        file = e.target.files || e.dataTransfer.files;
        //  下面的逻辑....
    });
```
然后上传，使用的是FormData

```
        var fm = new FormData();
        for (var i = 0; i < file.length; i++) {
            fm.append("files", file[i])
        }
```

需要监听进度做进度条，jq本身是没有的，需要自己手动new一个带进度事件的XHR对象给jq绑定上。
```
        var xhrOnProgress = function (fun) {
            xhrOnProgress.onprogress = fun; //绑定监听
            //使用闭包实现监听绑
            return function () {
                //通过$.ajaxSettings.xhr();获得XMLHttpRequest对象
                var xhr = $.ajaxSettings.xhr();
                //判断监听函数是否为函数
                if (typeof xhrOnProgress.onprogress !== 'function')
                    return xhr;
                //如果有监听函数并且xhr对象支持绑定时就把监听函数绑定上去
                if (xhrOnProgress.onprogress && xhr.upload) {
                    xhr.upload.onprogress = xhrOnProgress.onprogress;
                }
                return xhr;
            }
        }

      // ajax

         $.ajax({
            type: "post",
            url: url,
            data: fm,
            processData: false,
            contentType: false,
            headers: {
                'x-auth-token': token
            },
            xhr: xhrOnProgress(function (e) {
                var percent = e.loaded / e.total;//计算百分比
            }),
            success: function (response) {
                alert('上传成功');
            },
            error: function (err) {
                console.log(err)
            }
        });
```

如果用原生js写的话可以直接监听Progress事件

```
  var xhr;
    if(window.XMLHttpRequest){
        xhr = new XMLHttpRequest();
    }else{
        xhr = new ActiveXObject('Microsoft.XMLHTTP');
    }
    //进度事件
    xhr.onprogress = function(e){
        e = e || event;
        if (e.lengthComputable){
            var percent =  e.loaded / e.total ;
        }
    };
    //发送请求
    xhr.open('post','url',true);
    var form = new FormData();;
    form.append(file)
    xhr.send(form);
    xhr.onreadystatechange = function(){
        if(xhr.readyState==4 && xhr.status==200){         if(obj.status){
            alert('上传成功');
          }else{
            alert('上传失败');
          }
        }
      }
```

额外扩展一下 progress 事件
Progress Events定义了与客户端服务器通信有关的事件。IE8 或更早的浏览器不支持该事件。
有三个事件对象属性：lengthComputable、loaded和total， lengthComputable是一个表示进度信息是否可用的布尔值，loaded表示已经接收的字节数，loaded表示根据Content-Length响应头部确定的预期字节数，知道了原理计算百分比进度就简单啦~

