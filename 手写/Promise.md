promise 是ES6出的用于控制异步流的机制，是一种封装和组合未来值的易于复用的机制，弥补了回调函数信任缺失与控制反转的缺点，解决了回调地狱问题，但是它不是对回调函数的替代，而是在回调代码和将要执行这个代码之间提供了一种可靠的中间机制来管理回调。

* 如果调用reject，这个promise被拒绝，如果有任何值传给reject，这个值就被设置为拒绝的原因值。
* 如果调用resolve且没有值传入，或者传入任何非promise值，这个promise就完成。
* 如果调用resolve并传入另外一个promise，这个promise就会采用传入的promise的状态（要么实现要么拒绝）

* 每次对Promise调用then(),都会创建并返回一个新的promise，可以将其链接起来
* 不管从then调用的完成回调返回值是什么，都会自动被设置为被链接promise的完成。

sleep函数：
```javascript
function sleep(wait) {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, wait);
    })
}

sleep(3000)
    .then(() => {
        console.log('过了三秒打印');
        return sleep(4000);
    })
    .then(() => {
        console.log('过了4秒');
    })
```

# 简单的同步代码
未添加异步处理等其他边界情况。自动执行函数，有三个状态，then，基本实现简单的同步代码。

```javascript
class myPromise {
	constructor(fn) {
    	this.state = 'pending';
      	this.value = undefined;
      	this.reason = undefined;
      	let resolve = value = {
          if (this.state === "pending"){
          	this.state = "fulfulled";
            this.value = value;
          }
        }
        let reject = value => {
        	if (this.state === "pending") {
            	this.state = 'rejected';
              	this.reason = value;
            }
        }
        
        try {
        	fn(resulve, reject)
        } catch (e) {
          	reject(e)
        }
    }
  
  	then(onFulfilled, onRejected) {
    	switch(this.state) {
        	case "fulfilled":
            	onFulfilled(this.value);
            	break;
            case "rejected": 
            	onRejected(this.reason);
            	break;
          	default:
        }
    }
}
```

# 解决异步实现
当resolve放在setTimeout内执行，then时state还是pending的状态时，在调用的时候，将成功和失败存到各自的数组中，一旦成功或失败就调用它们。

```javascript
class myPromise {
	constructor(fn) {
    	this.state = 'pending';
      	this.value = undefined;
      	this.reason = undefined;
      	this.onRevolvedCallbacks = [];
      	this.onRejectedCallbacks = [];
      	let resolve = value = {
          if (this.state === "pending"){
          	this.state = "fulfulled";
            this.value = value;
            this.onRevolvedCallbacks.forEach(fn => fn());
          }
        }
        let reject = value => {
        	if (this.state === "pending") {
            	this.state = 'rejected';
              	this.reason = value;
              	this.onRejectedCallbacks.forEach(fn => fn())
            }
        }
        
        try {
        	fn(resulve, reject)
        } catch (e) {
          	reject(e)
        }
    }
  
  	then(onFulfilled, onRejected) {
    	if(this.state === 'fulfilled') {
        	onFulfilled(this.value);
        }
      	else if(this.state === 'rejected') {
        	onRejected(this.reason);
        }
      	else if(this.state === 'pending') {
        	this.onResoledCallbacks.push(() => {
            	onFulfilled(this.value);
            })
          	this.onRejectedCallbacks.push(() => {
            	onRejected(this.reason);
            })
        }
    }
}
```

# 手写 promise.all

```javascript
function PromiseAll(promiseArray) {
    return new Promise((resolve, reject) => {
        if(!Array.isArray(promiseArray)) {
            return reject(new TypeError('传入的不是参数'));
        }
        
        const res = [];
        const counter = 0;
        for(let i = 0; i < promiseArray.length; i++){ 
        Promise.resolve(promiseArray[i]).then(value => {
            counter++;
            res[i] = value;
            if(counter === promiseArray.length) {
                resolve(res);
            }
        }).catch(e => reject(e))
        }
    })
}
```

# 手写promise.race()

```javascript
function race(arr) {
    if(!Array.isArray(arr)) {
        throw new TypeError('not a array');
    }
    return new Promise((resolve, reject) => {
        for(let i = 0; i < arr.length ; i++) {
            arr[i].then(res => {
                resolve(res);
            }).catch(e => {
                reject(e);
            })
        }
    });
}
```

# 手写promise.all()
```javascript
function all(arr) {
    if(!Array.isArray(arr)) {
        throw new TypeError('not a array');
    }
    return new Promise((resolve, reject) => {
        let result = [];
        let len = arr.length;
        let count = 0;
        for(let i = 0; i < len ; i++) {
            arr[i].then(res => {
                result.push(res);
                count++;
                if(result.length === len) {
                    resolve(result)
                }
            }).catch(e => {
                reject(e);
            })
        }
    })
}
```

# 手写promise并发

- 参数有链接数组和并发的最大值
- maxReleaseList 释放请求数的数组，最多不超过 maxNums
- 存储结果的数组 dataList
- 用next函数来控制，一直回调所以需要临界条件，执行完了是返回Promise.resolve()
- 获取当前path，splice
- 开始请求，req记录请求结果
- 定义请求释放的方法，并把方法记录到请求释放数组中 splice
- 设置结果默认为完成
- 判断如果数组的数量过多应该释放位置
- 返回结果再then去执行next
- 最后直接调用next的then去promiseAll所有的结果

```javascript
function multiRequest(urls, maxNums) {
    //释放请求数的数组，最多不超过maxNums
    let maxReleaseList = [];
    //存储结果的数组
    let dataList = [];
    const next = () => {
        //递归结束条件：urls执行完了
        if(urls.length <= 0) return Promise.resolve();
        let path = urls.splice(0,1);
        console.log(path, '开始');
        let req = Promise.resolve(axios(path));
        dataList.push(req);
        let reqRelease = req.then(() => {
            maxReleaseList.splice(0,1);
            console.log(path, '结束');
        })
        maxReleaseList.push(reqRealease);
        let r = Promise.resolve();
        if(maxReleaseList.length >= maxNums) {
            r = Promise.race(maxReleaseList);
        }
        return r.then(() => next());
    }
    return next().then(() => Promise.all[dataList])
}
```



