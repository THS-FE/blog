---
title: JavaScript新特性
author: 吴俊
authorLink: https://github.com/Patrick-Jun
excerpt: 从ES6+中，总结了一些比较常用的新特性的基础用法
cover: 2020/08/24/JavaScript新特性/cover.jpg
thumbnail: 2020/08/24/JavaScript新特性/cover.jpg
categories:
  - - JavaScript
    - 新特性
tags:
  - JavaScript
  - ES6
toc: true
date: 2020-08-24 13:39:25
---

## 1 前言

我这里总结了一下 ES6+中，一些比较实用的新特性。我们日常开发应该尽快使用这些新特性，能极大地提高我们的开发效率。

我刚开始实习的时候，对 ES6 都不太怎么了解，工作后学习并渐渐运用起来，越用越爽，两个字：简洁方便高效。

提一句：**只要用了 babel，所有的新特性请放心大胆地用**。

## 2 你得尽快用上的“新特性”

> 为什么加引号，因为现在这些都不是多新的特性了，ES6 是 2015 年就出了，到现在已经 5 年了。

### 2.1 模板字符串

**模版字符串**：用 \`（反引号）标识，用 ${} 将变量括起来

**old**：

场景：通常我们在自定义一些 echarts 或者地图上添加东西时，我们常会拼接一些 html 代码

```javascript
var html = '<div style="color: ' + color + ';">' + str + "<div>";
```

传统做法需要使用大量的“”（引号）和 + 来拼接才能得到我们需要的模版

**new**：

```javascript
let html = `<div style="color: ${color} ;"> ${str} <div>`;
```

`${}` 里可以放任意的 JavaScript 表达式，也可以调用函数：

```javascript
const count = 8,
  price = 10;
console.log(`加购一个后数量：${++count}, 总价：${count * price}`); // 加购一个后数量：9, 总价：90
console.log(`输出个字符串：${"cool"}`); // 输出个字符串：cool

function myLove() {
  return "as you love!";
}
console.log(`I love ${myLove()}`); // I love as you love!
```

**需要注意的几个问题**：

1. 当需要在字符串里使用反引号的时候，需要转义；

   ```javascript
   console.log(`模版字符串：用 \`（反引号）标识`);
   ```

2. 如果`${}`中的变量不是字符串类型，那么会按照一般的规则转化为字符串；

   ```javascript
   const obj = { a: 1, b: 2 };
   console.log(`a = ${obj}`); // a = [object Object]
   ```

3. 模板字符串会保留所有的空格、缩进和换行；

   ```javascript
   let str = `I know   
    , you know!`;
   console.log(str);
   //  I know
   //  , you know!
   ```

   解决方案：使用`\`解决换行符；使用`+`换行拼接；使用正则替换；使用变量替换；

   ```javascript
   let str = `I know   \
    , you know!`;
   console.log(str); // I know    , you know!

   str = `I know   ` + `, you know!`;
   console.log(str); // I know   , you know!

   str = `I know
    , you know!`.replace(/\s+/gm, " ");
   console.log(str); // I know , you know!

   const N = "";
   str = `I know${N}, you know${N}, all we know!`;
   console.log(str); // I know, you know, all we know!
   ```

**扩展了解**：

实现原理（未验证）：通过正则匹配，替换原字符串中的变量。包括常见的`{% raw %}{{}}{% endraw %}`, `<%=xx%>`等

```javascript
function replace(str) {
  return str.replace(/\$\{([^}])\}/g, function (matched, key) {
    return eval(key);
  });
}
```

### 2.2 属性简写

**old**：

一个属性名对应一个值

```javascript
const pageNum = 0,
  pageSize = 10;
const params = {
  pageNum: pageNum,
  pageSize: pageSize,
};
```

**new**：

属性名和变量名保持一致，变量名尽量迎合属性名；

```javascript
const pageNum = 0,
  pageSize = 10,
  password = "123123";
const params = {
  pageNum,
  pageSize,
  password: encrypt(password), // 属性简写和键值对可以混写
};
// const params = {
//     pageNum: pageNum,
//     pageSize: pageSize
// }
```

**question**：

1. 如果我们的需要的值不是一个单独变量，而是从某个对象取出属性

   ```javascript
   const pageNum = 0, pageSize = 10, user = {uid: 100000, password: '123123'};
   const params = {
       pageNum,
       pageSize,
       user.password  ????
   }
   ```

   答案：[见 4.答案](https://ths-fe.github.io/2020/08/24/JavaScript新特性/#答案)

### 2.3 方法属性

**old**：

一个属性名对应一个值

```javascript
let math = {
  add: function (a, b) {
    return a + b;
  },
  sub: function (a, b) {
    return a - b;
  },
  multiply: function (a, b) {
    return a * b;
  },
};
```

**new**：

自动识别方法名称作为属性名

```javascript
let math = {
  add(a, b) {
    return a + b;
  },
  sub(a, b) {
    return a - b;
  },
  multiply(a, b) {
    return a * b;
  },
};
```

取函数名为属性名称

**微信小程序 page 结构**：

```javascript
Page({
  data: {
    isShowloading: true,
  },
  onLoad(options) {},

  onReady() {},

  handleTap(event) {},
});
// 给page传入一个对象，这个对象的所有函数都可以进行属性名简写
```

**question**:

下面两个表达式都正确吗？

```javascript
let obj1 = {
    fn1(){}.bind()
}

let obj2 = {
    fn2: function(){}.bind()
}
```

### 2.4 箭头函数

箭头函数表达方式：`=>`，因为像个箭头，所以叫箭头函数。

**old**：

```javascript
var f = function (v) {
  return v;
};
```

**new**：

```javascript
// 写法
let f = (v) => v;

// 完整写法
let f = (v) => {
  return v;
};
```

如上，当函数只有一个形参时，`=>`左侧可以省略`()`；

当函数返回值可以用一句简单表达式表示时，`=>`右侧可以省略`{}`和`return`；

```javascript
let f = () => 5; // ()不可省略
let sum = (num1, num2) => num1 + num2;
//var sun = function(num1, num2){return num1 + num2;};

this.httpUtil.get("xxxxxx.vm", params, true, (res) => {
  console.log(res);
});
```

**question**:

以下会输出什么？

```javascript
let getTempItem = () => { id: 's8309a82n', name: "Temp" };
getTempItem();
```

### 2.5 “你懂的”运算符

Spread operator，这个中文名称有好几种说法（扩展运算符、延展操作符、展开运算符等等），而我给它起的名字就叫**你懂的运算符**。它表示方法前面见过了`...`，作用是可以将数组、字符串、对象等在语法层面上展开。

**秘诀**：`给我“解压”到这里`

1. “解压”数组

   ```javascript
   const rgb = ["red", "green", "blue"];
   const colors = [...rgb]; // 巴啦啦魔仙变，给我把rgb解压到这个数组里
   // 结果： ['red', 'green', 'blue']

   const colorList = ["yellow", ...rgb]; // ['yellow', 'red', 'green', 'blue']

   console.log([...colors, ...colorList]); // ????
   ```

2. “解压”对象

   ```javascript
   let you = {
     name: "DJ",
     age: 16,
   };
   you = {
     ...you,
     school: "DLPU",
   };
   // {name: "DJ", age: 16, school: "DLPU"}
   ```

3. “解压”字符串

   ```javascript
   let myCountry = 'China';
   console.log([...myCountry]);  // ["C", "h", "i", "n", "a"]
   // 等同于：console.log(myCountry.split(''))

   cosnt resStr = {...myCountry};
   console.log(resStr);  // {0: "C", 1: "h", 2: "i", 3: "n", 4: "a"}
   // 问题：怎么取值呢？  resStr[0]
   ```

**question**:

以下分别会输出什么？

```javascript
let obj = {a: 1, b: 2};
console.log({a: 0, ...obj});
????

let arr = [2,3,4];
console.log({...arr})
????
console.log([...obj]);
????
```

**扩展了解**：见下一章

### 2.6 解构赋值

**old**:

获取对象中的值

```javascript
// res = {status: 200, data: {uid: 'ed9fa0', name: 'DJ', time: '1596808152'}}
this.thsService.getLog().then((res) => {
  const status = res.status;
  const data = res.data;
  const name = res.data.name;
  const time = res.data.time;

  console.log(status, data, name, time);
});
```

**new**:

```javascript
this.thsService.getLog().then(res=>{
    const { status, data } = res;

    const { status, data, data: { name, time } } = res;

    // console.log(status, data, name, time);
})

// 还可以这样写
this.thsService.getLog().then({ status, data }=>{
    console.log(status, data);
})
```

**数组**：

```javascript
let arr = [1, 2, 3, 4];
let [a, b, c] = arr; // a=1, b=2, c=3
let [a, b, , d] = arr; // a=1, b=2, d=4
```

**默认值**：

```javascript
const { status = 500, data = null } = res;
let [a = 0, b = 0, c = 0, d = 0, e = 0] = arr;
```

**扩展了解**：

剩余运算符（ES2018）

**秘诀**：`“剩下的”都是我的`

1. “剩下的”属性

   ```javascript
   let obj = { a: 1, b: 2, c: (_) => _ };
   let { b, ...rest } = obj; // rest说：b属性你拿走吧，剩下的全是我的
   b; // 2
   rest; // {a: 1, c: ƒ}
   ```

2. “剩下的”参数

   ```javascript
   let restParam = (p1, p2, ...p3) => {
     // p3说：前两个参数你们拿走，剩下的都是我的了
     console.log(p1, p2, p3);
   };

   restParam(1, 2, 3, 4, 5); // p1 = 1, p2 = 2, p3 = [3, 4, 5]
   ```

### 2.7 数组新方法

- `find(): any`：返回找到满足条件的第一项，否则返回 undefined
- `findIndex(): number`：找到满足条件的一项的索引
- `includes(): boolean`：是否包含一个值

在 ES6 之前，要判断一个数组中是否包含一个元素，是通过`indexOf()`返回不等于`-1`

ES6 之后，相继扩充一些方法：

`find( fn(item, [index], [arr]) )`:

```javascript
let arr = [{ id: 1, checked: true }, { id: 2 }, { id: 2 }, 3, 4, NaN];

arr.find((item) => item.id === 1); // { id: 1, checked: true }
arr.find((item) => Object.is(NaN, item)); // NaN
```

find 会将每一个元素挨个去运行回调函数，找到了第一项之后就不会再执行了；

`findIndex( fn(item, [index], [arr]) )`:

```javascript
arr.findIndex((item) => item.id === 1); // 0
arr.findIndex((item) => Object.is(NaN, item)); // 5
```

`includes(value, fromIdx)`:

```javascript
arr.includes(3); // true
arr.includes(NaN); // true
arr.includes({ id: 2 }); // false

let a1 = { id: 2 },
  a2 = { id: 2 };
a1 == a2; // false
let a = [a1, a2]; // [{id: 2}, {id: 2}]
a.includes(a2); // true
```

字符串同样存在`includes`方法：`'Made in China'.includes('o')`, false

`some( fn(item, [index], [arr]) )`：是否存在满足条件的一项，和 includes 是同样的作用。

区别（优缺点）：`some`传入的是回调函数，具有更强大的可操性；`includes`传入参数是具体的值，书写简便。

**question**:

find()只能取出满足条件的一项，那如何取出数组中满足条件的所有项呢？

```javascript
let arr = [{ id: 1, checked: true }, { id: 2 }, { id: 2 }, 3, 4, NaN];
// ????
```

**扩展**：[数组所有方法参考手册](https://www.runoob.com/jsref/jsref-obj-array.html)

### 2.8 Promise、async/await

- 回调地狱：“无限”（大量）地使用嵌套回调函数，好像掉进了 18 层地狱

  ```javascript
  // 一个动画的回调地狱例子
  animate(ball1, 100, function () {
    animate(ball2, 200, function () {
      animate(ball3, 300, function () {
        animate(ball1, 200, function () {
          animate(ball3, 200, function () {
            animate(ball2, 180, function () {
              animate(ball2, 220, function () {
                animate(ball2, 200, function () {
                  console.log("over");
                })
              })
            })
          })
        })
      })
    })
  });

  // promise优化后
  promiseAnimate(ball1, 500)
      .then(function () {
        return promiseAnimate(ball2, 200);
      })
      .then(function () {
        return promiseAnimate(ball3, 300);
      })
      .then(function () {
        return promiseAnimate(ball1, 200);
      })
      .then(function () {
        return promiseAnimate(ball3, 200);
      })
      .then(function () {
        return promiseAnimate(ball2, 180);
      })
      .then(function () {
        return promiseAnimate(ball2, 220);
      })
      .then(function () {
        return promiseAnimate(ball2, 200);
      })

  // async/await优化后
  async play() {
      await animate(ball1, 500);
      await animate(ball2, 200);
      await animate(ball3, 300);
      await animate(ball1, 200);
      await animate(ball4, 200);
      await animate(ball2, 180);
      await animate(ball2, 220);
      await animate(ball2, 200);
  }
  ```

**Promise**:

基本用法：

```javascript
function getUserData() {
    return new Promise((resolved, rejected) => {
        $.ajax({
            type : "get",
            url : "api.com",
            success : res => {
                if(res.isSuccess) {
                    resolved(res.data);
                }else {
                    rejected({msg: '服务器错误', info: res.errmsg});
                }
            },
            error: err => {
                rejected({msg: '网络错误', info: err});
            }
        });
    })
}

getUserData().then(data => {
    console.log('success:', data);
}).catch(err => {
    console.log(err.msg, err.info);
})

// 此外介绍一个方法，并行跑promise es2020有个新方法Promise.allSettled
Promise.all([promise1, promise2, ...]).then(res => {
    console.log(res); // 由promise1,promise2正确执行结果组成的数组
}).catch(err => {
  console.log(err);
})
```

**async/await**：

是对 Promise 的优化，为 Promise 服务。一句话：**用同步的风格写异步代码**。

基础用法：<https://patrick.js.org/post/1589841597>

需要注意：

- async/await 就是一对“海尔兄弟”，缺一不可。`async`声明一个函数（函数返回会处理成一个 Promise），函数里面必须要有`await`，await 标识一个需要等一会（异步）的操作。函数内部使用了 await，那么该函数就必须用 async 声明。
- await、return 和 return await 的陷阱：<https://jakearchibald.com/2017/await-vs-return-vs-return-await/>

### 2.9 Modules

模块化是 ES6 比较重要的特性，在此之前 JS 是不支持原生的模块化的，需要通过第三方库实现如 RequireJS。

> 了解更多模块化：[JavaScript 模块化](https://ths-fe.github.io/2020/05/29/JavaScript模块化/)

模块化由`export` 和 `import` 组成，ES6 视一个文件为一个模块，文件内通过 export 对外暴露接口，其他文件通过 import 引入使用。

export：可导出变量、常量和函数

```javascript
// utils/test.js

// 单个导出
export let name = 'Patrick Jun';
export const pi = Math.PI;
export function whoIAm() {
    console.log("I'm a FE coder!");
}

// 等同于（会将export作为一个对象导出）
let name = 'Patrick Jun';
const pi = Math.PI;
const whoIAm = () => console.log("I'm a FE coder!");

export { name, pi, whoIAm };


// this is an object, so.
export { name: name, PI: pi, iAm: whoIAm };
```

import：导入

```javascript
// home.js
import { name, pi, whoIAm } from "./utils/test.js";
console.log(name, pi);
whoIAm();
// Patrick Jun 3.141592653589793      main.js:2
// I'm a FE coder!                    test.js:11
```

> node 无法直接运行 module：<https://nodejs.org/dist/latest-v10.x/docs/api/esm.html>

default：只能有一个

```javascript
// math.js
export function add(a, b) {
  return a + b;
}
export function sub(a, b) {
  return a - b;
}

export default (a, b) => a * b;

// main.js
import mult, { add, sub } from "./math";
```

## 3 你可以尝试的新特性

### 3.1 对象新方法

- Object.values(obj): 返回由对象中属性值组成的数组；

- Object.entries(obj): 返回对象的每个属性名和所对应的值组成的数组：`[[key, value],[key, value]]`

之前通过`Object.keys()`，可以获取到对象的所有的 key，而要获得所对应的值的时候：

```javascript
let obj = { id: 1, value: "123", data: { code: "EC109" } };
Object.keys(obj); // ["id", "value", "data"]

Object.keys(obj).forEach((key) => {
  console.log(obj[key]); // [1, "123", {code: 'EC109'}]
});
```

Object.values()：无需先获取键名，直接可以拿到所有值

```javascript
Object.values(obj); // [1, "123", {code: 'EC109'}]
```

Object.entries():

```javascript
Object.entries(obj).forEach(([key, value]) => {
  console.log(key + ": " + value);
});
// id: 1
// value: 123
// data: [object Object]
```

### 3.2 \*\*

指数操作符：类似数学的书写方式进行指数计算，可以看做是`Math.pow()`的简写

```javascript
let a = 7 ** 3; // a = 343，等同于 a = Math.pow(7, 3)
```

### 3.3 ??

当我们查询某个属性时，经常会给没有该属性就设置一个默认的值，比如下面两种方式：

```javascript
let c = a ? a : b; // 方式1
let c = a || b; // 方式2
```

这两种方式有个明显的弊端，它都会覆盖所有的假值，如(0, '', false)，这些值可能是在某些情况下有效的输入。

空位合并操作符，用 ?? 表示。如果表达式在??的左侧运算符求值为 **undefined 或 null**，就返回其右侧默认值。

```javascript
let c = a ?? b;
// 等价于let c = a !== undefined && a !== null ? a : b;
```

### 3.4 padStart/padEnd

用于在字符串开头或结尾添加填充字符串（ES2017）

- padStart(maxLength, [fillString])：从前面补充字符
- padEnd(maxLength, [fillString])：从后面补充字符

```javascript
"es8".padStart(2); // 'es8'
"es8".padStart(5); // '  es8'
"es8".padStart(6, "woof"); // 'wooes8'
"es8".padStart(14, "wow"); // 'wowwowwowwoes8'
"es8".padStart(7, "0"); // '0000es8'

"es8".padEnd(2); // 'es8'
"es8".padEnd(5); // 'es8  '
"es8".padEnd(6, "woof"); // 'es8woo'
"es8".padEnd(14, "wow"); // 'es8wowwowwowwo'
"es8".padEnd(7, "6"); // 'es86666'
```

应用场景：

```javascript
// 1.日期格式化
const dt = new Date();
console.log(
    `${dt.getFullYear()+''}-`
    +`${(dt.getMonth()+1+'').padStart(2, '0')}-`
    +`${(dt.getDate()+'').padStart(2, '0')}`
);
// 2020-08-07

// 2.时间戳补位
let timestamp = '1596808152';
timestamp = +String(timestamp).padEnd(13, '0');
// 1596808152000

// 3.地区编码补位 省级编码2位，市级4位，区县6位，乡镇9位，村级12位。现在需要统一补充成12位

/**
 * 格式化地区编码，按指定长度输出
 * @param regionCode 地区编码
 * @param length 需要的长度
 */
formateRegionCode(regionCode: string|number, length: number = 12): string {
  regionCode = String(regionCode);
  if(!regionCode || regionCode.length < 2) {
    throw new Error('地区编码错误');
  }
  if(length < 2) {
    throw new Error('地区编码长度不能小于2');
  }
  const tempCode = regionCode.split('').slice(0, length).join('');
  return tempCode.length < length ? tempCode.padEnd(length, '0') : tempCode;
}
```

## 4 答案

**2.2 question**:

```javascript
const pageNum = 0,
  pageSize = 10,
  user = { uid: 100000, password: "123123" };

let params = {
  pageNum,
  pageSize,
  password: user.password, // 需给定属性名，user.password是无法将其识别成属性名
};
// 还可以这样
let params = {
  pageNum,
  pageSize,
  ...user, // 2.5小节讲解
};
// {pageNum: 0, pageSize: 10, uid: 100000, password: "123123"}
// 通过扩展符，可能会多出其他属性，如果多出来的属性对结果不影响，可以考虑这样做
```

**2.3 question**: obj1 错误，obj2 正确。简写方法的属性名总是变量本身作为字符串使用，bind 函数本身返回一个函数，从解析器角度来说，这个返回的函数叫什么名字并没有办法确定，而第二种写法，是确定好了`fn2`

**2.4 question**:

```javascript
Uncaught SyntaxError: Unexpected token ':'

let getTempItem = id => ({ id, name: "Temp" });
```

**2.5 question**:

```javascript
{a: 1, b: 2}

{0: 2, 1: 3, 2: 4}   // result.0 ????

VM37:1 Uncaught TypeError: obj is not iterable
    at <anonymous>:1:17
```

**2.7 question**:

```javascript
arr.filter((item) => item.id === 2); // [{id: 2}, {id: 2}]
```

## 5 参考资料

1. [Modern JavaScript, 10 things you should be using, starting today - DEV](https://dev.to/itnext/modern-javascript-10-things-you-should-be-using-starting-today-22dp)

2. [盘点 ES7、ES8、ES9、ES10 新特性](https://juejin.im/post/6844904018834096142)

3. [ES6，ES7，ES8，ES9，ES10 新特性一览](https://blog.csdn.net/weixin_43720095/article/details/89432584)

4. [ES2020 新特性](https://juejin.im/post/6844904080955932679)

5. [种草 ES2020 新特性](https://zhuanlan.zhihu.com/p/100251213)

6. [异步 Promise 及 Async/Await 可能最完整入门攻略](https://segmentfault.com/a/1190000016788484)

**刘哥金句**：给别人讲述知识时可以发现自己掌握的是否牢固透彻，写的过程不断发现自己的不足，然后通过一些方式来解决问题，这也是一种学习过程；当然，给别人分享，也要从学习者的角度出发，考虑他们想要从你的分享中获得什么，还有就是你想表达些什么给他们。
