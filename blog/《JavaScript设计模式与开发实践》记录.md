设计模式的主题总是把不变的事物和变化的事物分离开来


# 单例模式
需要时才创建对象实例，不默认调用
```js
var getSingle = function( fn ){
var result;
    return function(){
        return result || ( result = fn .apply(this, arguments ) );
    }
};

var createLoginLayer = function(){
    var div = document.createElement( 'div' );
    div.innerHTML = '我是登录浮窗';
    div.style.display = 'none';
    document.body.appendChild( div );
    return div;
};
var createSingleLoginLayer = getSingle( createLoginLayer );
document.getElementById( 'loginBtn' ).onclick = function(){
    var loginLayer = createSingleLoginLayer();
    loginLayer.style.display = 'block';
};

```

# 策略模式
消除if else
```js
//原版
var calculateBonus = function( performanceLevel, salary ){
if ( performanceLevel === 'S' ){
    return salary * 4;
}
if ( performanceLevel === 'A' ){
    return salary * 3;
}
if ( performanceLevel === 'B' ){
    return salary * 2;
}
};
calculateBonus( 'B', 20000 ); // 输出：40000
calculateBonus( 'S', 6000 ); // 输出：24000

//策略模式
var strategies = {
"S": function( salary ){
    return salary * 4;
},
"A": function( salary ){
    return salary * 3;
},
"B": function( salary ){
    return salary * 2;
}
};
var calculateBonus = function( level, salary ){
    return strategies[ level ]( salary );
};
console.log( calculateBonus( 'S', 20000 ) ); // 输出：80000
console.log( calculateBonus( 'A', 10000 ) ); // 输出：30000
```
# 代理模式
不方便直接访问对象，考虑采用此模式
```js
//先创建一个用于求乘积的函数：
var mult = function () {
  console.log("开始计算乘积");
  var a = 1;
  for (var i = 0, l = arguments.length; i < l; i++) {
    a = a * arguments[i];
  }
  return a;
};
mult(2, 3); // 输出：6
mult(2, 3, 4); // 输出：24
//现在加入缓存代理函数：
var proxyMult = (function () {
  var cache = {};
  return function () {
    var args = Array.prototype.join.call(arguments, ",");
    if (args in cache) {
      return cache[args];
    }
    return (cache[args] = mult.apply(this, arguments));
  };
})();
proxyMult(1, 2, 3, 4); // 输出：24
proxyMult(1, 2, 3, 4); // 输出：24
```

# 迭代器模式
迭代器模式是指提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示。迭代器模式可以把迭代的过程从业务逻辑中分离出来，在使用迭代器模式之后，即使不关心对象的内部构造，也可以按顺序访问其中的每个元素
```js
var getUploadObj = function(){
    try{
        return new ActiveXObject("TXFTNActiveX.FTNUpload"); // IE 上传控件
    }catch(e){
        if ( supportFlash() ){ // supportFlash 函数未提供
        var str = '<object type="application/x-shockwave-flash"></object>';
        return $( str ).appendTo( $('body') );
    }else{
        var str = '<input name="file" type="file"/>'; // 表单上传
        return $( str ).appendTo( $('body') );
        }
    }
}

<!--优化-->
  var getActiveUploadObj = function () {
    try {
      return new ActiveXObject("TXFTNActiveX.FTNUpload"); // IE 上传控件
    } catch (e) {
      return false;
    }
  };
  var getFlashUploadObj = function () {
    if (supportFlash()) { // supportFlash 函数未提供
      var str = '<object type="application/x-shockwave-flash"></object>';
      return $(str).appendTo($('body'));
    }
    return false;
  };
  var getFormUpladObj = function () {
    var str = '<input name="file" type="file" class="ui-file"/>'; // 表单上传
    return $(str).appendTo($('body'));
  };
  
  
  // 核心
    var iteratorUploadObj = function () {
    for (var i = 0, fn; fn = arguments[i++];) {
      var uploadObj = fn();
      if (uploadObj !== false) {
        return uploadObj;
      }
    }
  };
  var uploadObj = iteratorUploadObj(getActiveUploadObj, getFlashUploadObj, getFormUpladObj);
```

# 发布订阅模式
又叫观察者模式，它定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都将得到通知。
```js
var Event = (function(){
		var clientList = {},
		listen,
		trigger,
		remove;
		listen = function( key, fn ){
			if ( !clientList[ key ] ){
				clientList[ key ] = [];
			}
			clientList[ key ].push( fn );
		};
		trigger = function(){
			var key = Array.prototype.shift.call( arguments ),
			fns = clientList[ key ];
			if ( !fns || fns.length === 0 ){
				return false;
			}
			for( var i = 0, fn; fn = fns[ i++ ]; ){
				fn.apply( this, arguments );
			}
		};
		remove = function( key, fn ){
			var fns = clientList[ key ];
			if ( !fns ){
				return false;
			}
			if ( !fn ){
				fns && ( fns.length = 0 );
			}else{
				for ( var l = fns.length - 1; l >=0; l-- ){
					var _fn = fns[ l ];
					if ( _fn === fn ){
						fns.splice( l, 1 );
					}
				}
			}
		};
		return {
			listen: listen,
			trigger: trigger,
			remove: remove
		}
	})();

	Event.listen( 'squareMeter88', function( price ){ // 小红订阅消息
		console.log( '价格= ' + price ); // 输出：'价格=2000000'
	});

	Event.trigger( 'squareMeter88', 2000000 ); // 售楼处发布消息

```

# 命令模式
有时候需要向某些对象发送请求，但是并不知道请求的接收者是谁，也不知道被请求的操作是什么。此时希望用一种松耦合的方式来设计程序，使得请求发送者和请求接收者能够消除彼此之间的耦合关系。
```js
	var bindClick = function( button, func ){
			button.onclick = func;
		};
		var MenuBar = {
			refresh: function(){
				console.log( '刷新菜单界面' );
			}
		};
		var SubMenu = {
			add: function(){
				console.log( '增加子菜单' );
			},
			del: function(){
				console.log( '删除子菜单' );
			}
		};
		bindClick( button1, MenuBar.refresh );

		bindClick( button2, SubMenu.add );
		bindClick( button3, SubMenu.del );
```

# 组合模式
父对象和子对象有一样的方法
```js
var closeDoorCommand = {
		execute: function(){
			console.log( '关门' );
		}
	};
	var openPcCommand = {
		execute: function(){
			console.log( '开电脑' );
		}
	};
	var openQQCommand = {
		execute: function(){
			console.log( '登录QQ' );
		}
	};
	var MacroCommand = function(){
		return {
			commandsList: [],
			add: function( command ){
				this.commandsList.push( command );
			},
			execute: function(){
				for ( var i = 0, command; command = this.commandsList[ i++ ]; ){
					command.execute();
				}
			}
		}
	};
	var macroCommand = MacroCommand();
	macroCommand.add( closeDoorCommand );
	macroCommand.add( openPcCommand );
	macroCommand.add( openQQCommand );
	macroCommand.execute();
```

# 模板方法模式
模板方法模式由两部分结构组成，第一部分是抽象父类，第二部分是具体的实现子类。通常在抽象父类中封装了子类的算法框架，包括实现一些公共方法以及封装子类中所有方法的执行顺序。子类通过继承这个抽象类，也继承了整个算法结构，并且可以选择重写父类的方法。
```js
var Beverage = function( param ){
		var boilWater = function(){
			console.log( '把水煮沸' );
		};
		var brew = param.brew || function(){
			throw new Error( '必须传递brew 方法' );
		};
		var pourInCup = param.pourInCup || function(){
			throw new Error( '必须传递pourInCup 方法' );
		};
		var addCondiments = param.addCondiments || function(){
			throw new Error( '必须传递addCondiments 方法' );
		};
		var F = function(){};
		F.prototype.init = function(){
			boilWater();
			brew();
			pourInCup();
			addCondiments();
		};
		return F;
	};
	var Coffee = Beverage({
		brew: function(){
			console.log( '用沸水冲泡咖啡' );
		},
		pourInCup: function(){
			console.log( '把咖啡倒进杯子' );
		},
		addCondiments: function(){
			console.log( '加糖和牛奶' );
		}
	});

	var Tea = Beverage({
		brew: function(){
			console.log( '用沸水浸泡茶叶' );
		},
		pourInCup: function(){
			console.log( '把茶倒进杯子' );
		},
		addCondiments: function(){
			console.log( '加柠檬' );
		}
	});
	var coffee = new Coffee();
	coffee.init();
	var tea = new Tea();
	tea.init();
```

# 享元模式
享元模式要求将对象的属性划分为内部状态与外部状态（状态在这里通常指属性）。享元模式的目标是尽量减少共享对象的数量,可以把所有内部状态相同的对象都指定为同一个共享的对象。而外部状态可以从对象身上剥离出来，并储存在外部。剥离了外部状态的对象成为共享对象，外部状态在必要时被传入共享对象来组装成一个完整的对象。
```js
	var Model = function( sex ){
		this.sex = sex;
	};
	Model.prototype.takePhoto = function(){
		console.log( 'sex= ' + this.sex + ' underwear=' + this.underwear);
	};

	var maleModel = new Model( 'male' ),
	femaleModel = new Model( 'female' );

	for ( var i = 1; i <= 50; i++ ){
		maleModel.underwear = 'underwear' + i;
		maleModel.takePhoto();
	};

	for ( var j = 1; j <= 50; j++ ){
		femaleModel.underwear = 'underwear' + j;
		femaleModel.takePhoto();
	};
```

# 职责链模式
使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间
的耦合关系，将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止。
```js
	var order500 = function( orderType, pay, stock ){
		if ( orderType === 1 && pay === true ){
			console.log( '500 元定金预购，得到100 优惠券' );
		}else{
			return 'nextSuccessor'; // 我不知道下一个节点是谁，反正把请求往后面传递
		}
	};

	var order200 = function( orderType, pay, stock ){
		if ( orderType === 2 && pay === true ){
			console.log( '200 元定金预购，得到50 优惠券' );
		}else{
			return 'nextSuccessor'; // 我不知道下一个节点是谁，反正把请求往后面传递
		}
	};

	var orderNormal = function( orderType, pay, stock ){
		if ( stock > 0 ){
			console.log( '普通购买，无优惠券' );
		}else{
			console.log( '手机库存不足' );
		}
	};
	
	Function.prototype.after = function( fn ){
		var self = this;
		return function(){
			var ret = self.apply( this, arguments );
			if ( ret === 'nextSuccessor' ){
				return fn.apply( this, arguments );
			}
			return ret;
		}
	};
	
	var order = order500yuan.after( order200yuan ).after( orderNormal );
	order( 1, true, 500 ); // 输出：500 元定金预购，得到100 优惠券
	order( 2, true, 500 ); // 输出：200 元定金预购，得到50 优惠券
	order( 1, false, 500 ); // 输出：普通购买，无优惠券
</script>
	
```
# 中介者模式
中介者模式的作用就是解除对象与对象之间的紧耦合关系。增加一个中介者对象后，所有的相关对象都通过中介者对象来通信，而不是互相引用，所以当一个对象发生改变时，只需要通知中介者对象即可。中介者使各对象之间耦合松散，而且可以独立地改变它们之间的交互。中介者模式使网状的多对多关系变成了相对简单的一对多关系
```js
    var goods = { // 手机库存
        "red|32G": 3,
        "red|16G": 0,
        "blue|32G": 1,
        "blue|16G": 6
    };
    var mediator = (function() {
        var colorSelect = document.getElementById('colorSelect'),
            memorySelect = document.getElementById('memorySelect'),
            numberInput = document.getElementById('numberInput'),
            colorInfo = document.getElementById('colorInfo'),
            memoryInfo = document.getElementById('memoryInfo'),
            numberInfo = document.getElementById('numberInfo'),
            nextBtn = document.getElementById('nextBtn');
        return {
            changed: function (obj) {
                var color = colorSelect.value, // 颜色
                    memory = memorySelect.value,// 内存
                    number = numberInput.value, // 数量
                    stock = goods[color + '|' + memory]; // 颜色和内存对应的手机库存数量
                if (obj === colorSelect) { // 如果改变的是选择颜色下拉框
                    colorInfo.innerHTML = color;
                } else if (obj === memorySelect) {
                    memoryInfo.innerHTML = memory;
                } else if (obj === numberInput) {
                    numberInfo.innerHTML = number;
                }
                if (!color) {
                    nextBtn.disabled = true;
                    nextBtn.innerHTML = '请选择手机颜色';
                    return;
                }
                if (!memory) {
                    nextBtn.disabled = true;
                    nextBtn.innerHTML = '请选择内存大小';
                    return;
                }
                if (((number - 0) | 0) !== number - 0) { // 输入购买数量是否为正整数
                    nextBtn.disabled = true;
                    nextBtn.innerHTML = '请输入正确的购买数量';
                    return;
                }
                nextBtn.disabled = false;
                nextBtn.innerHTML = '放入购物车'
            }
        }
    })()

    // 事件函数：
    colorSelect.onchange = function(){
        mediator.changed( this );
    };
    memorySelect.onchange = function(){
        mediator.changed( this );
    };
    numberInput.oninput = function(){
        mediator.changed( this );
    };
```

# 装饰者模式
给对象动态地增加职责的方式称为装饰者（decorator）模式。装饰者式能够在不改变对象自身的基础上，在程序运行期间给对象动态地添加职责。跟继承相比，装饰者是一种更轻便灵活的做法，这是一种“即用即付”的方式

```js
Function.prototype.before = function( beforefn ){
		var __self = this;
		return function(){
			beforefn.apply( this, arguments );
			return __self.apply( this, arguments );
		}
	}
	document.getElementById = document.getElementById.before(function(){
		alert (1);
	});
	var button = document.getElementById( 'button' );

	console.log( button );

	window.onload = function(){
		alert (1);
	}
	window.onload = ( window.onload || function(){} ).after(function(){
		alert (2);
	}).after(function(){
		alert (3);
	}).after(function(){
		alert (4);
	});


	var before = function( fn, beforefn ){
		return function(){
			beforefn.apply( this, arguments );
			return fn.apply( this, arguments );
		}
	}
	var a = before(
		function(){alert (3)},
		function(){alert (2)}
		);
	a = before( a, function(){alert (1);} );
	a();
```

# 状态模式
将状态封装成独立的类，并将请求委托给当前的状态对象，当对象的内部状态改变时，会带来不同的行为变化。从客户的角度来看，我们使用的对象，在不同的状态下具有截然不同的行为，这个对象看起来是从不同的类中实例化而来的，实际上这是使用了委托的效果。
```js
var OffLightState = function( light ){
		this.light = light;
	};

	OffLightState.prototype.buttonWasPressed = function(){
		console.log( '弱光' ); // offLightState 对应的行为
		this.light.setState( this.light.weakLightState ); // 切换状态到weakLightState
	};
// WeakLightState：
var WeakLightState = function( light ){
	this.light = light;
};

WeakLightState.prototype.buttonWasPressed = function(){
		console.log( '强光' ); // weakLightState 对应的行为
		this.light.setState( this.light.strongLightState ); // 切换状态到strongLightState
	};
	// StrongLightState：
	var StrongLightState = function( light ){
		this.light = light;
	};

	StrongLightState.prototype.buttonWasPressed = function(){
		console.log( '关灯' ); // strongLightState 对应的行为
		this.light.setState( this.light.offLightState ); // 切换状态到offLightState
	};

	var Light = function(){
		this.offLightState = new OffLightState( this );
		this.weakLightState = new WeakLightState( this );
		this.strongLightState = new StrongLightState( this );
		this.button = null;
	};


	Light.prototype.init = function(){
		var button = document.createElement( 'button' ),
		self = this;
		this.button = document.body.appendChild( button );
		this.button.innerHTML = '开关';
		this.currState = this.offLightState; // 设置当前状态
		this.button.onclick = function(){
			self.currState.buttonWasPressed();
		}	
	};
		Light.prototype.setState = function( newState ){
		this.currState = newState;
	};

	var light = new Light();
	light.init();
```

# 适配器模式
适配器模式的作用是解决两个软件实体间的接口不兼容的问题。使用适配器模式之后，原本由于接口不兼容而不能工作的两个软件实体可以一起工作。
```js
	var googleMap = {
		show: function(){
			console.log( '开始渲染谷歌地图' );
		}
	};
	var baiduMap = {
		display: function(){
			console.log( '开始渲染百度地图' );
		}
	};
	var baiduMapAdapter = {
		show: function(){
			return baiduMap.display();

		}
	};
	var renderMap = function( map ){
	    if ( map.show instanceof Function ){
		map.show(); 
	    }
	};

	renderMap( googleMap ); // 输出：开始渲染谷歌地图
	renderMap( baiduMapAdapter ); // 输出：开始渲染百度地图
```



