---
title: Vue.js基础
date: 2017-01-26 19:50:17
tags: Vue.js Javascript
---

# 前言
  纠结了很久到底是学Vue还是React,然后发现有纠结那么久的时间，说不定一个已经入门了。于是先挑一个基于原生js的框架Vue开始吧（还有个主要原因是Vue的中文官网做的比较小清新，看起来舒服).
  稍微学习了一下，感觉vue和angular1.0真的有很多相像的地方。

# 生命周期
![生命周期](lifecycle.png)

如上图所示，Vue的生命周期一共有"Create","Mount","Update","Destroy"四个步骤。
vue.js提供了以下8个生命周期钩子函数，给我们提供了执行自定义逻辑的机会：
- beforeCreate
- created
- beforeMount
- mounted
- beforeUpdate
- updated
- beforeDestroy
- destroyed
从字面很好理解分别是创建前后，挂接前后，更新前后，销毁前后
注:所谓的mount个人理解就是用vue.js的el去替换dom元素。

用个最简单的demo说明：
html:
``` html
<div id="vm1" >{{data}}</div>
```
javascript:
``` javascript
var vmData = {
	name:"vmData",
	a:"999",
	data:"this is data"
};

var log = function(msg){
	console.log(msg);
};

var vm = new Vue(
{
	el:"#vm1",
	data:vmData,
	beforeCreate:function(){log("beforeCreate")},
	created:function(){log("created")},
	beforeMount:function(){log("beforeMount")},
	mounted:function(){log("mounted")},
	beforeUpdate:function(){log("beofreUpdate")},
	updated:function(){log("updated")},
	beforeDestroy:function(){log("beforeDestroy")},
	destroyed:function(){log("destroyed")}
});
vmData.b = "998";
vm.$watch("a",function(oldVal,newVal){
	console.log("oldVal:" + oldVal + ";newVal:" +newVal);
});
```
在页面初次加载时，会触发"create"和"mount"生命周期钩子：
![create](vue1.png)
在控制台中修改data中的值，以及调用$destroy()方法，分别会触发"update"和"destroy"生命周期钩子:
![update](vue2.png)
![destroy](vue3.png)

另外值得一提的是，官网中的说法叫`每个 Vue 实例都会代理其 data 对象里所有的属性`，通俗点说就是data对象是引用类型，其值是该对象的地址，因此可以看到vmData.data中的值变了的话，vm.data的值也会跟着变，同样的道理，vm.data变了也会引起vmData.data的变化。
但是如上面代码所示,在vmData赋值给vue实例之后，再给vmData增加的属性，并不会被vue实例所引用。
也就是vmData.b在vue实例中是不存在的。

最后再使用vm.$destroy()方法，这样会触发"destroy"生命周期钩子
$destroy()方法官方说法叫：Teardown wathcers,child components and event listeners
就是拆卸监控，也就是解除双向绑定，值的变化不会再引起dom的变化。
![vue4](vue4.png)

# 计算属性
和knockout.js非常类似（所以说vue.js确实是站在巨人的肩膀上，吸收了knockout,angular,react的各家所长)，vue也有计算属性：computed。
如以下代码所示：
``` javascript
var vm3 = new Vue({
	el:"#vm3",
	data:{
		firstName:"liu",
		lastName:"zhuang"
	},
	methods:{
		getFullName:function(){
			console.log("method exec")
			return this.firstName + " " + this.lastName ;
		}
	},
	computed:{
		fullName:{
			get:function(){
				return this.firstName + " " + this.lastName;
			},
			set:function(newVal){
				var names = newVal.split(" ");
				this.firstName = names[0];
				this.lastName = names[names.length-1];
			}
		}
	},
	watch:{
		firstName:function(oldval,newVal){
			console.log("wath exec")
			alert("firstName has changed");
		}
	}
});
```
html代码:
``` html
<div id="vm3">
	{{fullName}}
	<br>
	{{getFullName()}}
</div>
```
可以看到,computed的`fullName`在结果上和methods里面的`getFullName`效果是一样的，根据文档上的说法，计算属性是使用计算缓存的，当计算属性的依赖项发生变化时，计算属性才会重新计算，其他时候都是直接返回缓存中的值。而method总是会执行函数。

另外计算属性默认是get模式，同时也可以采用set模式，这点和knockout.js一模一样
``` javascript
computed:{
	fullName:function(){
	return this.firstName + " " + this.lastName;
}
}
```
结论就是计算属性在大多数情况都更合适，当不需要缓存的时候用method,需要缓存的时候用计算属性computed,有时候需要在数据变化时来响应数据变化，就需要用到$watch.
demo如下：
``` javascript
<script src="../lodash.js"></script>
<script src="../axios.min.js"></script>
var vmQuestion = new Vue({
	el:"#Question",
	data:{
		question:"",
		answer:"I cannot give you an answer until you ask a question!",
		url:"https://yesno.wtf/assets/no/8-5e08abbe5aacd2cf531948145b787e9a.gif"
	},
	methods:{
		getAnswer:_.debounce(function(){
			var vm = this;
			if(vm.question.indexOf("?") === -1){
				vm.answer = "Question usually contain a question mark :-)";
				return false;
			}
			vm.answer = "think...";
			axios.get('https://yesno.wtf/api')
			.then(function(ret){
				vm.answer = ret.data.answer;
				vm.url = ret.data.image;
			})
			.catch(function(err){
				vm.answer = "Error! " + err;
			})
		},500)
	},
	watch:{
		question:function(newVal,oldVal){
			this.answer = "Waiting for you to stop typing...";
			this.getAnswer();
		}
	}
});
```
html:
``` html
<div id="Question">
	<p>
		Ask a yes/no question:
		<input v-model="question">	
	</p>
	<p>{{answer}}</p>
	<img v-bind:src="url" />
</div>
```

# Class绑定
在将v-bind用于class和style的时候，表达式的结果类型出了字符串外，还可以是对象是数组：

## 对象
和其他的框架类似，采用`v-bind:class="{active:isActive}"`的方式
代码如下：
``` javascript
var vmCss = new Vue({
	el:"#divCss",
	data:{
		isActive:true,
		msg:"this is a css/style demo",
		cssClass:{
			active:false
		},
		activeCss:"active",
		selectCss:"selected"
		},
	methods:{},
	watch:{},
	computed:{
		cssComputed:function(){
			return {
				active:this.isActive
			}
		}
	}
});
```
html:
``` html
<div id="divCss"  class="test" v-bind:class="[activeCss,selectCss]">
	{{msg}}
</div>
```
小结如下：
`v-bind:class`可以和普通的class属性共存，如上html所示，最终渲染出来的结果是`class=test active selected`。
`v-bind:class`赋值的对象除了是javascript字面量外，也可以是vue.$data中的值指向的对象，同时还可以是computed计算属性中返回的对象。

## 数组
可以把一个数组传递给`v-bind:class`，以应用一个class列表。（例子如上面对象的demo）
同时也可以在数组中使用对象：`v-bind:class="[{active:isActive},selectCss]"`。
当然同样的道理，也可以使用计算属性:
``` javascript
computed:{
	cssArray:function(){
		return [this.activeCss,this.selectCss];
	}
}
```

# 内联样式绑定
内联样式的绑定和class的绑定几乎没有区别，同样也是可以绑定对象和数组:
`v-bind:style="{color:'red';fontSize:'30px'}"`,有一点要注意的是，css的属性名除了用驼峰法，还可以用短横分割法命名：
`v-bind:style="{color:'blue','font-size':'30px',}"`和`v-bind:style="{color:'blue',fontSize:'30px',}"`的效果是一模一样的。
当然，把v-bind:style绑定到一个样式对象上是个更好的做法：
javascript:
``` javascript
styleObj:{
	color:"yellow",
	"font-size":'40px'
}
``` 
html:
``` html
<div id="divCss"  v-bind:style="styleObj">
	{{msg}}
</div>
```
数组的用法和class的一样，最后值得一提的是：当`v-bind:style`使用需要特定前缀的css属性时，如`transform`，会自动添加前缀。
但是这点没有验证出来，vue是把前缀加在哪里的？
![transform](style1.png)
如图所示，在chrome下，并没有加上`-webkit-`这个前缀。

# 条件渲染
使用`v-if`指令可以很容易的实现按条件渲染。
当然vue比较强大的地方是又扩展来`v-else`和`v-else-if`指令

因为`v-if`是一个指令，需要将它添加到一个元素上，如果需要按条件渲染多个元素的时候，就需要用到`<template></template>`，把多个元素放在`template`内，把`v-if`指令写在`template`上。

Vue尝试尽可能高效的渲染元素，通常会复用已有元素而不是从头开始渲染。这么做除了使 Vue 更快之外还可以得到一些好处。如下例，当允许用户在不同的登录方式之间切换:
``` html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address">
</template>
```
在控制台切换`loginType`,会显示不同的label和plaaceholder,但是在切换的时候，input的内容并不会被删除，两个模版因为使用了相同的元素，因此`<input>`会被复用，仅仅只是替换了placeholder。当然，vue也提供了一种方式让你自己决定是否要复用元素，所要做的就是加一个`key`属性。

## v-show
另一个根据条件渲染元素的指令叫`v-show`，和`v-if`的区别在于`v-show`做的仅仅只是切换元素的css中的`display`属性。
⚠️`v-show`不支持`template`语法

## `v-if` VS `v-show`
`v-if`是真实的条件渲染，因为它会确保条件块在切换当中适当地销毁与重建条件块内的事件监听器和子组件。
`v-if`也是惰性的：如果在初始渲染时条件为假，则什么也不做——在条件第一次变为真时才开始局部编译（编译会被缓存起来）。
相比之下，`v-show`简单得多——元素始终被编译并保留，只是简单地基于 CSS 切换。
一般来说， `v-if` 有更高的切换消耗而 `v-show` 有更高的初始渲染消耗。因此，如果需要频繁切换使用 `v-show` 较好，如果在运行时条件不大可能改变则使用 `v-if` 较好。

# 列表渲染
使用`v-for`指令来根据一组数组来进行元素渲染。主要以`item in items`的语法。

和条件渲染一样，当有多个元素块需要渲染的时候，也可以使用`<template v-for=''></template>`标签来渲染

`v-for`指令除了渲染数组外，也可以渲染对象。

在渲染数组时，`v-for`还支持可选的第二个参数为当前项的索引,`v-for='(item,index) in items'`;
在渲染对象时，`v-for`支持第二个可选参数作为键名，第三个参数作为索引，`v-for='(vale,key,index) in items'`;

`v-for`还可以进行整数迭代：`<span v-for='n in 10'>{{n}}</span>`

为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一 key 属性。理想的 key 值是每项都有唯一 id。这个特殊的属性相当于 Vue 1.x 的 track-by ，但它的工作方式类似于一个属性，所以你需要用 v-bind 来绑定动态值（在这里使用简写）：
``` html 
<div v-for="item in items" :key="item.id">
  <!-- 内容 -->
</div>
```

## 变异方法
Vue 包含一组观察数组的变异方法，所以它们也将会触发视图更新。这些方法如下：
- push()
- pop()
- shift()
- unshift()
- splice()
- sort()
- reserve()

⚠️由于 JavaScript 的限制， Vue 不能检测以下变动的数组：
当你利用索引直接设置一个项时，例如： vm.items[indexOfItem] = newValue
当你修改数组的长度时，例如： vm.items.length = newLength
为了避免第一种情况，以下两种方式将达到像 vm.items[indexOfItem] = newValue 的效果， 同时也将触发状态更新：
``` javascript
// Vue.set
Vue.set(example1.items, indexOfItem, newValue)
// Array.prototype.splice`
example1.items.splice(indexOfItem, 1, newValue)
```
避免第二种情况，使用 splice：
``` javascript
example1.items.splice(newLength)
```
值得一提的是，上面提到的第一种情况只有更新整个item的时候才需要用到
如果是更新item中的一个属性并不需要。
比如:
``` javascript
vm.items[indexOfItem].id = newId;
```
这种写法是👌的。

# 事件处理器
使用`v-on`指令来监听DOM事件来触发一些业务逻辑事件
可以是简单的计数功能：
``` html
<button v-on:click="cnt += 1">click</button>
<p>这个按钮被点击了{{cnt}}次</p>
```

当然最常用的还是javascript的function:
html:
``` html
<div v-on:click.self="warn('parent alert')">
	<div v-on:click.self="warn('middle')">
		<a href="http://liuzhuang.tech" v-on:click.self="warn('prevent')">warn</a>
	</div>
</div>
```
javascript:
``` javascript
var vmOn = new Vue(
{
	el:"#divOn",
	data:{
		cnt:0,
		name:"xueyy"
	},
	methods:{
		greet:function(event){
			alert("hello " + this.name);
			console.log(event);
		},
		warn:function(msg){
			alert(msg);
		}
	}
}
);
```

有时候需要在`v-on`绑定的事件中处理原生dom的事件，则可以使用`$event`将它传入方法：
html:
``` html
<button v-on:click="warn('Form cannot be submitted yet.', $event)">Submit</button>
```
javascript:
``` javascript
// ...
methods: {
  warn: function (message, event) {
    // 现在我们可以访问原生事件对象
    if (event) event.preventDefault()
    alert(message)
  }
}
```
## 事件修饰符
其实上面提到的在方法中处理原生dom事件，主要也就是阻止冒泡之类的方法，为了可以在method只专注于业务逻辑，vue还提供了一些事件修饰符：
- stop
- prevent
- capture
- self
- once
```html
<!-- 阻止单击事件冒泡 -->
<a v-on:click.stop="doThis"></a>
<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>
<!-- 修饰符可以串联  -->
<a v-on:click.stop.prevent="doThat"></a>
<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>
<!-- 添加事件侦听器时使用事件捕获模式 -->
<div v-on:click.capture="doThis">...</div>
<!-- 只当事件在该元素本身（而不是子元素）触发时触发回调 -->
<div v-on:click.self="doThat">...</div>
<!-- the click event will be triggered at most once -->
<a v-on:click.once="doThis"></a>
```

## 按键修饰符
在监听键盘事件时，还需要监听常见的键值，vue允许为`v-on`在监听键盘事件时，添加特殊的键盘修饰符:
``` html
<!-- 只有在 keyCode 是 13 时调用 vm.submit() -->
<input v-on:keyup.13="submit">
```
记住所有的 keyCode 比较困难，所以 Vue 为最常用的按键提供了别名：
``` html
<!-- 同上 -->
<input v-on:keyup.enter="submit">
<!-- 缩写语法 -->
<input @keyup.enter="submit">
```

全部的键盘别名：
- .enter
- .tab
- .delete (捕获 “删除” 和 “退格” 键)
- .esc
- .space
- .up
- .down
- .left
- .right

同时还有一些组合按键：
- .ctrl
- .alt
- .shift
- .meta(window系统下是window键，mac下是command键)
例如：
``` html
<!-- Alt + C -->
<input @keyup.alt.67="clear">
<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

# 表单控件绑定
所谓的表单控件其实就是使用`v-model`指令放在`<input>`和`<textarea>`，`<select>`这些控件上实现双向绑定
demo如下：
html:
``` html
<div id="divModel">
	<input type="text" v-model="message">
	<textarea v-model="message" name="" id="" cols="30" rows="10"></textarea>
	<p style="white-space: pre">{{message}}</p>
	<input type="checkbox" id="cb" v-model="cbChecked">
	<label for="">{{cbChecked}}</label>
	<input type="checkbox" id="cbName1" value="xyy-val" v-model="cbNames">
	<label for="">xyy</label>
	<input type="checkbox" id="cbName2" value="lz-val" v-model="cbNames">
	<label for="">lz</label>
	<input type="checkbox" id="cbName3" value="yebao-val" v-model="cbNames">
	<label for="">yebao</label>
	<p>{{getNames()}}</p>
	<input type="radio" id="rb1" value="xyy" v-model="rbChecked">
	<label for="">xyy</label>
	<input type="radio" id="rb2" value="lz" v-model="rbChecked">
	<label for="">lz</label>
	<p>{{rbChecked}}</p>
	<select name="" id="" v-model="selectItem" multiple>
		<option >xyy</option>
		<option >lz</option>
		<option >yebao</option>
	</select>
	<p>{{selectItem}}</p>
	<br>
	choiseObj:
	<select name="" id="" v-model.number="choiseObj" multiple="">
		<option v-for="item in optionItems" v-bind:value="item.age" @click="warn">
			{{item.name}}
		</option>
	</select>
	<p>{{choiseObj}}</p>
	<input type="radio" v-model="rbVal" v-bind:value="rbValY">
	<input type="radio" v-model="rbVal" v-bind:value="rbValN">
	<label v-text="rbVal"></label>
	<br>
	<input type="checkbox" v-model="cbVal" v-bind:true-value="cbValA" v-bind:false-value="cbValB">
	<label v-text="cbVal"></label>
	<select name="" id="" v-model="choiseItem" multiple="">
		<option v-for="item in optionItems" v-bind:value="item.val">
			{{item.name}}
		</option>
	</select>
	<p>{{choiseItem}}</p>
</div>
```
javascript:
``` javascript
var vmModel = new Vue({
	el:"#divModel",
	data:{
		message:"",
		cbChecked:false,
		cbNames:[],
		rbChecked:"",
		selectItem:[],
		optionItems:[
		{id:1,name:'薛芸莹',age:'18'},
		{id:2,name:'耶包',age:'16'},
		{id:3,name:'呆包',age:'19'},
		],
		choiseItem:[],
		cbVal:"b",
		cbValA:"a",
		cbValB:"b",
		rbVal:"",
		rbValY:"c",
		rbValN:"d",
		choiseObj:[]
	},
	methods:{
		getNames:function(){
			return this.cbNames.toString();
		},
		warn:function(){
			alert("click!");
		}
	}
});
```
归纳总结一下就是checkbox list绑定在同一个数组，就把多个checkbox的`v-model`设置为同一个数组对象，多个radio设置为排他选择，就把多个radio的`v-model`设置为同一个数组对象。
总之如果是多选，`v-model`绑定的对象一定要是个数组
同时可以通过`v-bind`来为“表单控件”设置value值，比如:
``` html
<!-- 复选框 -->
<input
  type="checkbox"
  v-model="toggle"
  v-bind:true-value="a"
  v-bind:false-value="b"
>
<!-- 单选按钮 -->
<input type="radio" v-model="pick" v-bind:value="a">
<!-- 下拉框 -->
<select v-model="selected">
    <!-- 内联对象字面量 -->
  <option v-bind:value="{ number: 123 }">123</option>
</select>
<!-- 当然了，下拉框也可以绑定把value设置为该option的对象（如上面大demo所示),这样就可以直接在选中的时候获取到完整信息了 -->
```

## 修饰符
还可以在`v-model`中使用修饰符来进行一些常用操作：
- .lazy:在默认情况下， v-model 在 input 事件中同步输入框的值与数据 (除了 上述 IME 部分)，但你可以添加一个修饰符 lazy ，从而转变为在 change 事件中同步：
``` html
<!-- 在 "change" 而不是 "input" 事件中更新 -->
<input v-model.lazy="msg" >
```
- .number:如果想自动将用户的输入值转为 Number 类型（如果原值的转换结果为 NaN 则返回原值），可以添加一个修饰符 number 给 v-model 来处理输入值：
``` html
<input v-model.number="age" type="number">
```
- .trim:如果要自动过滤用户输入的首尾空格，可以添加 trim 修饰符到 v-model 上过滤输入：
``` html
<input v-model.trim="msg">
```

至此，Vue2.0的基础功能已经差不多都掌握了，接下去就是最强大的组件功能了。
个人感觉和angular以及knockout相比，确实有种站在巨人的肩膀上的感觉，React不能比较，才刚入门，毕竟使用jsx语法门槛比Vue要高点。

