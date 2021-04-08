# JavaScript

> JavaScript（简称“JS”） 是一种具有函数优先的轻量级，解释型或即时编译型的编程语言。虽然它是作为开发Web页面的[脚本语言](https://baike.baidu.com/item/脚本语言/1379708)而出名，但是它也被用到了很多非浏览器环境中，JavaScript 基于原型编程、多范式的动态脚本语言，并且支持面向对象、命令式和声明式（如函数式编程）风格。

[TOC]



## 数据类型

`number` 

> `JS`不区分小数和整数
>
> 浮点数存在精度问题,一般使用如下方法解决：	
>
> ​		`Math.abs(1/3 - (1-2/3) < 0.0000001)`
>

``` javascript
NaN - not a number
//NaN与所有的数值都不相等，包括自己，只能使用isNaN(NaN)来测试
Infinity - 无穷大
```

`String`

``` javascript
    // ` 可以实现多行字符串
    var str = `sasd
                asd`
    //模板字符串
    var name = "0.0"
    let a = `you are ${name}`
    console.log(a)//you are 0.0
	
	//字符串可变性
    let student = "stuDent"
    student[0] = 1
    console.log(student)//stuDent string说明不可变

    console.log(student.toLowerCase())//student
    console.log(student.toUpperCase())//STUDENT
    console.log(student.indexOf('t'))//1
    console.log(student.substring(1,3))//tu
```

`Boolean`

> 只有`true  ` `false`

`逻辑运算符`

> `=` 赋值
>
> `==` 等于（类型不一样，值一样会判断为`true`）,例如 `1 == '1'`
>
> `===` 绝对等于 (类型一样，值一样，为`true`) 推荐

`null` `undefined`

> `null` - 空
>
> `undefined` - 未定义

`数组`

> 数组可以中可以不是同一类型数据，但不推荐这样使用
>
> 下标越界会显示`undefined`

``` javascript
var arr = [1,2,3,"hello",null,true];
new Array(1,2,3,"hello",null)
```

`对象`

```javascript
var Person = {
    name : "zhang",
    age:13,
    tage:['student','class1']
}
```

## 变量 `var`与`let`

> `ES5`之前使用`var`定义局部变量，`ES6`之后推荐使用`let`
>
> 严格检查模式只需要在代码第一行加上 `use strict`（ES6）