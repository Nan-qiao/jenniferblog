---
title: ES6 模块化
date: 2023-02-22 19:54:12
tags: 
    - ES6 
    - 模块化
categories: 
    - 前端开发
    - (一)编程基础
    - 1.编程语言
    - JavaScript
    - ES6+(6、7、8)
cover: https://pic.imgdb.cn/item/640c2ab5f144a01007cfb82f.webp
---
## node.js 中实现模块化

node.js  遵循了 **CommonJS** 的模块化规范。其中：

- 导入其它模块使用 require() 方法
- 模块对外共享成员使用 module.exports 对象

模块化的好处：

大家都遵守同样的模块化规范写代码，降低了沟通的成本，极大方便了各个模块之间的相互调用，利人利己。

## 前端模块化规范的分类

在 ES6 模块化规范诞生之前，JavaScript 社区已经尝试并提出了 AMD、CMD、CommonJS 等模块化规范。

但是，这些由社区提出的模块化标准，还是存在一定的差异性与局限性、并不

是浏览器与服务器通用的模块化

标准，例如：

- AMD 和 CMD 适用于浏览器端的 Javascript 模块化

- CommonJS 适用于服务器端的 Javascript 模块化

太多的模块化规范给开发者增加了学习的难度与开发的成本。因此，大一统的 ES6 模块化规范诞生了！

## 什么是ES6模块化规范

ES6 模块化规范是浏览器端与服务器端通用的模块化开发规范。它的出现极大的降低了前端开发者的模块化学

习成本，开发者不需再额外学习 AMD、CMD 或 CommonJS 等模块化规范。

ES6 模块化规范中定义：

- 每个 js 文件都是一个独立的模块
- 导入其它模块成员使用 import 关键字
- 向外共享模块成员使用 export 关键字

## 在 node.js 中体验 ES6 模块化

node.js 中默认仅支持 CommonJS 模块化规范，若想基于 node.js 体验与学习 ES6 的模块化语法，可以按照

如下两个步骤进行配置：

① 确保安装了 v14.15.1 或更高版本的 node.js

② 在 package.json 的根节点中添加 "type": "module" 节点

## ES6 模块化的基本语法

ES6 的模块化主要包含如下 3 种用法：

① 默认导出与默认导入

② 按需导出与按需导入

③ 直接导入并执行模块中的代码

### 默认导出 - 默认导入

- 默认导出的语法：export default  默认导出的成员；注意事项：每个模块中，只允许使用唯一的一次 export default，否则会报错！

```js
let n1 = 10	//定义模块私有成员 n1
let n2 = 12 // 定义模块私有成员 n2（外界访问不到 n2，因为她没有被共享出去）
function show(){} //定义模块私有方法 show

export default{// 使用 export default 默认导出语法，向外共享 n1 和 show 两个成员
    n1,
    show
}
```

- 默认导入的语法：import  接受名称  from  ‘模块标识符’；注意事项：默认导入时的接收名称可以任意名称，只要是合法的成员名称即可

```js
// 从 01_m1.js 模块中导入 export default 向外共享的成员
// 并使用 m1 成员进行接收
import m1 from './01_m1.js'

// 打印输出的结果为：
//{ n1: 10, show: [function: show] }
console.log(m1)
```

### 按需导出 - 按需导入

- 按需导出的语法：export 按需导出的成员

```js
// 当前模块为 03_m2.js

// 向外按需导出变量 s1
export let s1 = 'aaa'
// 向外按需导出变量 s2
export let s2 = 'ccc'
// 向外按需导出方法 say
export function say() {}
```



- 按需导入的语法： import { s1 } from ‘模块标识符’

```js
// 导入模块成员
import {s1, s2, say} from './03_m2.js'

console.log(s1) //打印输出 aaa
console.log(s2) //打印输出 ccc
console.log(say) //打印输出 [Function: say]
```

- 按需导出与按需导入的注意事项
  - 每一个模块中可以使用多次按需导出
  - 按需导入的成员名称必须和按需导出的名称保持一致
  - 按需导入时，可以使用 as 关键字进行重命名
  - 按需导入可以和默认导入一起使用

### 直接导入并执行模块中的代码

如果只想单纯地执行某个模块中的代码，并不需要得到模块中向外共享的成员。此时，可以直接导入并执行模块代码。

```js
// 当前文件模块为 05_m3.js
// 在当前模块中执行一个 for 循环操作
for(let i = 0; i < 3; i++){
    console.log(i)
}
——————————————————————————————分割线——————————————————————————
// 直接导入并执行模块代码，不需要得到模块向外共享的成员
import './05_m3.js'
```

