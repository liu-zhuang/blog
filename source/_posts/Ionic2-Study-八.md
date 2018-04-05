---
title: Ionic2 Study(八)
date: 2016-12-05 23:08:34
tags: Ionic2 Angular2
---
# Ionic2 基础组件之Alert
> An Alert is a dialog that presents users with information or collects information from the user using inputs. An alert appears on top of the app's content, and must be manually dismissed by the user before they can resume interaction with the app. It can also optionally have a title, subTitle and message.
You can pass all of the alert's options in the first argument of the create method: create(opts). Otherwise the alert's instance has methods to add options, such as setTitle() or addButton().

Alert组件是一个用来收集用户信息的弹出对话框，出现在app的最顶层，只有用户手动把它关闭之后，才能继续和app进行交互。它有可选的title，subTitle以及message，可以在初始化alert的时候在create方法中设置这些参数，也可以对alert的实例使用诸如setTitle()或addButton()等方法来设置这些属性。
上面是官方直译版，民间意译就是，alert组件其实就是我们js中常用的alert，只不过它有很多扩展属性，可以设置按钮，设置input等等。。（有点类似于modal)

## Alert的options
|        Property       |   Type  |                    Description                    |
|-----------------------|---------|---------------------------------------------------|
| title                 | string  | alert的标题                                       |
| subTitle              | string  | alert的副标题                                     |
| message               | string  | alert的内容                                       |
| cssClass              | string  | 自定义样式，以空格分割（但是暂时没研究出来怎么用) |
| inputs                | array   | alert中的input组合，具体见下面的说明              |
| buttons               | array   | alert中的按钮组合，具体见下面的说明               |
| enableBackdropDismiss | boolean | 是否允许按空白处关闭alert                         |

# Alert Buttons
在Alert组件中，有一个buttons属性，既然带s了，显而易见是个数组，因此这个属性的值当然是个数组。
比较形象的类比就是js中的confirm,会有两个按钮，“是”和“否”，在这里就是在buttons中设置["yes","no"],当然了，这是最基础的设置。
> In the array of buttons, each button includes properties for its text, and optionally a handler. If a handler returns false then the alert will not automatically be dismissed when the button is clicked. All buttons will show up in the order they have been added to the buttons array, from left to right. Note: The right most button (the last one in the array) is the main button.
Optionally, a role property can be added to a button, such as cancel. If a cancel role is on one of the buttons, then if the alert is dismissed by tapping the backdrop, then it will fire the handler from the button with a cancel role.

在按钮组合中，每个按钮都有`text`属性，和可选的`handler`属性，当h某个按钮的handler返回`false`时，按下该按钮后，alert组件不会被关闭。
按钮组合按先后顺序从左到右排列在alert组件的最下面。 Note:最右边的按钮即数组中最后一个按钮被认为是主要按钮。
按钮还有一个可选属性叫做`role`,要么被赋值为`cancel`,要么被赋值为`null`,当被赋值为`cancel`的时候，如果alert因点击背景而被关闭，也会触发该按钮的handler事件。

## Alert Button 的options

| Property |  Type  |                              Description                              |
|----------|--------|-----------------------------------------------------------------------|
| text     | string | 按钮上显示的文本                                                      |
| handler  | any    | 官方文档给出的是any，我认为应该是个function,用来处理该按钮的click事件 |
| cssClass | string | 该按钮的样式                                                          |
| role     | string | 按钮的角色，`null`或`cancel`                                          |

# Alert Inputs
> Alerts can also include several different inputs whose data can be passed back to the app. Inputs can be used as a simple way to prompt users for information. Radios, checkboxes and text inputs are all accepted, but they cannot be mixed. For example, an alert could have all radio button inputs, or all checkbox inputs, but the same alert cannot mix radio and checkbox inputs. Do note however, different types of "text"" inputs can be mixed, such as url, email, text, etc. If you require a complex form UI which doesn't fit within the guidelines of an alert then we recommend building the form within a modal instead.

Alert组件也可以包含不同的input组件，用来和app交互信息.包括text input,radio,checkbox等。
但是类型不能混合，也就是说要么全部都是text input,要么全部都是radio,或者全部都是checkbox，如果有需要混合使用的场合，建议创建一个form，使用modal来代替。

## Alert Input 的options
|   Property  |   Type  |                      Description                      |
|-------------|---------|-------------------------------------------------------|
| type        | string  | `text`,`radio`,`checkbox`等                           |
| name        | string  | 控件的name                                            |
| placeholder | string  | placeholder的内容（只有当类型为text或number时才生效)  |
| value       | string  | value                                                 |
| label       | string  | input的label(只有当类型为`radio`或`checkbox`时才生效) |
| checked     | boolean | 是否选中                                              |
| id          | string  | id                                                    |


# demo
根据上面的文档，写两个基础的alert
``` typescript
	if(this.userInfo.UserName == "" ){
			let alertUserNameError = this.alertCtrl.create({
				title:"Login Info",
				subTitle:"please enter a valid UserName",
				message:'UserName can not be empty...',
				buttons:["OK"],
				cssClass:'danger',
				inputs:["text","password"],
				enableBackdropDismiss:false
			});
			alertUserNameError.present();
		} else if(this.userInfo.PassWord == ""){
			let alertPwdError = this.alertCtrl.create({
				title:'Login Info',
				subTitle:'please enter a valid Password',
				message:'password can not be empty...',
				buttons:[{
					text:'I see',
					handler:()=>{
						console.log('tap i see!');
						return false;
					},
					cssClass:"color:danger"
				},{
					text:'Cancel',
					handler:()=>{
						console.log('tap cancel!');
					},
					cssClass:'danger',
					role:'null'
				}],
				inputs:[{
					type:'checkbox'
				},{
					type:'checkbox'
				}]
			});
			alertPwdError.present();
		} 
		else {
			this.loadDefault();
		}
	}
```

# 源码
[代码地址](https://github.com/nicky-lau/Ionic2-demo/tree/add-alert)

# 补充说明
> Dismissing And Async Navigation
After an alert has been dismissed, the app may need to also transition to another page depending on the handler's logic. However, because multiple transitions were fired at roughly the same time, it's difficult for the nav controller to cleanly animate multiple transitions that may have been kicked off asynchronously. This is further described in the Nav Transition Promises section. For alerts, this means it's best to wait for the alert to finish its transition out before starting a new transition on the same nav controller.
In the example below, after the alert button has been clicked, its handler waits on async operation to complete, then it uses pop to navigate back a page in the same stack. The potential problem is that the async operation may have been completed before the alert has even finished its transition out. In this case, it's best to ensure the alert has finished its transition out first, then start the next transition.

``` typescript
let alert = this.alertCtrl.create({
  title: 'Hello',
  buttons: [{
    text: 'Ok',
    handler: () => {
      // user has clicked the alert button
      // begin the alert's dismiss transition
      let navTransition = alert.dismiss();

      // start some async method
      someAsyncOperation().then(() => {
        // once the async operation has completed
        // then run the next nav transition after the
        // first transition has finished animating out

        navTransition.then(() => {
          this.nav.pop();
        });
      });
      return false;
    }
  }]
});

alert.present();
```

> It's important to note that the handler returns false. A feature of button handlers is that they automatically dismiss the alert when their button was clicked, however, we'll need more control regarding the transition. Because the handler returns false, then the alert does not automatically dismiss itself. Instead, you now have complete control of when the alert has finished transitioning, and the ability to wait for the alert to finish transitioning out before starting a new transition.

官方文档中还大篇幅的写了关闭alert和异步操作的一些注意事项。
大体意思就是说，在你按下alert组件中的按钮后，可能会有一些异步事件，给出了一些解决方案和思路。
比如说，可以在handler中`return false`,这样的话，就不会一按下按钮就关闭alert了。
然后给出了一个利用promise的demo。(正好借这个机会把promise也好好学习一下，就不在这里展开了。)