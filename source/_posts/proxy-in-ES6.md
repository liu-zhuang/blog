---
title: proxy in ES6
date: 2017-06-25 23:37:11
tags: ES6 proxy class reflect
---

# js中的AOP
在其他面向对象的语言中,AOP已经不足为奇了，js作为世界上最好的语言，在ES7中提供了Decorators来实现AOP，但是在这个使用ES6还需要Babel来"转码"的年代，ES7言之尚早，其实可以自己使用Proxy来实现AOP。
其实在java的Spring中，就是利用proxy来实现的AOP和IOC。

# ES6基本语法
包含以下几个ES6语法：
- 模板字符串: ``
- 类: class
- 代理: proxy
- 反射: reflect

## 模板字符串

## 类

## 代理

## 反射

## demo
``` javascript 
{
	// 验证
	function validator(target, validate) {
		return new Proxy(target, {
			_validate: validate,
			set(target, key, value) {
				if(target.hasOwnProperty(key)){
					let validateRet = this._validate[key](value);
					if(validateRet) {
						return Reflect.set(target,key,value);
					} else {
						throw Error(`该属性验证不通过:${key}`);
					}
				} else {
					throw Error(`不存在该属性:${key}`);
				}
			}
		})
	}
	const UserValidate = {
		name(val) {
			return typeof val === "string";
		},
		age(val) {
			return typeof val === "number" && val > 0 && val < 150;
		}
	}
	class UserInfo {
		constructor(name, age) {
			this.name = name; 
			this.age = age;
		}
	}
	let xyy = new UserInfo("xyy", 18);
	// console.log(xyy);
	let proxyXyy = new validator(xyy, UserValidate);
	 // proxyXyy.set("age", 200);
	 // proxyXyy.age = 200;
	 // proxyXyy.name = 200;
	 console.log(proxyXyy);
	}
```
