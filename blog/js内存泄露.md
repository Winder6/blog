## 内存泄露定义
由于疏忽或错误造成程序未能释放那些已经不再使用的内存，造成内存的浪费。表现为页面占用的内存随着时间增长，从而页面的性能越来越差，越来越卡。

## js内存管理
js内存的申请和释放都是自动的，释放主要是通过垃圾回收机制（对象不再被需要即为垃圾）。难点在找出不再被需要的垃圾，主要有两种算法：
 
- 1.引用计数

“没被其他对象引用即为垃圾”，通过计数来判断是否被其他对象引用，为0时就可被清除。但是无法解决循环引用的问题。

```js

var o = {
	a: {
		b: 2
	}
};


var o2 = o; // o2引用了o对应的对象，现在计数为2
o = 1; // 现在原先o对应的对象只被o2引用了，计数为1

var oa = o2.a; // oa引用了a属性

o2 = 'yo'; // 最开始o对应的对象没有谁引用了，本可以被回收，但是a属性被oa引用了，所以引用计数还
// 是不为0，不能被回收

oa = null; // oa设为null,这下没有任何引用原先o对应对象了，可以被回收
```

- 2.标记清除

“不可获得即为垃圾”，把js中的window对象作为根，可以看做一颗很大的树，判断一个对象是否可以获得，要看从根找能不能找到，该对象不是垃圾，则必定为window的子属性，子子属性...。否则就可以判定为垃圾。这个算法可以处理循环引用的问题。
```js

function fn1() {
	var obj = {name: 'hyw', age: 20};
}


function fn2() {
	var obj = {name:'hyw2', age: 18};
	return obj;
}


var a = fn1();
var b = fn2();


//首先定义了两个function，分别叫做fn1和fn2，当fn1被调用时，进入fn1的环境，会开辟一块内存存放对象obj，被标记记入环境;，而当调用结束后，出了fn1的环境，那么该块内存会被js引擎中的垃圾回收器自动释放；在fn2被调用的过程中，返回的对象被全局变量b所指向，所以该块内存并不会被释放。
```

## 常见内存泄露方式
- 1.未及时清除的无用全局变量

```js

var x = [];

function grow() {
  for (var i = 0; i < 10000; i++) {
    document.body.appendChild(document.createElement('div'));
  }
  x.push(new Array(1000000).join('x'));
  document.getElementById('grow').removeEventListener('click', grow);
}

document.getElementById('grow').addEventListener('click', grow);

```
```js
function createGlobalVariables() {
    leaking1 = 'I leak into the global scope'; // 将值分配给未声明的变量
    this.leaking2 = 'I also leak into the global scope'; // 使用“this”指向全局对象。
};
createGlobalVariables();
window.leaking1; // 'I leak into the global scope'
window.leaking2; // 'I also leak into the global scope'

```
预防措施：使用严格模式（"use strict"）。

- 2.未及时清除的计数器或者回调方法
 
```js

var buggyObject = {
 callAgain: function () {
  var ref = this;
  var val = setTimeout(function () {
   console.log('Called again: ' 
   + new Date().toTimeString()); 
   ref.callAgain();
  }, 1000);
 }
};

buggyObject.callAgain();
buggyObject = null;
```

- 3.js引用了dom，没有正确清理

```js

var select = document.querySelector;
var treeRef = select("#tree");
var leafRef = select("#leaf");
var body = select("body");
body.removeChild(treeRef);
//#tree can't be GC yet due to treeRef
treeRef = null;
//#tree can't be GC yet, due to 
//indirect reference from leafRef
leafRef = null;
//NOW can be #tree GC

//leaf 可以维持对其父级 (parentNode) 的引用，并以递归方式返回 #tree，因此，只有 leafRef 被作废后，#tree 下的整个树才会被清除
```

- 4.console保存大量数据在内存中
 

## 用chrome分析内存问题
- 1.用开发者工具performance判断是否泄露
    - 使用隐身模式
    - 在performance勾选memory，点击record，点击垃圾回收，在页面中进行一些操作，点击垃圾回收
    - 点击stop后看到结果
注意：因为chrome的优化，垃圾回收不会频繁进行，所以需要自己手动垃圾回收，不然结果都会如泄露图所示，内存占用一直上升
- 2.用memory栏详细分析
    - 使用隐身模式
    - 在memory栏，先拍一张内存快照，作为初始状态
    - 一番操作后先触发垃圾回收（垃圾桶logo，避免一些不必要的干扰），再拍内存快照，保险起见多拍几次减少偶然


## 避免内存泄露
- 减少不必要的全局变量，使用严格模式避免意外创建全局变量。
- 在使用完数据后，及时解除引用（闭包中的变量，dom引用，定时器清除）。
- 组织好逻辑，避免死循环等造成浏览器卡顿，崩溃的问题。

## react项目中注意点
- 在componentWillUnmount时移除自定义事件监听和清除定时器
- 慎重使用第三方库，使用后可以测试下是否会产生内存泄露

## 参考
https://mp.weixin.qq.com/s?src=11&timestamp=1592815347&ver=2415&signature=6FG9pMEx1u0J3h5EJx3*QJMfTM-ubKtRZYUpP7v076uP8jXF8nCixpl0YzxC3uMzoitLv8nKvZV5MU-GQfwkrzA3F*ny0IdxpoY7VsPSYPJJUWRUK6H*O7jZo4dgM1Ya&new=1
