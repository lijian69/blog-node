---

title: JavaScript ES6入门   
categories: 前端开发   
tags:

  - JavaScript
  - ES6
  - 前端

---

## 第 1 章 变量的用法

### 1.1 let 关键字

let 是`块级作用域`变量关键字，块级也就是一对大括号。 在 {} 中声明，那么它只能在 {} 中访问。

```js
if(true){
	let a = 10;
    if(true){
        let b = 20;
    }
    console.log(b); // b is not defined
}
console.log(a); // a is not defined
```

let 能够防止了循环变量变成全局变量。

```
for(let i=0; i < 10; i++){

}
console.log(i); // i is not defined
```

- 使用 let 变量，没有变量提升。必须先声明后使用

**总结：** 在一个大括号（{}）中，使用 `let` 关键字声明的变量才具有块级作用域；而 `var 不具有`

### 1.2 var 关键字

```js
if(true){
	let a = 10;
    var b = 20; 
}
console.log(b); // 20
console.log(a); // a is not defined
```

### 1.3 const 关键字

作用：声明常量，常量就是值不可更改。

- 具有块级作用域

```js
if(true){
	const a = 10;
    if(true){
        const a = 20;
        console.log(a); // 20
    }
    console.log(a); // 10
}
console.log(a); // a is not defined
```

- 使用 const 声明常量时，`必须赋初始值`。
- 使用 const 声明常量时，值不可修改，**但是可以修改对象或者数组。**

```js
const PI = 3.14;
PI = 4; // Assignment to constant variable
```

### 1.4 let const var 的区别

1. 使用 var 声明的变量，其作用域位该语块所在的函数内，并且存在变量的提升现象。
2. 使用 let 声明的变量，其作用域位还语句所在的代码块中，不存在变量提升。
3. 使用 const 声明的变量，在后面的出现的代码中不能修改该变量的值。

| var          | let            | const          |
| ------------ | -------------- | -------------- |
| 函数级作用域 | 块级作用域     | 块级作用域     |
| 变量提升     | 不存在变量提升 | 不存在变量提升 |
| 值可变       | 值可变         | 值不可变       |

## 第 2 章 解构赋值用法

> ES6 中允许从数组中提取值，按照对应的位置，对变量赋值，对象也可以实现解构

### 2.1 数组解构

**注意：**`解构不成功，变量的值为 undefined`

```js
let ary = [1,2,3];
let [a,b,c,d] = ary
console.log(a); //1
console.log(b); //2
console.log(c); //3
console.log(d); //undefiend
```

### 2.2 对象解构

- 方式一

```js
let person = {name:"zhangsan", age:20};
let {name,age} = person;
console.log(name); // `zhangsan`
console.log(age); // 20
```

- 方式二

```js
let person = {name:"zhangsan", age:20};
let {name:myName, age:myAge} = person;
console.log(myName); // `zhangsan`
console.log(myAge); // 20
```

## 第 3 章 箭头函数

箭头函数是 ES6 新增的定义函数的方式，箭头函数是用来简化函数定义语法的。

```js
() => {}
// 通常我们给函数起一个名字
const fn = () => {};
```

> 若函数体内只有一句代码，且代码的执行结果是有返回值的，可以忽略大括号，若形参的个数只有一个，小括号也可以省略

```js
function sum(num1,num2){ //传统函数
	return num1 + num2;
}

const sum = (num1,num2) => num1 + num2; //箭头函数

const fn2 = v => {
    alert(v);
}
```

箭头函数不绑定 this 关键字，箭头函数中的 this 指向 `函数定义位置的上下文 this `；

```js
var age = 100;
var obj = {
	age : 20,
	say : () => {
		alert(this.age); // 100 对象是不能产生作用域的
	}
}
object.say();
```

## 第 4 章 剩余参数

与 Java 的多参数 类似；

```js
sum('加法',10,20,30)
const sum = (title,...args) => {
	let total = 0;
	args.forEach((item,index) => {
		total += item;
	})
}
```

剩余参数，还可以和解构配合使用

```js
let students = ['wangwu','zhangsan','lisi'];
let [s1,...s2] = students;
console.log(s1); // 'wangwu'
console.log(s2); // ['zhangsan','lisi'] Array[2]
```

### 4.1 Array 的拓展方法

拓展运算符可以将数组或者对象转为用逗号分割的参数序列。

```js
let ary = [1,2,3]
...ary //1,2,3
console.log(...ary) // 1 2 3 这里没有逗号
```

- 拓展运算符 可以应用于合并数组

```js
// 方式一
let ary1 = [1,2,3]
let ary2 = [4,5,6]
...ary1 // 1,2,3
...ary2 // 4,5,6
let ary3 = [...ary1, ...ary2]

// 方式二
ary1.push(...ary2)
```

- 拓展运算符 可以将伪数组转换为真正的数组

```
var oDiv = document.getElementsByTagName('div');
console.log(oDiv);
var ary = [...oDiv]
ary.push('a')
```

