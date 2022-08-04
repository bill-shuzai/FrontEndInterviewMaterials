# 手写Ajax
新建XMLHTTPRequest实例化对象。
对象的readyState用来获取ajax状态吗

* 0未初始化，没调用open
* 1请求建立未发送，没有调用open
* 2已发送请求
* 3请求正在处理，响应中的部分数据可以用
* 4请求已完成，可以获取服务器的响应

当readyState的状态发生改变时，会触发xhr的时间onreadystatechange


```javascript
var xhr = new XMLHTTPRequest();
xhr.onreadystatechange = function(){
    if(xhr.readyState ===4 && xhr.status === 200) {
        console.log(xhr.respnesText;)
    }
}
xhr.open('post', 'url');
xhr.send();
```



```javascript
const getJSON = function(url) {
    return new Promise((resolve, reject) => {
        const xhr = XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject('Microsoft.XMLHTTP');
        xhr.open('GET', url, false);
        xhr.setRequestHeader('Accept', 'application/json');
        xhr.onreadystatechange = function() {
            if (xhr.readyState !== 4) return;
            if (xhr.status === 200 || xhr.status === 304) {
                resolve(xhr.responseText);
            } else {
                reject(new Error(xhr.responseText));
            }
        }
        xhr.send();
    })
}
```

