# JavaScript

> JavaScript（简称“JS”） 是一种具有函数优先的轻量级，解释型或即时编译型的编程语言。虽然它是作为开发Web页面的[脚本语言](https://baike.baidu.com/item/脚本语言/1379708)而出名，但是它也被用到了很多非浏览器环境中，JavaScript 基于原型编程、多范式的动态脚本语言，并且支持面向对象、命令式和声明式（如函数式编程）风格。



## 数据类型

`number` 

> `JS`不区分小数和整数
>
> 浮点数存在精度问题,一般使用如下方法解决：	
>
> ​		`Math.abs(1/3 - (1-2/3) < 0.0000001)`
>
> 

``` javascript
NaN - not a number
//NaN与所有的数值都不相等，包括自己，只能使用isNaN(NaN)来测试
Infinity - 无穷大
```

`String`

`Boolean`

> 只有`true  ` `false`

`逻辑运算符`

> `=` 赋值
>
> `==` 等于（类型不一样，值一样会判断为`true`）,例如 `1 == '1'`
>
> `===` 绝对等于 (类型一样，值一样，为`true`) 推荐

`null` `undefined`

> null 未定义
>
> und