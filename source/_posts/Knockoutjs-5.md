---
title: Knockout.js学习(五)
date: 2016-10-07 12:39:27
tags: javascript knockout mvm
---
# 前言
> All JavaScript libraries are legally required to offer a "task list" example (note: that's a joke) so here's one. Take a moment to play with it - add and remove some tasks - and read through the code to understand how it works. It's pretty basic and predictable. Next, you'll load some initial task list from the server, and then see two different ways to save modified data back to the server

官网说，所有的js库都会有一个task List的eg，（我怎么不知道这个梗？）
于是，我们也以task list来结束ko的基础学习。

## Loading and Saving Data
ko是一个很纯粹的client-side的js库，因此，它可以很灵活的和很多服务器端的语言相结合，只要服务器端可以接受/发送JSON数据，那么，就可以和ko结合使用。

### Loading Data from Server
和其他的js类库没有什么区别，使用$.ajax(),$.getJSON()方法均可
``` html
   self.tasks = ko.observableArray([]);
   $.getJSON("/tasks", function(allData) {
    	var mappedTasks = $.map(allData, function(item) { return new Task(item) });
    	self.tasks(mappedTasks);
    });    
```

### Saving Data to Server
同load数据类似，有两种方式，一种是通过form表单提交：
```html
<form action="/tasks/saveform" method="post">
    <input type="hidden" name="tasks" data-bind="value: ko.toJSON(tasks)" />
    <button type="submit">Save</button>
</form>
```
一种是通过$.ajax:
``` Javascript
    self.save = function() {
    	$.ajax("/tasks", {
    		data: ko.toJSON({ tasks: self.tasks }),
    		type: "post", contentType: "application/json",
    		success: function(result) { alert(result) }
    	});
    };
```

### tips
唯一有一点特别的地方，就是ko提供了destroy方法，以便将数据提交回服务器的时候，很清楚的知道这题数据是要删除的，而不需要服务器端再去判断这条数据到底是update，还是insert还是delete。
这样的话稍微修改一下代码：
``` Javascript
// 计算还有多少任务没有完成时，不再统计删除的
 self.incompleteTasks = ko.computed(function() {
    	// return ko.utils.arrayFilter(self.tasks(), function(task) { return !task.isDone() });
    	return ko.utils.arrayFilter(self.tasks(), function(task) { return !task.isDone() && !task._destroy });
    });

 // 不再使用remove而是使用destroy
 // self.removeTask = function(task) { self.tasks.remove(task) };
    self.removeTask = function(task) { self.tasks.destroy(task) };

```
另外，使用destroy删除的对象，会有一个“_destroy”属性，值为true。
这样，在客户端和服务器端均可根据这个属性很方便的判断了。

国际惯例：[源码](https://github.com/nicky-lau/Knockoutjs/tree/master/LoadingAndSavingData)

