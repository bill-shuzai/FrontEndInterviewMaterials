# 1、使用setTimeout实现
```javascript
function repeat(fn, times, wait) {
    return function callback() {
        fn.call(this, ...arguments);
        times-- && setTimeout(() => {
            callback(...arguments);
        }, wait);
    }
}
```

# 2、使用setInterval
```javascript
function repeat(func, times, wait) {
    return function(val) {
        let count = 1;
        func(val);
        let timer = setInterval(() => {
            count++;
            count < times ? '' : clearInterval(timer);
            func(val);
        }, wait);
    }
}
```





