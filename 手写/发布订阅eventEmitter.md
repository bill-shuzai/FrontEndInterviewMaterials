发布订阅模式：
对象中创建缓存列表：调度中心
缓存列表添加方法：订阅者注册事件
emit取得到方法并执行：发布者发布事件到调度中心

完整版和错误处理：

- 使用es6的类
- 添加ype的时候要判断是否有type，同样移除的时候要判断是否需要清除掉当前type
- addEventListener 时判断没有type则赋值空数组，并把事件推到数组中
- dispatchEvent触发事件时首先判断是否有type，没有要抛错未注册该事件，有时遍历数组去执行handler
- removeEvent移除事件最复杂
  - 首先判断是否有type，没有抛错无效事件
  - 找到当前ihandler 的 index，用splice来删除
  - index不存在时 ，没有抛错未绑定该事件
  - 删除后该 type 变成空类型时，delete 掉该 type

```javascript
class EventEmitter {
		constructor() {
    	this.handlers = {};
    } 
  
  	addEventListener(type, handler) {
    	if(!(type in this.handlers)) {
        	this.handlers[type] = [];
        }
      	this.handlers[type].push(handler);
    }
  
  	dispatchEvent(type, ...params) {
    	if(!(type in this.handlers)) {
        	throw new Error('未注册该事件');
        }
      	this.handlers[type].forEach(handler => {
        	handler(...params);
        })
    }
  
  	removeEventListener(type, handler) {
    	if(!(type in this.handlers)) {
        	throw new Error('无效事件');
        }
      	if(!handler) {
        	delete this.handlers[type];
        } else {
        const index = this.handlers[type].findIndex(ele => ele === handler);
        if(index === undefined) {
          throw new Error('无该绑定事件');
        }
				this.handlers[type].splice(index, 1);
          	if (this.handlers[type].length === 0) {
            	delete this.handlers[type];
            }
        }
    }
}

var event = new EventEmitter();
function load (params) {
  console.log('load', params)
}

event.addEventListener('load', load)

function load2 (params) {
  console.log('load2', params)
}
event.addEventListener('load', load2)

event.dispatchEvent('load', 'load事件触发')
event.removeEventListener('load', load2)
event.removeEventListener('load')
```