手写reduce：

- 绑定在原型的方法需要用this来判断下是否是数组调用
- 参数是传入的方法和初始值，若没有初始值遍历是从1开始
- fn方法默认传参数 total 上一个方法执行的结果，arr[i] 当前的值，i 指下标，arr 数组

```javascript
Array.prototype.myReduce = function(fn, init) {
    if(!Array.isArray(this)) {
        throw new TypeError('not a array');
    }
    let arr = this;
    let total = init || arr[0];
    for(let i = init ? 0 : 1; i < arr.length ; i++ ){
        total = fn(total, arr[i], i , arr);
    }
    
    return total;
}
```