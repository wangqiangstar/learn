### 历史版本

至发稿日为止有九个ECMA-262版本发表。其历史版本如下：

1. 1997年6月：第一版
2. 1998年6月：修改格式，使其与ISO/IEC16262国际标准一样
3. 1999年12月：强大的正则表达式，更好的词法作用域链处理，新的控制指令，异常处理，错误定义更加明确，数据输出的格式化及其它改变
4. 2009年12月：添加严格模式(`"use strict"`)。修改了前面版本模糊不清的概念。增加了getters，setters，JSON以及在对象属性上更完整的反射。
5. 2011年6月：ECMAScript标5.1版形式上完全一致于国际标准ISO/IEC 16262:2011。
6. 2015年6月：ECMAScript 2015（ES2015），第 6 版，最早被称作是 ECMAScript 6（ES6），添加了类和模块的语法，其他特性包括迭代器，Python风格的生成器和生成器表达式，箭头函数，二进制数据，静态类型数组，集合（maps，sets 和 weak maps），promise，reflection 和 proxies。作为最早的 ECMAScript Harmony 版本，也被叫做ES6 Harmony。
7. 2016年6月：ECMAScript 2016（ES2016），第 7 版，多个新的概念和语言特性。
8. 2017年6月：ECMAScript 2017（ES2017），第 8 版，多个新的概念和语言特性。
9. 2018年6月：ECMAScript 2018 （ES2018），第 9 版，包含了异步循环，生成器，新的正则表达式特性和 rest/spread 语法。
10. 2019年6月：ECMAScript 2019 （ES2019），第 10 版。

### ES6(ES2015)

> ES6是一次重大的革新，比起过去的版本，改动比较大，本文仅对常用的API以及语法糖进行讲解

#### let 和 Const

用`var`定义的变量没有块级作用域的概念，而`let`跟`const`则会有，因为这三个关键字创建是不一样的。

```
{
    var a = 10
    let b = 20
    const c = 30
}
a // 10
b // Uncaught ReferenceError: b is not defined
c // c is not defined
let d = 40
const e = 50
d = 60
d // 60
e = 70 // VM231:1 Uncaught TypeError: Assignment to constant variable.

```



|                | var  | let  | const |
| -------------- | ---- | ---- | ----- |
| 变量提升       | √    | ×    | ×     |
| 全局变量       | √    | ×    | ×     |
| 重复声明       | √    | ×    | ×     |
| 重新赋值       | √    | √    | ×     |
| 暂时死区       | ×    | √    | √     |
| 块作用域       | ×    | √    | √     |
| 只声明不初始化 | √    | √    | ×     |

#### 类（Class）

在ES6之前，如果我们要生成一个实例对象，传统的方法就是写一个构造函数，例子如下：

```
function Person(name, age) {
    this.name = name
    this.age = age
}
Person.prototype.information = function () {
    return 'My name is ' + this.name + ', I am ' + this.age
}
```

但是在ES6之后，我们只需要写成以下形式：

```
class Person {
    constructor(name, age) {
        this.name = name
        this.age = age
    }
    information() {
        return 'My name is ' + this.name + ', I am ' + this.age
    }
}
```

#### 箭头函数（Arrow function）

箭头函数表达式的语法比函数表达式更简洁，并且没有自己的`this`，`arguments`，`super`或 `new.target`。这些函数表达式更适用于那些本来需要匿名函数的地方，并且它们不能用作构造函数。

在ES6以前，我们写函数一般是：

```
var list = [1, 2, 3, 4, 5, 6, 7]
var newList = list.map(function (item) {
    return item * item
})
```

但是在ES6里，我们可以：

```
const list = [1, 2, 3, 4, 5, 6, 7]
const newList = list.map(item => item * item)
```

看，是不是简洁了不少

#### 函数参数默认值（Function parameter defaults）

在ES6之前，如果我们写函数需要定义初始值的时候，需要这么写：

```
function config (data) {
    var data = data || 'data is empty'
}
```

这样看起来也没有问题，但是如果参数的布尔值为**falsy**时就会出问题，例如我们这样调用config：

```
config(0)
config('')
```

那么结果就永远是后面的值

如果我们用函数参数默认值就没有这个问题，写法如下：

```
const config = (data = 'data is empty') => {}
```

#### 模板字符串（Template string）

在ES6之前，如果我们要拼接字符串，则需要像这样：

```
var name = 'kris'
var age = 24
var info = 'My name is ' + this.name + ', I am ' + this.age
```

但是在ES6之后，我们只需要写成以下形式：

```
const name = 'kris'
const age = 24
const info = `My name is ${name}, I am ${age}`
```

#### 解构赋值（Destructuring assignment）

我们通过解构赋值, 可以将属性/值从对象/数组中取出,赋值给其他变量。

比如我们需要交换两个变量的值，在ES6之前我们可能需要：

```
var a = 10
var b = 20
var temp = a
a = b
b = temp
```

但是在ES6里，我们有：

```
let a = 10
let b = 20
[a, b] = [b, a]
```

是不是方便很多

#### 模块化（Module）

在ES6之前，JS并没有模块化的概念，有的也只是社区定制的类似CommonJS和AMD之类的规则。例如基于CommonJS的NodeJS：

```
// circle.js
// 输出
const { PI } = Math
exports.area = (r) => PI * r ** 2
exports.circumference = (r) => 2 * PI * r

// index.js
// 输入
const circle = require('./circle.js')
console.log(`半径为 4 的圆的面积是 ${circle.area(4)}`)
```

在ES6之后我们则可以写成以下形式：

```
// circle.js
// 输出
const { PI } = Math
export const area = (r) => PI * r ** 2
export const circumference = (r) => 2 * PI * r

// index.js
// 输入
import {
    area
} = './circle.js'
console.log(`半径为 4 的圆的面积是: ${area(4)}`)
```

#### 扩展操作符（Spread operator）

扩展操作符可以在函数调用/数组构造时, 将数组表达式或者string在语法层面展开；还可以在构造字面量对象时, 将对象表达式按key-value的方式展开。

比如在ES5的时候，我们要对一个数组的元素进行相加，在不使用`reduce`或者`reduceRight`的场合，我们需要：

```
function sum(x, y, z) {
    return x + y + z;
}
var list = [5, 6, 7]
var total = sum.apply(null, list)
```

但是如果我们使用扩展操作符，只需要如下：

```
const sum = (x, y, z) => x + y + z
const list = [5, 6, 7]
const total = sum(...list)
```

非常的简单，但是要注意的是扩展操作符只能用于可迭代对象

如果是下面的情况，是会报错的：

```
var obj = {'key1': 'value1'}
var array = [...obj] // TypeError: obj is not iterable
```

#### 对象属性简写（Object attribute shorthand）

在ES6之前，如果我们要将某个变量赋值为同样名称的对象元素，则需要：

```
var cat = 'Miaow'
var dog = 'Woof'
var bird = 'Peet peet'

var someObject = {
  cat: cat,
  dog: dog,
  bird: bird
}
```

但是在ES6里我们就方便很多：

```
let cat = 'Miaow'
let dog = 'Woof'
let bird = 'Peet peet'

let someObject = {
  cat,
  dog,
  bird
}

console.log(someObject)

//{
//  cat: "Miaow",
//  dog: "Woof",
//  bird: "Peet peet"
//}
```

非常方便

#### Promise

Promise 是ES6提供的一种异步解决方案，比回调函数更加清晰明了。

`Promise` 翻译过来就是承诺的意思，这个承诺会在未来有一个确切的答复，并且该承诺有三种状态，分别是：

1. 等待中（pending）
2. 完成了 （resolved）
3. 拒绝了（rejected）

这个承诺一旦从等待状态变成为其他状态就永远不能更改状态了，也就是说一旦状态变为 resolved 后，就不能再次改变

```
new Promise((resolve, reject) => {
  resolve('success')
  // 无效
  reject('reject')
})
```

当我们在构造 `Promise` 的时候，构造函数内部的代码是立即执行的

```
new Promise((resolve, reject) => {
  console.log('new Promise')
  resolve('success')
})
console.log('finifsh')
// new Promise -> finifsh
```

`Promise` 实现了链式调用，也就是说每次调用 `then` 之后返回的都是一个 `Promise`，并且是一个全新的 `Promise`，原因也是因为状态不可变。如果你在 `then` 中 使用了 `return`，那么 `return` 的值会被 `Promise.resolve()` 包装

```
Promise.resolve(1)
  .then(res => {
    console.log(res) // => 1
    return 2 // 包装成 Promise.resolve(2)
  })
  .then(res => {
    console.log(res) // => 2
  })
```

当然了，`Promise` 也很好地解决了回调地狱的问题，例如：

```
ajax(url, () => {
    // 处理逻辑
    ajax(url1, () => {
        // 处理逻辑
        ajax(url2, () => {
            // 处理逻辑
        })
    })
})
```

可以改写成：

```
ajax(url)
  .then(res => {
      console.log(res)
      return ajax(url1)
  }).then(res => {
      console.log(res)
      return ajax(url2)
  }).then(res => console.log(res))
```

#### for...of

`for...of`语句在可迭代对象（包括 `Array，Map，Set，String，TypedArray，arguments` 对象等等）上创建一个迭代循环，调用自定义迭代钩子，并为每个不同属性的值执行语句。

例子如下：

```
const array1 = ['a', 'b', 'c'];

for (const element of array1) {
      console.log(element)
}

// "a"
// "b"
// "c"
```

#### Symbol

**symbol** 是一种基本数据类型，`Symbol()`函数会返回**symbol**类型的值，该类型具有静态属性和静态方法。它的静态属性会暴露几个内建的成员对象；它的静态方法会暴露全局的symbol注册，且类似于内建对象类，但作为构造函数来说它并不完整，因为它不支持语法："`new Symbol()`"。

每个从`Symbol()`返回的symbol值都是唯一的。一个symbol值能作为对象属性的标识符；这是该数据类型仅有的目的。

例子如下：

```
const symbol1 = Symbol();
const symbol2 = Symbol(42);
const symbol3 = Symbol('foo');

console.log(typeof symbol1); // "symbol"
console.log(symbol3.toString()); // "Symbol(foo)"
console.log(Symbol('foo') === Symbol('foo')); // false
```

#### 迭代器（Iterator）/ 生成器（Generator）

迭代器（Iterator）是一种迭代的机制，为各种不同的数据结构提供统一的访问机制。任何数据结构只要内部有 Iterator 接口，就可以完成依次迭代操作。

一旦创建，迭代器对象可以通过重复调用next()显式地迭代，从而获取该对象每一级的值，直到迭代完，返回`{ value: undefined, done: true }`

虽然自定义的迭代器是一个有用的工具，但由于需要显式地维护其内部状态，因此需要谨慎地创建。生成器函数提供了一个强大的选择：它允许你定义一个包含自有迭代算法的函数， 同时它可以自动维护自己的状态。 生成器函数使用 [`function*`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/function*)语法编写。 最初调用时，生成器函数不执行任何代码，而是返回一种称为Generator的迭代器。 通过调用生成器的下一个方法消耗值时，Generator函数将执行，直到遇到yield关键字。

可以根据需要多次调用该函数，并且每次都返回一个新的Generator，但每个Generator只能迭代一次。

所以我们可以有以下例子：

```
function* makeRangeIterator(start = 0, end = Infinity, step = 1) {
    for (let i = start; i < end; i += step) {
        yield i;
    }
}
var a = makeRangeIterator(1,10,2)
a.next() // {value: 1, done: false}
a.next() // {value: 3, done: false}
a.next() // {value: 5, done: false}
a.next() // {value: 7, done: false}
a.next() // {value: 9, done: false}
a.next() // {value: undefined, done: true}
```

#### Set/WeakSet

`Set` 对象允许你存储任何类型的唯一值，无论是原始值或者是对象引用。

所以我们可以通过`Set`实现数组去重

```
const numbers = [2,3,4,4,2,3,3,4,4,5,5,6,6,7,5,32,3,4,5]
console.log([...new Set(numbers)]) 
// [2, 3, 4, 5, 6, 7, 32]
```

`WeakSet` 结构与 `Set` 类似，但区别有以下两点：

- `WeakSet` 对象中只能存放对象引用, 不能存放值, 而 `Set` 对象都可以。
- `WeakSet` 对象中存储的对象值都是被弱引用的, 如果没有其他的变量或属性引用这个对象值, 则这个对象值会被当成垃圾回收掉. 正因为这样, `WeakSet` 对象是无法被枚举的, 没有办法拿到它包含的所有元素。

所以代码如下：

```
var ws = new WeakSet()
var obj = {}
var foo = {}

ws.add(window)
ws.add(obj)

ws.has(window) // true
ws.has(foo)    // false, 对象 foo 并没有被添加进 ws 中 

ws.delete(window) // 从集合中删除 window 对象
ws.has(window)    // false, window 对象已经被删除了

ws.clear() // 清空整个 WeakSet 对象
```

#### Proxy/Reflect

`Proxy` 对象用于定义基本操作的自定义行为（如属性查找，赋值，枚举，函数调用等）。

`Reflect` 是一个内置的对象，它提供拦截 JavaScript 操作的方法。这些方法与  `Proxy` 的方法相同。`Reflect`不是一个函数对象，因此它是不可构造的。

`Proxy`跟`Reflect`是非常完美的配合，例子如下：

```
const observe = (data, callback) => {
      return new Proxy(data, {
            get(target, key) {
                return Reflect.get(target, key)
            },
            set(target, key, value, proxy) {
                  callback(key, value);
                  target[key] = value;
                    return Reflect.set(target, key, value, proxy)
            }
      })
}

const FooBar = { open: false };
const FooBarObserver = observe(FooBar, (property, value) => {
  property === 'open' && value 
          ? console.log('FooBar is open!!!') 
          : console.log('keep waiting');
});
console.log(FooBarObserver.open) // false
FooBarObserver.open = true // FooBar is open!!!
```

当然也不是什么都可以被代理的，如果对象带有`configurable: false` 跟`writable: false` 属性，则代理失效。

### ES7(ES2016)

#### Array.prototype.includes()

`includes()` 方法用来判断一个数组是否包含一个指定的值，根据情况，如果包含则返回 true，否则返回false。

代码如下：

```
const array1 = [1, 2, 3]
console.log(array1.includes(2)) // true

const pets = ['cat', 'dog', 'bat']
console.log(pets.includes('cat')) // true
console.log(pets.includes('at')) // false
```

#### 幂运算符**

幂运算符**，具有与Math.pow()一样的功能，代码如下：

```
console.log(2**10) // 1024
console.log(Math.pow(2, 10)) // 1024
```

### ES8(ES2017)

#### async/await

虽然`Promise`可以解决回调地狱的问题，但是链式调用太多，则会变成另一种形式的回调地狱 —— 面条地狱，所以在ES8里则出现了`Promise`的语法糖`async/await`，专门解决这个问题。

我们先看一下下面的`Promise`代码：

```
fetch('coffee.jpg')
    .then(response => response.blob())
    .then(myBlob => {
          let objectURL = URL.createObjectURL(myBlob)
          let image = document.createElement('img')
          image.src = objectURL
          document.body.appendChild(image)
    })
    .catch(e => {
          console.log('There has been a problem with your fetch operation: ' + e.message)
    })
复制代码
```

然后再看看`async/await`版的，这样看起来是不是更清晰了。

```
async function myFetch() {
      let response = await fetch('coffee.jpg')
      let myBlob = await response.blob()

      let objectURL = URL.createObjectURL(myBlob)
      let image = document.createElement('img')
      image.src = objectURL
      document.body.appendChild(image)
}

myFetch()
复制代码
```

当然，如果你喜欢，你甚至可以两者混用

```
async function myFetch() {
      let response = await fetch('coffee.jpg')
      return await response.blob()
}

myFetch().then((blob) => {
      let objectURL = URL.createObjectURL(blob)
      let image = document.createElement('img')
      image.src = objectURL
      document.body.appendChild(image)
})
```

#### Object.values()

`Object.values()`方法返回一个给定对象自身的所有可枚举属性值的数组，值的顺序与使用for...in循环的顺序相同 ( 区别在于 for-in 循环枚举原型链中的属性 )。

代码如下：

```
const object1 = {
      a: 'somestring',
      b: 42,
      c: false
}
console.log(Object.values(object1)) // ["somestring", 42, false]

```

#### Object.entries()

`Object.entries()`方法返回一个给定对象自身可枚举属性的键值对数组，其排列与使用 for...in 循环遍历该对象时返回的顺序一致（区别在于 for-in 循环还会枚举原型链中的属性）。

代码如下：

```
const object1 = {
      a: 'somestring',
      b: 42
}

for (let [key, value] of Object.entries(object1)) {
      console.log(`${key}: ${value}`)
}

// "a: somestring"
// "b: 42"
```

### ES9(ES2018)

#### for await...of

`for await...of` 语句会在异步或者同步可迭代对象上创建一个迭代循环，包括 `String`，`Array`，`Array-like` 对象（比如`arguments` 或者`NodeList`)，`TypedArray`，`Map`， `Set`和自定义的异步或者同步可迭代对象。其会调用自定义迭代钩子，并为每个不同属性的值执行语句。

配合迭代异步生成器，例子如下：

```
async function* asyncGenerator() {
      var i = 0
      while (i < 3) {
            yield i++
      }
}

(async function() {
      for await (num of asyncGenerator()) {
            console.log(num)
      }
})()
// 0
// 1
// 2
```

#### 正则表达式命名捕获组

在以往的版本里，JS的正则分组是无法命名的，所以容易混淆。例如下面获取年月日的例子，很容易让人搞不清哪个是月份，哪个是年份:

```
const matched = /(\d{4})-(\d{2})-(\d{2})/.exec('2019-01-01')
console.log(matched[0]);    // 2019-01-01
console.log(matched[1]);    // 2019
console.log(matched[2]);    // 01
console.log(matched[3]);    // 01
复制代码
```

ES9引入了命名捕获组，允许为每一个组匹配指定一个名字，既便于阅读代码，又便于引用。代码如下：

```
const RE_DATE = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/;

const matchObj = RE_DATE.exec('1999-12-31');
const year = matchObj.groups.year; // 1999
const month = matchObj.groups.month; // 12
const day = matchObj.groups.day; // 31

const RE_OPT_A = /^(?<as>a+)?$/;
const matchObj = RE_OPT_A.exec('');

matchObj.groups.as // undefined
'as' in matchObj.groups // true
```

#### 对象扩展操作符

ES6中添加了数组的扩展操作符，让我们在操作数组时更加简便，美中不足的是并不支持对象扩展操作符，但是在ES9开始，这一功能也得到了支持，例如：

```
var obj1 = { foo: 'bar', x: 42 };
var obj2 = { foo: 'baz', y: 13 };

var clonedObj = { ...obj1 };
// 克隆后的对象: { foo: "bar", x: 42 }

var mergedObj = { ...obj1, ...obj2 };
// 合并后的对象: { foo: "baz", x: 42, y: 13 }
```

上面便是一个简便的浅拷贝。这里有一点小提示，就是`Object.assign()` 函数会触发 `setters`，而展开语法则不会。所以不能替换也不能模拟`Object.assign()` 。

如果存在相同的属性名，只有最后一个会生效。

#### Promise.prototype.finally()

`finally()`方法会返回一个`Promise`，当promise的状态变更，不管是变成`rejected`或者`fulfilled`，最终都会执行`finally()`的回调。

例子如下：

```
fetch(url)
      .then((res) => {
        console.log(res)
      })
      .catch((error) => { 
        console.log(error)
      })
      .finally(() => { 
        console.log('结束')
    })
```

### ES10(ES2019)

#### Array.prototype.flat() / flatMap()

`flat()` 方法会按照一个可指定的深度递归遍历数组，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回。

`flatMap()`与 `map()` 方法和深度为1的 `flat()` 几乎相同.，不过它会首先使用映射函数映射每个元素，然后将结果压缩成一个新数组，这样效率会更高。

例子如下：

```
var arr1 = [1, 2, 3, 4]

arr1.map(x => [x * 2]) // [[2], [4], [6], [8]]

arr1.flatMap(x => [x * 2]) // [2, 4, 6, 8]

// 深度为1
arr1.flatMap(x => [[x * 2]]) // [[2], [4], [6], [8]]
```

`flatMap()`可以代替`reduce()` 与 `concat()`，例子如下：

```
var arr = [1, 2, 3, 4]
arr.flatMap(x => [x, x * 2]) // [1, 2, 2, 4, 3, 6, 4, 8]
// 等价于
arr.reduce((acc, x) => acc.concat([x, x * 2]), []) // [1, 2, 2, 4, 3, 6, 4, 8]
```

但这是非常低效的，在每次迭代中，它创建一个必须被垃圾收集的新临时数组，并且它将元素从当前的累加器数组复制到一个新的数组中，而不是将新的元素添加到现有的数组中。

#### Object.fromEntries()

`Object.fromEntries()` 方法把键值对列表转换为一个对象，它是`Object.entries()`的反函数。

例子如下：

```
const entries = new Map([
  ['foo', 'bar'],
  ['baz', 42]
])

const obj = Object.fromEntries(entries)

console.log(obj) // Object { foo: "bar", baz: 42 }
```

#### import()

静态的`import` 语句用于导入由另一个模块导出的绑定。无论是否声明了 严格模式，导入的模块都运行在严格模式下。在浏览器中，`import` 语句只能在声明了 `type="module"` 的 `script` 的标签中使用。

但是在ES10之后，我们有动态 `import()`，它不需要依赖 `type="module"` 的script标签。

所以我们有以下例子：

```
const main = document.querySelector("main")
for (const link of document.querySelectorAll("nav > a")) {
      link.addEventListener("click", e => {
            e.preventDefault()

            import('/modules/my-module.js')
              .then(module => {
                    module.loadPageInto(main);
              })
              .catch(err => {
                    main.textContent = err.message;
              })
      })
}
```









































