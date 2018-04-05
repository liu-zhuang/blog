---
title: Vue-js组件
date: 2017-02-03 15:26:15
tags: Vue Vue2.0 组件
---

# 前言
Vue除了数据绑定之外，还有一个很强大的功能就是组件。
在本文中，暂时先不管以`.vue`为后缀的组件文件，讲讲组件的基本用法


# 注册
有全局和局部两种注册组件的方式。

## 全局注册
顾名思义，就是这个组件在所有的Vue实例中都可以使用。
通过`Vue.component(tagname,option)`的形式。例如：
``` javascript
Vue.component('my-component',{
// 选项 诸如template等等
})
```

> 注意：tagname根据w3c要求，最好是小写加一个短杠

另外，即使是全局注册，也一定要保证组件注册在vue实例声明之前，否则在这个vue实例中是无法使用该组件的。

比如:
``` javascript 
var vm1 = new Vue({
el: "#div1"
});
// 组件注册在实例声明的下面
Vue.component("my-component",{
template: "<div>this is a component</div>"
})

```
这时如果在页面上的`div1`里面使用`<my-component></my-component>`,Vue将会报错说该组件没有正确注册

## 局部注册
不在全局注册组件，而是在实例的选项中注册，使组件仅在一个实例/组件的作用域可用：
``` javascript 
var myComponent = {
	template: '<div>this is a component</div>'
};
var vm1 = new Vue({
	el: '#div1',
	components: {
	'my-component': myComponent
}
});
```

# data 必须是函数
组件的选项几乎和vue的选项一模一样，除了`data`属性必须为函数:
``` javascript
Vue.component('my-component',{
	template: '<div>{{msg}}}</div>',
	data: function () {
	return {
		msg: 'this is my-component'
};
}
})
```
这是为了让每个data有自己独立的实例，否则所有的组件将共用同一个data

# 构成组件
为了将组件间解耦，父子组件间的通信以以下方式进行：
父组件通过`props`将数据传递给子组件，子组件通过`$emit`触发事件给父组件发送消息。

## 单向数据流
`props`是单向绑定的：当父组件的属性变化时，将传导给子组件，但是不会反过来。另外，当每次父组件更新时，子组件中的`props`均会更新为最新值，因此，vue规定绝对不要在子组件中修改`props`的值(`props`是数组或者对象，也就是引用类型，子组件和父组件都指向同一个内存空间，在子组件中改变会影响父组件的状态)

那么问题来了，有可能从父组件传入的`props`数据并不是子组件中直接能用的数据，或者确实需要修改要怎么办呢？
官方给出了两种解决方案：
方案一：定义一个局部变量，并用`props`的值初始化它
``` javascript 
props: ['propData'],
data: function () {
	return {
	 componentData: this.propData
}
}
```
方案二：定义一个计算属性，来返回组件中所需要的数据
``` javascript 
props: ['propData'],
computed: {
	computedData: function () {
	  return this.propData.trim();
}
}
```

## camerlClass vs kebab-case (驼峰式命名和短横线隔开式命名)
HTML特性是不区分大小写的，因此，当使用的不是字符串模板时，驼峰式命名的prop需要转为相对应的短横线隔开式命名：
``` html 
Vue.component('my-component',{
	template: '<div>{{myMessage}}</div>',
	props: ['myMessage']
});

<my-component :my-message-"this is prop data"></my-component>
```

## Prop验证
当组件给很多人使用时，可能需要为组件的`props`指定验证规格。如果传入的数据不符合规格，vue会给出警告。
要为`props`验证规格时，就不能数组的形式接收`props`了,而是要以对象的形式:
``` html
function UserInfo (id,name) {
	this.Id = id;
	this.Name = name;
}
var lz = new UserInfo(1,'liuzhuang');
var xyy = {id:2,name:'xyy'};

var myComponent = {
	template: '<div>{{propNull}}-{{propNumber}}-{{propRequired}}-{{propDefault}}--{{propDefaultFunc.message}}-{{propCustomValidate}}-{{propCustomType}}</div>',
	props: {
		propNull: null, // 不做任何验证
		propNumber: Number, // Number验证
		propMuli: [String, Number], // 多种类型
		propRequired: {
			type: null,
			required: true
		}, // 类型随意，必填
		propDefault: {
			type: Number,
			default: 100
		}, // 有默认值的
		propDefaultFunc: {
			type: Object,
			default: function () {
				return {message:'this is default message'};
			}
		}, // 默认值是一个函数的
		propCustomValidate: {
			type: Number,
			validator: function (val) {
				return val > 10;
			}
		}, // 自定义验证
		propCustomType: {
			type: UserInfo
		} // 自定义类型验证 使用Vue内部使用instanceof方法check
	}
};


var vm1 = new Vue({
	el: '#div2',
	components: {
		'my-component': myComponent
	},
	data: {
		propNull: {a:1},
		propNumber: 1,
		propRequired: 1,
		propCustomValidate:11,
		user: lz
	}
});

<div id="div2">
	<my-component :prop-null="propNull" :prop-number="propNumber" :prop-required="propRequired"
	:prop-custom-validate="propCustomValidate" :prop-custom-type="user"></my-component>
</div>
```

上述代码最终在屏幕显示为：`{ "a": 1 }-1-1-100--this is default message-11-{ "Id": 1, "Name": "liuzhuang" }`

附录：type可以是以下原生构造器：
- String
- Number
- Boolean
- Function 
- Object
- Array

# 自定义事件
上面讲了父组件通过`prop`向子组件传递数据，接下来就要说说如何从子组件将数据传递回父组件了

首先有个基本概念，就是每个Vue实例都实现了事件接口： 
- 使用$on(eventName) 监听事件
- 使用$emit(eventName) 触发事件

注意，这个$on要和`v-on`区分开来
因为，父组件不能直接通过`$on`来监听子组件的事件，只能在使用子组件的地方，通过`v-on`来监听子组件内部通过`$emit`触发的事件。

##  给组件绑定原生事件
有时候，你可能想在某个组件的根元素上监听一个原生事件。可以使用`.native`修饰`v-on`:
``` javascript
<my-component v-on:click.native="doTheThing"></my-component>
```
## 使用自定义事件的的表单输入组件
说白了就是怎么在组件上实现`v-model`的双向绑定

首先要知道一个基础知识：
```
<input v-model="something">
```
上面这个只不过是以下内容的语法糖：
```
<input v-bind:value="something" v-on:input="something = $event.target.value">
```
把这个逻辑用在组件中就是：
``` 
<custom-input v-bind:value="something" v-on:input="something = arguments[0]"></custom-input>
```
首先给组件绑定value为"something",然后在触发"input"事件时，将事件的值返回给"something"

接下来看看一个简单的例子：
