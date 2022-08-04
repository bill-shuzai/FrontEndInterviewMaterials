思路：右边变量的原型在左边变量的原型链上。

- 获取左边变量的下划线 proto 和右边的原型prototype
- 死循环开头，只要左边不为空一直下划线proto直到空
- 相等则返回 ture


```javascript
function myInstanceof(left, right) {
		let leftValue = left.__proto__;
  	let rightValue = right.prototype;
  	while(true) {
    		if(leftValue === null) {
        	return false;
        }
      	if(leftValue === rightValue) {
        	return true;
        }
      	leftValue = leftValue.__proto__;
    }
}
```
由于__proto__在部分浏览器不兼容，可用对象方法Object.getPrototypeOf()来替换