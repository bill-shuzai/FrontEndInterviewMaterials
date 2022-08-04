# Call
this 指向最后一个调用他对象，因为是要给方法指定新的this，所以一开始可以先判断是否给函数使用的。

- 非函数判断
- context需记住不传this的情况
- 直接利用context.fn来挂载方法
- 参数记得把第一个参数去掉
- 删除挂载的方法再返回结果


```javascript
Function.prototype.myCall = function(context) {
    // this 指向调用函数 myCall 的对象
  	if(typeof this !== 'function') {
    	throw new TypeError('not a function');
    }
	  context = context || window;
  	context.fn = this; // 隐式绑定，改变构造函数的调用者，间接改变 this 指向
  	// 将方法添加到context下的fn。表示context去调用那个方法
  	let args = [...arguments].slice(1);
  	let result = context.fn(...args);
  	delete context.fn;
  	return result;
}
```

# apply

- 非函数判断
- context需记住不传一参的情况
- 第二个参数是数组不确定是不是所以需要判断

```javascript
Function.prototype.myApply = function(context) {
    // this 指向调用函数 myCall 的对象
  	if(typeof this !== 'function') {
    	throw new TypeError('not a function');
    }
	   context = context || window;
  	context.fn = this; // 隐式绑定，改变构造函数的调用者，间接改变 this 指向
  	// 将方法添加到context下的fn。表示context去调用那个方法
  	let result;
  	if(arguments[1]) {
  	     result = constext.fn(…argument[1]);
  	}
  	else {
  	     result = constext.fn();
  	}
  	delete context.fn;
  	return result;
}
```

# bind
* bind 传入多个参数
* 创建的函数传入多个参数
* 新函数可能被当做构造函数使用
* 函数可能有返回值
* 支持柯里化传参



- 非函数判断
- 记住当前的this _this，context也需要
- 返回的时候给具名
- 通过判断 this instanceof F 是否作为构造函数使用
- 构造函数使用 return new _this(...args, ...arguments);
- 否则返回_this.apply

```javascript
Function.prototype.mybind = function(context) {
	if(typeof this !== 'function') {
    	throw new TypeError('not a function');
    }
    let _this = this;
  	context = context || window;
  	let arg = [...arguments].slice(1);
  	return function F() {
    	if(this instanceof F) {
          	// 此处argument 为新函数的参数
        	return new _this(...arg, ...arguments);
        }
      	else {
        	return _this.apply(context, arg.concat(...arguments));
        }
    }
}
```