---
title: Konckout.js学习(二)
date: 2016-09-25 22:18:33
tags: javascript knockout mvm
---
## 前言
话不多说，继续官网的Tutorial，第二站：Working with Lists and Collections.
> Very often, you'll want to generate repeating blocks of UI elements, especially when displaying lists where the user can add and remove elements. Knockout lets you do that easily, using observable arrays and the foreach binding.

官方如是说，当你想动态的增加或删除列表上的行的时候，使用ko的observable arrays可以很方便的帮你实现。

## Getting started
直接上代码吧，实现一个简单的将数组以table的形式显示。
```HTML
<h2>Your seat reservations</h2>
<table>
	<thead>
		<th>Passenger Name</th>
		<th>Meal</th>
		<th>Surcharge</th>
	</thead>
	<!-- 使用foreach来绑定数组到table上 -->
	<tbody data-bind="foreach:seats">
		<tr>
			<!-- 绑定对象的各个属性 -->
			<td data-bind="text:name"></td>
			<td data-bind="text:meal.mealName"></td>
			<td data-bind="text:meal.price"></td>
		</tr>
	</tbody>
</table>

<script>
		// SeatReservation构造函数
		var SeatReservation = function(name,initialMeal){
			var self = this;
			self.name = name;
			self.meal = initialMeal;
			// 先不使用双向数据绑定
			// self.meal = ko.observable(initialMeal);
		};

		var ReservationViewModel = function(){
			var self = this;
			// 一个JavaScript的数组对象提供meal data
			self.availabelMeals = [
			{mealName:'Standard(sandwich)',price:0},
			{mealName:'Premium (lobster)',price:0},
			{mealName:'Ultimate (whole zebra)',price:0}
			];
			// 使用ko.observableArray来监视数组
			self.seats = ko.observableArray([
				new SeatReservation('lz',self.availabelMeals[0]),
				new SeatReservation('xyy',self.availabelMeals[1])
				]);
		};
		ko.applyBindings(new ReservationViewModel());
	</script>
```
上述代码实现了最基本的数组绑定table。
接下来展示双向绑定数组的时候到了。。。。
结合上Introduction中的demo，把上面的代码做一些调整

```HTML
	<!-- 在html增加这个table ,用以动态增加seats-->
	<table>
		<tr>
			<td>name:</td><td><input type="text" data-bind="value:addName"></td><td>
			<select data-bind="options:availabelMeals,
			optionsCaption:'Choose...',
			optionsText:'mealName',
			value:addMeal"></select></td>
		</tr>
		<tr><td colspan="3">
			<button data-bind="click:addSeat">add</button>
		</td></tr>
	</table>
	<!-- 在js中增加以下代码，以实现动态增加seats中的值 -->
	<script>
		self.addName = ko.observable();
		self.addMeal = ko.observable();
		self.addSeat = function(){
			debugger;
			var sr = new SeatReservation(self.addName(),self.addMeal());
			self.seats.push(sr);
		};
	</script>
```
可以发现，动态向seats数组中增加元素后，table的行自动增加了。

接下来，使表格可编辑

```HTML
	<tbody data-bind="foreach:seats">
		<tr>
			<td><input type="text" data-bind="value:name"></td>
			<td><select data-bind="options:$root.availabelMeals,
				optionsText: 'mealName',
				value:meal"></select></td>
				<td data-bind="text:formattedPrice"></td>
			</tr>
		</tbody>

		<script>
		// SeatReservation构造函数
		var SeatReservation = function(name,initialMeal){
			var self = this;
			self.name = name;
			// self.meal = initialMeal;
			// 现在需要双向数据绑定了
			self.meal = ko.observable(initialMeal);
			self.formattedPrice = ko.computed(function(){
				var price = self.meal().price;
				return price?"$"+price.toFixed(2):"None";
			});
		};
	</script>
```

接下来实现动态移除table上的seat

```HTML
<!-- 在tr里面加上一列 -->
<td><a href="#" data-bind="click:$root.remove">remove</a></td>
<script>
	// viewModel中增加remove方法
	self.remove = function(seat){
		self.seats.remove(seat);
	};
</script>
```
就实现了点击remove按钮移除该行的事件。

最后，再为这个表格增加一个汇总目前座位数和总金额的功能，当总金额大于0时才显示出来
同时规定座位数最多只能有5个（也就是等于5之后，不能再add）
```HTML
	<!-- 在表格上方增加以下代码 -->
	<h2>Your seat reservations(<span data-bind="text:seats().length"></span>)</h2>
	<h3 data-bind="visible:calcTotal()>0">
		total price is $<span data-bind="text:calcTotal().toFixed(2)"></span>
	</h3>

	<!-- 修改add按钮，增加enable -->
	<button data-bind="click:addSeat, enable:seats().length<5">add</button>
	<script>
		// 在viewModel中增加这个方法
		self.calcTotal = function(){
				var total  = 0,
						i  = 0,
				   length  = self.seats().length;
				for(;i<length;i++){
					total += self.seats()[i].meal().price;
				}
				return total;
			};
	</script>
```
这样就能实现动态计算总金额的功能了。

好了，简单的list绑定就结束了。
老样子，[源代码](https://github.com/nicky-lau/Knockoutjs/tree/master/List)