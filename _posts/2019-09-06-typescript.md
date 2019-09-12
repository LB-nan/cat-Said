---
layout: post
title: typescript
categories: typescript
description: typescript学习笔记
keywords: typescript
---

typescript学习笔记，跟着学习记录，不断完善。

### 1、typescript简介

> 一个开源的语言。

> typescript是JavaScript的超集。

> typescript运行在浏览器的话需要一些插件转换，因为浏览器只认前端三剑客。

> typescript是强类型语言。

> 是属于微软的，所以推荐用VSCODE编辑器

安装：`npm install -g typescript`

使用：

> 转换单个文件: `tsc tsFileName.ts`  

> 多个文件：`tsc --init`
> 生成了一个`tscconfig.json`的文件
> 然后只需要输入`tsc`命令就好了
> 使用tsc命令会在终端看到报错的changelog

> 插件，vscode `TypeScript Auto Compiler`
> `tscconfig.json`初始化一个config文件，然后修改ts文件的时候保存完自动转换为js文件了。


### 2、语法规则

1. 不能更改变量为非原有类型的数据。ps: 强类型语言,不会像JavaScript一样进行隐式类型转换。推荐写法：`let num: number = 25;` 把数据类型标注出来，比较清晰。

### 3、基本数据类型

1. Number: 整型，浮点型，二进制，八进制，十六进制 `let num: number = 25; || let num = 25;`
2. bboolean: 布尔值。 `let bo: boolean = false;`
3. string: 字符串 `let str: string = 'hello';`
4. any：任何类型。类似于js了 `let anything: any = 任何值` 尽量不用

### 4、 数组

1. 使用 `let arr:Array<number> = [1,2.1,3,5,6] || let arr: number[] = [1,2.1,3,5,6]`
2. 声明一个数组类型，需要在`<>`里面声明数组值的类型
3. 可以通过数组的下标拿到值
4. 修改：`arr[0] = 'abc'`
5. any类型可以存储任何类型。 `let anyArr: any[] = [1, '1', true]; `

### 5、元组

1. 数组的每一项的类型和值需一致 `let colors: [string, number] = ['red', 255];`


### 5、枚举 enum

它用于声明一组命名的常数，当一个变量有几种可能的取值时，可以将它定义为枚举类型。通常只将枚举元素数值赋给枚举类型的变量,但也可以将任何长整数数值赋给该变量。

1. 可以通过`enum`关键字自己定义类型

```
  enum Color{
    black,  // 0
    yellow = 100, // 100
    red, // 101
    ...
  }

  let myColor: Color = Color.balck

  console.log(myColor);  // 0
```






