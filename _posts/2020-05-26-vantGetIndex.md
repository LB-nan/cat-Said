---
layout: post
title: vant的rules匹配规则没有index索引
categories: javascript
description: vant的自定义rules获取index
keywords: vant, index, rules
---

vant的自定义匹配规则并未返回`index`，如果是数组需要判断某一项的值就会出问题，因为找不到单独的那一项，一个不匹配会全部报错。官方文档上没有,这是解决方案，记录一下笔记。

### 1、问题复现
一个`van-field`正常写`rules`的自定义

```js
// html内容  这是for的

<div v-for="(item, index) in arr" :key="item.id">
	<van-field
		v-model="item.a"
		label="领取数量"
		input-align='right'
		:rules="rules.packQuantity"
	/> 
</div>

// rules的规则
rules:{
	packQuantity: [
	   {
	     validator: this.validatorGreenBeanGoodQuantity,
	     message: "领取数量不能大于余量",
	     trigger: "onChange"
	   }
	 ]
}

// 自定义的匹配规则
methods: {
	validatorGreenBeanGoodQuantity(value) {
	  return this.arr.every(item=>{
	  	// arr[{a:10, b: 20}, {a: 20, b: 20}]
	    return item.a <= item.b;
	  })
	}
}
```

这种情况下就会出问题，需要如下面这样返回一个index`validatorGreenBeanGoodQuantity(value, index)`，然后去拿到对应的那一项进行匹配判断就好了。

### 2、解决方案

```js

<van-field
  v-model="item.a"
  label="领取数量"
  input-align='right'
  :rules="getRule(index)"
/>

methods: {
    getRule(index) {
      return [
        {index, validator: this.validator, message: '包材领取数量不能大于余量', trigger: 'onChange'}
      ];
    },
    validator(value, rule) {
      console.log(value, rule)
      return this.arr[rule.index].a <= this.arr[rule.index].b;
    },
}
```

具体的项目示例我放到了github：[https://github.com/LB-nan/vantRules/tree/master/vantrules](https://github.com/LB-nan/vantRules/tree/master/vantrules)

