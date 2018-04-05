---
title: How To(Safely) Usa A jQuery Plugin With Vue.js
date: 2017-09-18 16:38:23
tags: vue jQuery 
---

![pic](jQueryInVue.jpeg)

[原文地址](https://medium.com/js-dojo/how-to-safely-use-a-jquery-plugin-with-vue-js-786acdfb743b)

# 前言
在同一个UI中同时使用jQuery和Vue并不是一个好主意，如果可以避免的话，尽量不要这样做。
但是人在江湖身不由己，现实工作中也会遇到客户强烈坚持你使用某个特定的jQuery插件，但是你却没有时间去把这个插件Vue化的情况。
不过虽说勉强没有幸福，但是如果你以正确的方式使用，江湖也不乏勉强在一起却成为神仙眷侣的传说，本文就将用在Vue项目中使用jQjery的Datepicker组件来证明这点。
同时还将展示如何在jQuery组件和Vue中传递数据。

本文用到的插件是：[bootstrap-datepicker](https://bootstrap-datepicker.readthedocs.io/en/latest/)

[示例代码](https://github.com/liu-zhuang/jquery-vue)

# 同时使用Vue和jQuery的问题
简单的说Vue是通过虚拟Dom来渲染Dom的，而jQuery是直接操作Dom树，这也就意味着当通过jQuery改变了一个Vue控制的元素，比如给该元素增加一个class，Vue并不会关心这些改变，同时在下一个声明周期中会将其覆盖。

# 解决方案： 使用组件来包裹
既然知道了Vue和jQuery永远不会共享对Dom的操作，我们必须告诉Vue来封锁一块区域，并将其传递给jQuery
使用组件来包裹jQuery插件有以下几个原因：
- 可以利用生命周期钩子函数来加载和卸载jQuery组件
- 可以使用props和event来进行vue与jQuery之间的通信
- 可以使用v-once来避免多次更新

# 设置jQuery组件date-picker
很显然，你需要先引用jQuery库和datepicker库，引用之后，现在页面上简单的做下测试
``` javascript
Date: <input id="datepicker"/>

$('#datepicker').datepicker();

```
显而易见，datepicker组件会正常工作

# 实现Datepicker 组件
如同上面的使用方式，datepicker组件模板中也只需要`input`一个元素:
```
Vue.component('date-picker', function() {
  template: '<input/>'
});
new Vue({
  el: '#app'
});
```
在页面上使用时，只需要如下方法：
```
<div id="app">
  Date: <date-picker></date-picker>
</div>
```
注意： 这个组件除了wrapper之外不要有任何其他属性，不要加诸如"data-id"之类的属性， 也不要 或者使用指令(directive)或插槽(slot)

# 实例化组件
在Vue中，不需要通过给这个input指订Id来选择它，我们可以简单的使用`this.$el`来实现选择器的功能。然后就像在jQuery中做的那样把它"jQuery"化，
再调用`datepicker()`方法来实例化。
请注意，必须在`mounted`这个钩子函数中来进行实例化操作，(因为在此之前`this.$el`还是undefined)
```
Vue.component('date-picker', function() {
  template: '<input/>',
  mounted: function() {
    $(this.$el).datepicker();
  }
});
```

# 卸载组件
如同实例化一样，我们也是在生命周期钩子函数中来进行操作,不过这次选择了`beforeDestroy`,因为在`destory`中它已经undefined了。
```
Vue.component('date-picker', {
  template: '<input/>',
  mounted: function() {
    $(this.$el).datepicker();
  },
  beforeDestroy: function() {
    $(this.$el).datepicker('hide').datepicker('destroy');
  }
});
```

# 使用props传递参数
为了使组件可复用，有必要传递一些自定义的config参数给组件，例如format，我们可以使用props
``` javascript
Vue.component('date-picker', {
  template: '<input/>',
  props: [ 'dateFormat' ],
  mounted: function() {
    $(this.$el).datepicker({
      dateFormat: this.dateFormat
    });
  },
  beforeDestroy: function() { ... }
});


<div id="app">
  <date-picker date-format="yy-mm-dd"></date-picker>
</div>

```

# 实现双向绑定
这段原文写的意思就是可以通过双向绑定来实现给props赋值，但是，一旦传给props的值变化了，就会引起jQuery组件的变化，这不是我们希望看到的，因此我们使用了`v-once`这个指令，[详情](https://cn.vuejs.org/v2/api/#v-once)这个指令简单说就是只渲染一次。


# 和组件的通信
上面实现了通过props向组件传值，接下来要实现的就是接受组件向外的传值了。
其实实现原理也很简单，在`changeDate`事件的时候，派发一个事件，然后在组件上监听该事件即可。
```
<date-picker @update-date="onUpdateDate" :date-format="dateformat" :autoclose="autoclose" v-once></date-picker>

Vue.component('date-picker', {
	template: '<input />',
	props: {
		dateFormat: {
			type: String,
			default: 'yyyy-mm-dd'
		},
		autoclose: {
			type: Boolean,
			default: false	
		}
	},
	mounted () {
		$(this.$el).datepicker({
			language: 'zh-CN',
			format: this.dateFormat,
			autoclose: this.autoclose,
		});
		$(this.$el).datepicker()
		.on("changeDate", (event) => {
			debugger;
			this.$emit('update-date', event.date);
		});
	},
	beforeDestroy () {
		$(this.$el).datepicker('hide').datepicker('destroy');
	}
});


let vm = new Vue({
			el: "#app",
			data () {
				return {
					dateformat: 'yyyy-mm-dd',
					autoclose: true,
					date: ''
				}
			},
			methods: {
				onUpdateDate (val) {
					const temp = new Date(val);
					this.date = [temp.getFullYear(), temp.getMonth()+1, temp.getDate()].join('-');
				}
			}
		});

```


至此，在Vue中使用jQuery组件就实现了。