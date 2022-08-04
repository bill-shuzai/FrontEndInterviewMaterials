new 操作符表示使用构造函数来构造实例。

# 使用下划线proto

- 新建一个对象

```javascript
function _new() {
	let obj = {};
	let fn = [].shift.call(argument);
	obj._proto_ = fn.prototype;
	let result = fn.apply(obj, arguments);
	return typeof result === “object” ? result : obj;
}
```

# 使用Object.create()

```javascript
function _new(fn, ...args) {
	   let a = Object.create(fn.prototype); // 使用fn原型创建一个新对象
  	let result = fn.apply(a, args); // 绑定 this
  	return typeof result === "Object" ? result : a;
}
```