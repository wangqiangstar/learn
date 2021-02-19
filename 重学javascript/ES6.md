# ECMAScript 6

## 使用环境

### Chrome

访问chrome://flags/#enable-javascript-harmony,开启`enable-javascript-harmony`特性,就可以使用`ES6`了.

### Nodejs

升级最新的Nodejs版本,在Nodejs中使用`ES6`.

### Webpack

可以访问[使用Babel 6和Webpack在浏览器端运行ES6和ES7](http://www.tuicool.com/articles/fmUze2M)查看使用方法.

## let 和 const

`ES5`只有两种声明变量的方式, `var`和`function`. 在`ES6`中有6种声明变量的方法,`var`,`function`,`let`,`const`,`import`和`class`.

### let

`let`具有块级作用域,而`var`没有

```
{
    let a = 10;
    var b = 1;
}

console.log(b); //1 
console.log(a); //a is not defined  
```



进一步说明`let`仅在块级作用域有效

```
var a = [],
    b = [];
    
for(var i = 0; i < 10; i++) {
    var c1 = i;
    let c2 = i;
    a[i] = function() {
        console.log(c1);
    };

    b[i] = function(){
        console.log(c2);
    };
}

a[5](); //9
b[5](); //5
```



`let`更像`c`语言中的变量,不再具有**变量提升**能力

```
var a = 1;
let b = 2;

function variable(){
    console.log(a);     //undefined
    console.log(b);     //b is not defined
    var a = 1;
    let b = 2;
}

variable();
```



暂时性死区

```
let b = 1;
if(true){
    b = 2;  //b is not defined
    let b;
}
```



> 提示:`ES6`明确规定,如果区块中存在`let`和`const`命令,这个区块对这些命令声明的变量,从一开始就形成了封闭作用域.凡是在声明之前就使用这些变量,就会报错.总之,在代码块内,使用`let`命令声明变量之前,该变量都是不可用的.这在语法上,称为“暂时性死区”（**temporal dead zone**,简称`TDZ`）.

```
if(true){
                                //TDZ Start
    typeof(undefinedVariable);  //undefined
    typeof(b);                  //b is not defined  

    b = 2;                      //b is not defined
    b = b + 1;

    let b;                      //TDZ End
    b = 10;
    console.log(b);
}
```



> 提示:此时使用`typeof`就会报错,但是如果变量没有声明反而不会报错而输出`undefined`.

`let`不允许重复声明

```
{
    var b = 1;
    var b = 2;
    console.log(b);     //2
    let a = 1;
    let a = 2;
    console.log(a);     //Identifier 'a' has already been declared
}
```



再次说明`let`新增了块级作用域

```
function f(){
    let a = 1;      //外层代码块不受内层代码块影响
    if(a){
        let a = 2;  //只在该块中有作用域
    }
    console.log(a); //1
}

f();

function f1(){
    var b = 1;
    if(b){
        var b = 2;  //没有块级作用域
    }
    console.log(b); //2
}

f1();
```



> 提示: `ES5`只有全局作用域和函数作用域,而没有块级作用域,一般也都是利用立即执行的匿名函数来模拟块级作用域.

函数的块级作用域

`ES5`中规定函数只能在**顶层作用域**和**函数作用域**中声明,而不能在**块级作用域**中声明,在严格模式下会报错[非严格模式下不会报错],`ES6`中引入了类似于`let`的函数声明语句,该函数也是有作用域的,在**块级作用域**之外不能被引用.

```
function f(){           //第一次f函数声明
    console.log('f is 1!');
}

if(false){              
    function f(){       //第二次f函数声明
        console.log('f is 2');
    }
}

f();                    //f is 1 ES6
                        //f is 2 ES5
```



> 提示: 在`ES5`中,`if`语句块中的`f`函数的声明会被提升到**全局作用域(顶层作用域)**,所以输出的是覆盖了第二次声明的`f`函数.但是在`ES6`中,由于语句块中的函数声明只是在该语句块中起作用(只能提升到该**块级作用域**的顶部),不会对语句块外产生影响,所以输出的是第一次声明的`f`函数.

`ES6`中的函数

- 允许在块级作用域内声明函数(在`ES5`中原则上是不允许的)
- 函数声明类似于`var`,会提升到全局作用域或者函数作用域的顶部
- 会提升到块级作用域的顶部

```
console.log(f);         //undefined 当做未声明过的变量来处理

if(true){       
    console.log(f);     //function f() 函数声明在块级作用域中提升
    function f(){       
        console.log('f is 2');
    }
}
```



### const

```
const a = 1;
console.log(a);         //1
a = 2;                  //Assignment to constant variable

const b;                //Missing initializer in const declaration
```



> 提示: `const`在声明的同时必须赋值,否则会报错.且不能重新赋值.

`const`同样具有块级作用域

```
{
    const a = 1;
    console.log(a);    //1
}
console.log(a);        //a is not defined
```



和`let`一样也不可重复声明

```
const a = 1;
const a = 2;   //Identifier 'a' has already been declared

var b = 1;
let b = 2;     //Identifier 'a' has already been declared
const b = 3;
```



和`let`一样,变量不会提升声明,同样存在暂时性死区`TDZ`.

```
const a = 3;

if(true){
    //TDZ Start
    console.log(a); //a is not defined
    const a = 1;    //TDZ End
}
```



对于引用类型,`const`只能保证变量所指向的地址不变,而不能保证地址所在的值不变.

```
const a = {};
a.name = 'a';
a.name = 'b';
console.log(a); //Object {name: "b"}

a = {           //另一个对象赋值给a
    name: 'c'
}
console.log(a); //Assignment to constant variable.
```



> 提示: `a`的属性值变没关系,只要`a`指向的地址不变,但是对`a`重新赋值,则导致`a`指向的地址变化了,这是不允许的. 数组也是一样.

### 全局对象和属性

`let`,`const`,`class`声明的全局变量将不再是全局对象(`window`,`global`)的属性,`var`和`function`仍然不变.

```
var a=1;
console.log(window.a);  //1

let b=2;
const c=3;
console.log(window.b);  //undefined
console.log(window.c);  //undefined
```



## 变量的解构赋值

### Array解构

将变量封装成数组的形式,然后将表达式右边的数组和对象中提取的值一一对应的赋值给这些变量,就叫做数组解析.

```
var [a,b,c] = [1,2,3];
console.log(a); //1
console.log(b); //2
console.log(c); //3
```



还可以以嵌套的形式赋值

```
var [a,[[b],c]] = [1,[[2],3]];
console.log(b); //2
```



其他的一些例子

```
let [,,a] = ['1','2','3'];
console.log(a); //3

let [b,,,c] = [1,2,3,4,5,6];    //不完全解构,6没用
console.log(b); //1
console.log(c); //4

const [d,...e] = [1,2,3,4,5,6];
console.log(d); //1
console.log(e); //[2,3,4,5,6]
```



如果解构失败,值就是`undefined`

```
let [a] = [];
console.log(a); //undefined

const [b,c,d] = [3];
console.log(b); //3
console.log(c); //undefined
console.log(d); //undefined
```



如果右边不是数组的形式,则会报错

```
const [a] = 1;          //1 is not iterable
const [b] = false;
const [c] = NaN;
const [d] = {};         //本身不具备Iterator接口
const [e] = null;
const [f] = undefined;
```



> 提示: 转为对象以后不具备`Iterator`接口,或者本身不具备`Iterator`接口.

默认值

```
const [a = 1] = [];
console.log(a);         //1

const [b = 1] = [4];    
const [d = 1] = [null];
const [e = 1] = [{}];

const [c = 1] = [undefined];

console.log(b);         //4     默认值失效
console.log(d);         //null  默认值失效
console.log(e);         //Object {  } 默认值失效

console.log(c);         //1     默认值有效
```



> 提示: 如果右边的数组中的值不严格等于`undefined`,那么左边的默认值是不会生效的.例如`4`和`null`不严格等于`undefined`,所以默认值都失效了.

如果默认值是一个表达式

```
function f(){
    console.log('aaa');
}

const [a = f()] = [1];
console.log(a);         //1 默认值失效
    
const [b = f()] = [];   //aaa, f()执行了   
```



默认值引用解析赋值的其他变量,但是变量必须已经声明(从左到右)

```
let [a,b=a] = [2];
console.log(a); //2
console.log(b); //2

let [c=d,d] = [1,2];
console.log(c); //1 右边的值不等于undefined,左边的默认值失效
console.log(d); //2

let [e=f,f] = [1];
console.log(e); //1
console.log(f); //undefined

let [g=h,h=1] = []; //h is not defined
```



### Object解构

数组是按顺序解析的,对象的属性没有次序,左边需要赋值的变量必须和右边的对象中的属性同名,这样才能一一匹配.

```
let {a,b} = {b:'2',a:'1'};
console.log(a); //1
console.log(b); //2 

let {c} = {b:'2',a:'1'};
console.log(c); //undefined
```



完整的写法

```
var {a : v_a, b: v_b} = {b:'2',a:'1'};
console.log(v_a);   //1
console.log(v_b);   //2
```



> 提示:采用这种方式赋值的时候真正被赋值的是后者`v_a`而不是`模式a`.

以下的解构中的`a`和`b`是模式,所以不会报错,但是解构中的`c`是变量,属于重复声明,所以报错.

```
let a,b;
let {a : v_a, b: v_b} = {b:'2',a:'1'};
console.log(v_a);   //1
console.log(v_b);   //2

let c;
let{c} = {c:1};     //Identifier 'c' has already been declared
```



以下的`a`和`b`并不是变量,而是一种匹配模式而已

```
let {a : v_a, b: v_b} = {b:'2',a:'1'};
console.log(v_a);   //1
console.log(v_b);   //2
console.log(a);     //a is not defined
console.log(b);     //b is not defined

let {c,d} = {c:'1',d:'2'};
console.log(c);     //1
console.log(d);     //2
```



嵌套匹配

```
let obj = {
    a:[
        '1',
        {
            b:'2'
        }
    ]
};

let {
    a:[
        v_a,
        {
            b:v_b
        }
    ]
} = obj;

console.log(v_a);   //1
console.log(v_b);   //2
```



> 提示: 和上面的一样,此时的`a`和`b`并不是变量,而是一种匹配模式.

对象的解构也可以有默认值

```
let {a=1,b} = {b:3};
console.log(a); //1

let {c=3} = {c:null};
console.log(c); //null
```



### String解构

```
const [a,b,c] = 'abc';
console.log(a); //a
console.log(b); //b
console.log(c); //c

let {length:len} = 'hello';
console.log(len);
```



> 提示: 'hello'如果转化为对象有一个length属性,所以可以赋值这个属性

### Number or Boolean解构

先将`number`或者`boolean`转换为**包装**对象

```
let {parseInt: pI} = 123;
console.log(pI === Number.prototype.parseInt);  //true
```



> 提示: 相当于最开始的对象解构的同名属性匹配. 当然由于`null`和`undefined`无法转化对象,对它们解析时会报错.

### Function解构

函数的参数解构

```
function add({a,b,c}){
    return a + b + c.d;
}
let sum = add({a:1,b:2,c:{d:3}});

console.log(sum);       //6
```



> 提示: 如果是`JSON`对象,这种方法就非常有用.当然返回的时候同样可以使用这种方式解构.

```
//Array.prototype.map(function(item,index,arr));
//该方法返回的是新数组
//把[1,2]和[3,4]各看成数组的元素
let arr = [[1,2],[3,4]].map(([a,b]) => a + b);
console.log(arr);   //[3,7]
```



默认值

```
function f({x=0,y=1} = {}){
    return [x,y];
}

console.log(f({x:1,y:2}));  //[1,2]
console.log(f({x:1}));      //[1,1]
console.log(f({}));         //[0,1]
console.log(f());           //[0,1]
```



注意如果是设置函数参数的默认值,结果将是完全不一样的

```
function f({x,y} = {x:0,y:1}){
    return [x,y];
}

console.log(f({x:1,y:2}));  //[1,2]
console.log(f({x:1}));      //[1,undefined]
console.log(f({}));         //[undefined,undefined]
console.log(f());           //[0,1]
```



和前面一样,如果值严格等于`undefined`,就会函数的参数的解构的默认值将会生效

```
var arr = [1,undefined,3].map((x = 2) => x);
console.log(arr);   //[1,2,3]
```



### ()问题

解构赋值虽然很方便,但是解析起来并不容易.对于编译器来说,一个式子到底是模式,还是表达式,没有办法从一开始就知道,必须解析到（或解析不到）等号才能知道.由此带来的问题是,如果模式中出现圆括号怎么处理.`ES6`规定,只要有可能导致解构的歧义,就不得使用圆括号.建议只要有可能,就不要在模式中放置圆括号.

- 变量声明语句中,不能带有圆括号.
- 函数参数中,模式不能带有圆括号.
- 赋值语句中,不能将整个模式,或嵌套模式中的一层,放在圆括号之中.

### 用途

- 交换变量的值

```
let [x,y] = [1,2];
[x,y] = [y,x];
console.log(x); //2
console.log(y); //1
```



- 从函数返回多个值

```
 function f(){
    return [1,2,3];
}

let [a,b,c] = f();
console.log(a); //1
```



```
function f2(){
    return {
        a:2,
        b:3
    };
}

var {a,b} = f2();
console.log(a);     //2
```



> 提示: `ES5`中的函数如果要返回多个值只能是数组或者对象的形式,否则只能返回单个值.

- 函数参数的定义

```
function f([x,y,z]){
    return x + y + z;
}

f([1,2,3]);
```



> 提示: 传入参数也可以快速的一一对应起来.

- 提取`JSON`数据

```
var jsonD = {
    name: 'ziyi2',
    age: '23'
};

let {name,age} = jsonD;

console.log(name);  //ziyi2
console.log(age);   //23
```



> 提示: 可以快速的提取`JSON`对象的数据或者给函数传入参数.

- 函数参数的默认值

> 提示: 可以避免在函数中使用 `var arg = arg || 'default arg';`

- 遍历Map解构

```
var map = new Map();
map.set('name','ziyi2');
map.set('age',23);

for(let[key,value] of map){
    console.log(key + '-' + value);     //name-ziyi2 age-23
}

for(let[key] of map){
    console.log(key);                   //name age
}
    
for(let[,value] of map){                //ziyi2 23
    console.log(value);
}
```



> 提示: 何部署了`Iterator`接口的对象,都可以用`for...of`循环遍历.`Map`结构原生支持`Iterator`接口.

- 输入模块的指定特定的方法

```
const  {f1,f2,...} = require("source");
```



### String扩展

### 遍历器接口

```
let str = 'hello es6';

for(let codePoint of str){
    console.log(codePoint); //h e l l o  e s 6
}
```



### at

用于替代`charAt`方法,可以识别码点大于0xFFFF的字符.

```
let str = 'hello es6';
str.at(0);  //h
```



### includes,startsWith,endsWith

```
let str = 'hello es6';
console.log(str.includes('hel'));       //true
console.log(str.startsWith('h'));       //true
console.log(str.startsWith('hello'));   //true
console.log(str.endsWith('es6'));       //true
console.log(str.endsWith('6'));         //true

//fun(str,startIndex)
console.log(str.includes('hel',2));     //false
console.log(str.includes('llo',2));     //true
```



### repeat

```
//repeat(copyCount)
let str = 'hello es6 ';
console.log(str.repeat(3));     //hello es6 hello es6 hello es6 
console.log(str.repeat(3.5));   //hello es6 hello es6 hello es6 
console.log(str.repeat('3.5')); //hello es6 hello es6 hello es6 
console.log(str.repeat(-0.9));  //''
console.log(str.repeat(NaN));   //''
console.log(str.repeat('str')); //''
console.log(str.repeat(-2));    //Invalid count value
```



### 模板字符串

模板字符串中的空格和换行都会被保留

```
//repeat(copyCount)
let str = `我是大傻瓜
我是大傻瓜`;

console.log(str);
//我是大傻瓜
//我是大傻瓜
```



直接输出模板而不再使用`+`号连接

```
<ul id='list'>
</ul>

<ul id='list1'>
</ul>

var name = 'ziyi2',
    age = 23;

$('#list').html(
    '<li>' + name + '</li>' + 
    '<li>'+ age + '</li>'
);

$('#list1').html(`
    <li>${name}</li>
    <li>${age}</li>
`);
```



在模板字符串中嵌入变量需要放在`${}`中

```
let name = 'Bob',
    time = 'tody';
console.log(`Hello ${name}, how are you ${time}`);  //Hello Bob, how are you tody
```



在`${}`中可以是任意的`JavaScript`表达式,可以进行运算也可以引用对象的属性

```
let name = 'ziyi2',
    age = 23;

function f(){
    return name + age;
}


$('#list').html(`
    <li>${name + age}</li>
    <li>${f()}</li>
`.trim());
```



报错情况

```
let name = 'ziyi2',
    msg = `Hello, ${name}`,     //Hello,ziyi2
    hello = `Hello, ${age}`;    //age is not defined
```



模块字符串可以嵌套

```
let infos = [{name:'ziyi2',age:23},{name:'ziyi3',age:24},{name:'ziyi4',age:25}];

const temp = infos => ` 
    ${infos.map(info => `
        <li>${info.name}</li>
        <li>${info.age}</li>
    `
    ).join('')}
`;

$('#list').html(temp(infos));
```



### 标签模板

紧跟在函数后面,该函数将被调用来处理这个模板字符串,该功能称为"标签模板"功能.这其实就是函数的一种特殊调用形式,"标签"是指函数,而模板字符串是函数的参数.

```
alert  `Hello`;
alert('Hello'); 

console.log `Hello`;    //['Hello']
```



但是如果模板字符串中还有变量,函数就会先处理成多个参数,第一个是字符串数组,第二个开始就是变量...

```
let name = 'ziyi2';
let age = 23;


function tag(s,v_name,v_age){
    console.log(s);         //['Hello','your age is','']
    console.log(v_name);    //ziyi2
    console.log(v_age);     //23
}

tag `Hello ${name}, your age is ${age}`;
```



> 提示: `tag`函数实际上以这样的形式调用, `tag(['Hello','your age is',''],name,age);`

另外一种写法

```
let name = 'ziyi2';
let age = 23;


function tag(s,...args){
    console.log(s);             //['Hello','your age is','']
    console.log(args[0]);       //ziyi2
    console.log(args[1]);       //23

    console.log(arguments[0]);  //['Hello','your age is','']
    console.log(arguments[1]);  //ziyi2
    console.log(arguments[2]);  //23
}

tag `Hello ${name}, your age is ${age}`;
```



这种方式还可以假象代码以其他方式运行,例如

```
jsx `
    //以jsx代码的方式运行   
`


java `
    //以java代码的方式运行  
`

xml `
    //以xml代码的方式运行   
`
```



以这种方式运行的参数中,刚刚说了第一个参数是字符串数组,这个数组还有一个属性`raw`,这个参数是对字符串数组进行了转义处理过的数组

```
let name = 'ziyi2';
let age = 23;


function tag(s,...args){
    console.log(s);             //['Hello','your age is','']
    console.log(s.raw);         

    console.log(args[0]);       //ziyi2
    console.log(args[1]);       //23

    console.log(arguments[0]);  //['Hello','your age is','']
    console.log(arguments[1]);  //ziyi2
    console.log(arguments[2]);  //23
}

tag `Hello ${name},\n your age is ${age}`;
```



### String.raw

```
console.log(String.raw `Hi\nHi`);   //Hi\nHi
```



## Number扩展

### Number.isFinite,Number.isNaN

> 提示: 与传统方法`isFinite`和`isNaN`的区别在于传统方法先转换为`Number`再判断,而新方法只对数值有效.

### Number.parseInt,Number.parseFloat

> 提示: 将全局的`parseInt`和`parseFloat`方法移植到了`Number`对象上,更加模块化.

### Number.isInteger

```
console.log(Number.isInteger(2));   //true
console.log(Number.isInteger('2')); //false
console.log(Number.isInteger(2.0)); //true
console.log(Number.isInteger(2.1)); //false
```



### Number.EPSILON

引入一个极小的常量

```
console.log(Number.EPSILON);        // 2.220446049250313e-16

if(0.1 + 0.2 - 0.3 < Number.EPSILON){
    console.log(0.1 + 0.2 - 0.3);   //5.551115123125783e-17
    console.log(true);              //true
} 
```



### Math.trunc

去除小数部分

```
console.log(Math.trunc(4.23423423423)); //4
```



### Math.sign

判断正负数,0,NaN,-0

### Math.cbrt

计算一个数的立方根

### Math.hypot

计算所有参数的平方和的平方根

### 指数运算

```
console.log(2 ** 3);    //8
let a =  2;
console.log(a **= 3);   //8
```



## Array扩展

### Array.from

把类数组转化为数组,例如类似数组的对象和可遍历的对象

```
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '100': 'c',
    length: 3
};

let arr = Array.from(arrayLike);
console.log(arr);   // ["a", "b", undefined]
```



`NodeList`集合就是类数组对象,还有`arguments`对象也是,都可以转化为真正的数组

```
<p>1</p>
<p>2</p>
<p>3</p>

let lists = document.querySelectorAll('p');

Array.from(lists).forEach(function(item,index,list){
    console.log(item.innerHTML);
});

lists.forEach(function(item){   //lists.forEach is not a function
    console.log(item.innerHTML);
});
```



> 提示: `lists`是类数组,本身没有数组的`forEach`方法.

`Iterator`接口的数据结构,`Array.from`都能将其转为数组.

```
let namesSet = new Set(['ziyi2','ziyi3']);

namesSet.forEach(function(item){    
    console.log(item);          //ziyi2,ziyi3
});

console.log(namesSet.length);   //undefined

console.log(Array.from(namesSet).length);   //2
```



`(...)`扩展运算符也可以将某些数据转为数组

```
function f(){
    let args = [...arguments];
    args.forEach(function(arg){
        console.log(arg);       //1,2,3,4,5
    });

    arguments.forEach(function(item){   //arguments.forEach is not a function
        console.log(item);
    })

    console.log(args.length);   //5

}

f(1,2,3,4,5);
```



`Array.from`方法还支持类似数组的对象.所谓类似数组的对象,本质特征只有一点,即必须有`length`属性.因此,任何有`length`属性的对象,都可以通过`Array.from`方法转为数组,而此时扩展运算符就无法转换.

```
let obj = {
    length: 3
};

console.log(Array.from(obj)); //[undefined, undefined, undefined]
```



> 提示: 扩展运算符转换不了这个对象.

`Array.from`还有第二个参数

```
function f(){
    console.log(Array.from(arguments, x => x*x));       //[1, 4, 9, 16]
    //类似于
    console.log(Array.from(arguments).map(x => x*x));   //[1, 4, 9, 16]
}

f(1,2,3,4);
```



稀疏数组转化为非稀疏数组

```
console.log(Array.from([1,,2,,3], item => item || 0));  //[1, 0, 2, 0, 3]
```



第一个参数指定了第二个参数运行的次数

```
console.log(Array.from({length:3}, () => 'ziyi2')); //["ziyi2", "ziyi2", "ziyi2"]
```



### Array.of

```
let arr = Array.of(1,2,3,4);    //[1, 2, 3, 4]
console.log(arr);

arr = Array.of(3);
console.log(arr);               //[3]

arr = Array();
console.log(arr);               //[]

arr = Array(3);
console.log(arr);               //[undefined, undefined, undefined]

arr = Array(3,3);
console.log(arr);               //[3, 3]
```



> 提示: `Array.of`就是为了避免`Array`的方法因为参数不同而导致的不同结果.`Array.of`基本上可以替代`Array`或`new Array`. 它的行为非常统一.

### Array.copyWithin

将制定位置的成员赋值到其他位置`Array.prototype.copyWithin(targetIndex,start=0,end=this.length);`

- targetIndex 从该位置开始替换数据(会覆盖原有位置的元素)
- start 从该位置读取数据
- end 到该位置前停止读取数据

```
let arr = [1,2,3,4,5].copyWithin(0,4);  //[5,2,3,4,5]
console.log(arr);

arr = [1,2,3,4,5].copyWithin(0,3);  //[4,5,3,4,5]
console.log(arr);
```



### Array.find,Array.findIndex

用于找出符合条件的数组的成员.

```
let item = [1,2,3,4].find((n) => (console.log(n), n>1));    //1 2
console.log(item);  //2
```



> 提示: 执行`n`遍,直到第一个返回`true`的项则停止遍历并返回这个项.如果遍历完了仍然没有找到符合条件的值,则返回`undefined`. `find`和`forEach`等方法一样,回调函数中都有三个参数`item,index,arr`.

用于找出符合条件的数组的成员的位置.

```
let item = [1,2,3,4].findIndex((n) => (console.log(n), n>1));   //1 2
console.log(item);  //1  arr[1] = 2
```



> 提示: 如果没有找到匹配的数组项,则返回`-1`.这两个方法都可以接受第二个参数,用来绑定回调函数的`this`对象.

### Array.fill

可以抹去之前的数据

```
console.log([1,2,3].fill(4));       //[4, 4, 4]
console.log(new Array(3).fill(4));  //[4, 4, 4]
```



> 提示: 初始化的时候应该非常有用吧!

### Array.entries,Array.keys,Array.values

```
let arr = ['a','b','c'];

for(let key of arr.keys()){
    console.log(key);   //0 1 2
}

for(let item of arr.values()){
    console.log(item);  //error?
}


for(let [key,value] of arr.entries()){
    console.log([key,value]);   //[0, "a"] [1, "b"] [2, "c"] 
}   
```



### Array.includes

`ES7`的方法,`Babel`已经支持,该方法判断参数是否存在于数组中

```
console.log([1,2,3].includes(3));   // true
```



没有该方法之前,通常使用`indexOf`方法检测是否包含某个值,`indexOf`方法有两个缺点,一是不够语义化,它的含义是找到参数值的第一个出现位置,所以要去比较是否不等于-1,表达起来不够直观.二是,它内部使用严格相等于运算符`（===）`进行判断,这会导致对`NaN`的误判.

```
console.log([NaN].indexOf(NaN));    //-1
console.log([NaN].includes(NaN));   //true
```



### 稀疏数组

```
console.log((0 in [undefined,undefined]));  //true
console.log((0 in [,,]));                   //false
```



> 提示: 第二个是稀疏数组,第一个不是.

`ES5`对于稀疏数组的处理:

- `forEach,filter,every,some`都会跳过空位
- `map`遍历的时候跳过空位,但是返回的时候会保留这个位置的空
- `join`和`toString`会将空位视为`undefined`,而`undefined`会被处理成空字符串

```
var arr = [1,,2,,3];
arr.forEach( (item,index) => console.log(item));    //1,2,3
console.log(arr.filter(item => true));              //[1, 2, 3]
console.log(arr.every(item => item >= 1));          //true

console.log(arr.map(item => item*item));            //[1, undefined, 4, undefined, 9]
console.log(arr.join('-'));                         //1--2--3
console.log(arr.toString());                        //1,,2,,3
```



```
ES6`明确将空位转为`undefined`,`Array.from`方法会将数组的空位,转为`undefined
console.log(Array.from([1,,2,,3])); //[1, undefined, 2, undefined, 3]
console.log([...[1,,2,,3]]);        //[1, undefined, 2, undefined, 3]
```



`for...of`循环会遍历空位

```
let arr = [,,,];

for(let key of arr){
    console.log(key);   //undefined undefined undefined
}
```



> 提示: 其他的函数也可以试试是什么情况.

## Function扩展

形参的初始值

```
function f(x,y='es6'){
    console.log(x + y);
}

f('hello ');            //hello es6
f('hello ','world');    //hello world
f('hello ','');         //hello 
```



构造函数的形参

```
function Person(name='ziyi2',age=23){
    this.name = name;
    this.age = age;
    
    let name;       //Identifier 'name' has already been declared
}

let p = new Person();
console.log(p);     // Person { name="ziyi2",  age=23}
let p2 = new Person('ziyi3');
console.log(p2);    // Person { name="ziyi3",  age=23}
```



> 提示: 函数的形参是默认声明的,所以不能在函数中再次声明,否则会报错.

解构默认值,注意不是函数的形式参数的初始值

```
function Person({name='ziyi2',age=23}){
    this.name = name;
    this.age = age;
}

var p = new Person({});
console.log(p);         //Person { name="ziyi2",  age=23}
var p1 = new Person({name:'ziyi3'});
console.log(p1);        //Person { name="ziyi3",  age=23}

var p2 = new Person();  //Cannot match against 'undefined' or 'null'.
```



```
function ajax(url, {method = 'GET', data = {}, type = 'text/plain'}){
    console.log(method);
}

ajax('/add',{});    //GET
ajax('/add');       //Cannot match against 'undefined' or 'null'.
```



> 提示: 这种情况下不能省略解构需要的第二个参数,否则就会报错,如果结合解构默认值和函数形式参数的初始值,就可以忽略这个参数了.

解构默认值和函数形式参数初始值结合使用

```
function f({x=0,y=0} = {}) {        //函数的形参初始值是空对象,而结构的默认值是0,0
    console.log([x,y]);
}

function f1({x,y} = {x:1,y:1}) {    //函数的形参的初始值是对象{x:1,y:1},而结构没有默认值
    console.log([x,y]);
}

function f2({x=0,y=0} = {x:1,y:1}) {    
    console.log([x,y]);
}


f();        //[0, 0]    生效的是解构的默认值
f1();       //[1, 1]    生效的是形参的初始值
f2();       //[1, 1]    生效的是形参的初始值,因为这个时候没有解构赋值,所以形参初始值生效

f(2,2);     //[0, 0] 生效的是解构的默认值,传入的参数因为不是对象而无效
f1(2,2);    //[undefined, undefined] 解构没有赋默认值,传入的参数也不是对象
f2(2,2);    //[undefined, undefined] 解构没有赋默认值,传入的参数也不是对象

f({x:2});   //[2, 0] 覆盖了形参的初始值{},同时解构的默认值生效了,y为默认值0
f1({x:2});  //[2, undefined],因为传入的是{x:2}对象,覆盖了默认的初始对象{x:1,y:1},同时因为解构没有初始值所以y是undefined     
f2({x:2});  //[2, 0] 覆盖了形参初始值{x:1,y:1},同时解构的默认值生效了

f({});      //[0, 0] 生效的是解构的默认值
f1({});     //[undefined, undefined] {}覆盖了默认的初始对象{x:1,y:1}
f2({});     //[0, 0] 解构的默认值生效了,形参的初始对象也被覆盖了
```



默认的参数最好放在最后,方便赋值

```
function f(x=1,y){
    console.log([x,y]);
}

f();            //[1, undefined]
f(5);           //[5, undefined]
f(undefined,1); //[1, 1]    
//f(,1);        //Unexpected token ,

function f1(x,y=1){
    console.log([x,y]);
}

f1();           //[undefined, 1]
f1(1);          //[1, 1]
```



### 函数的length属性

```
function f(x=1,y){
    console.log([x,y]);
}

function f1(x,y=1){
    console.log([x,y]);
}

function f2(x=1,y,z){
    console.log([x,y]);
}

function f3(x,y,z=1){
    console.log([x,y]);
}

function f4(...args){

}

console.log(f.length);  //0 如果默认参数没放在末尾,那么没有指定默认值得参数个数将会出错
console.log(f1.length); //1 函数未指定默认值得参数个数为1
console.log(f2.length); //0
console.log(f3.length); //2
console.log(f4.length); //0 不计算的
```



### 作用域

```
let x=1;

function f(x=2,y=x){
    console.log(y);
}

f();    //2
```



```
let x=1;

function f(y=x){
    console.log(y);
}

f();    //1
```



```
function f1(f0 = x => a){
    let a = 'inner';
    console.log(f0());
}

f1();   //inner
```



```
var a = 'a_global';
var b = 'b_global';

function f(a,c = () => console.log(a + b)){
    var a = 'a_inner';
    var b = 'b_inner';
    c();                //a_innerb_inner
    console.log(a);     //a_inner
}

f();
```



### 应用

指定函数必须有参数

```
function throwArgsError(){
    throw new Error('Missing parameter!');
}

function f(a = throwArgsError()){
    console.log(a);
}
    
f(1);   //1
f();    //Error: Missing parameter!
```



> 提示: 如果传入参数,形参初始值就生效了,动态运行函数抛出`error`.

指定函数的参数可以省略

```
function f(a = undefined){
    console.log(a);
}
    
f();    //undefined
```



### rest参数

`ES6`引入了`rest`参数, 以`...变量名`的形式获取函数的多余参数,这样就不需要使用`arguments`对象了.`rest`参数中的变量代表一个数组,所以数组特有的方法都可以用于这个变量.

```
function f(...args){
    let sum = 0;

    for(let arg of args){
        sum += arg;
    }

    
    console.log(sum);   //15
}

f(1,2,3,4,5);
```



> 提示: `rest`参数之后不能再有其他参数,否则会报错.同时函数的`length`属性不包括`rest`参数.

### (...)扩展运算符

`...数组`扩展运算符可以认为是rest参数的逆运算,将一个数组转为用逗号分隔的参数序列. 注意与`rest`参数的区别.

```
function sum(x,y,z){
    console.log(x+y+z);
}

function sum1(...parms){
    console.log(parms[0] + parms[1] + parms[2]);
}

let arr = [1,2,3];
console.log(...arr);    //1 2 3
sum(...arr);            //6
sum1(1,2,3);            //6
```



### 替代数组的apply方法

扩展运算符`...`可以展开数组,所以不需要`apply`方法将数组转为函数的参数,例如`ES5`中的写法

```
function f(x,y,z){
    console.log(x + y + z);
}

let arr = [1,2,3];

f.apply(null,arr);  //6
```



> 提示: `apply`和`call`作用相同,只是`apply`的第二个参数传入的是数组,而`call`从第二个开始是要传入函数的实际参数,所以`call`第二个参数开始后面可以有很多参数.两个函数的第一个参数都是传递`this`的指向对象.

`ES6`中的写法

```
function f(x,y,z){
    console.log(x + y + z);
}

let arr = [1,2,3];

f(...arr);  //6
```



一个实际的例子

```
let arr = [1,2,2,3,4,5,6,7,7,9];
console.log(Math.max(1,2,2,3,4,5,6,7,7,9)); //9
console.log(Math.max.apply(null,arr));      //9
console.log(Math.max(...arr));              //9
```



> 提示: `Math.max`方法传入的参数不能是数组.

```
let arr = [1,2];
let arr1 = [3,4];
Array.prototype.push.apply(arr,arr1);
console.log(arr);   //[1, 2, 3, 4]

arr.push.apply(arr,arr1);
console.log(arr);   //[1, 2, 3, 4, 3, 4]

arr.push(...arr1);
console.log(arr);   //[1, 2, 3, 4, 3, 4, 3, 4]
```



### 扩展运算符的应用

- 数组合并新写法

```
let arr = [1,2];
let arr1 = [3,4];
var arr2 = arr.concat(arr1);
console.log(arr2);              //ES5 [1, 2, 3, 4]
console.log([...arr,...arr1]);  //ES6 [1, 2, 3, 4]
```



- 与解构赋值结合

```
let list = [1,2,3,4,5];
let [a,...rest] = list;

//ES6
console.log(a);     //1
console.log(rest);  //[2, 3, 4, 5]

//ES5
let b = list[0];
let c = list.slice(1);
console.log(b);     //1
console.log(c);     //[2, 3, 4, 5]
```



- 实现了`Iterator`接口的对象 详细的查看`Array.from`内容.
- `Map`和`Set`,`Generator`

```
let map = new Map([
    [1,'ziyi2'],
    [2,'ziyi3'],
]);

let arr = [...map.keys()];
console.log(arr);           //[1, 2]


var go = function* (){
    yield 1;
    yield 2;
    yield 3;
};

console.log([...go()]);     //[1, 2, 3]

let person = {
    name: 'ziyi2',
    age: 34
};

console.log([...person]);   //person is not iterable
```



- name属性

### `=>` 函数

```
let f= v => v;
console.log(f(1));  //1

//等同于
function f(v){
    return v;
}
```



如果不需要形参或者多个参数,就用`()`代替参数部分

```
let f= () => 5;
console.log(f());       //5

let f1= (x,y) => x+y;
console.log(f1(3,4));   //7
```



> 提示: 单条语句时省略了`return`关键字.

如果代码块多余一条语句,就要用`{}`包裹起来,并且不能省略返回时的`return`关键字

```
let f= (x) => {
    let a = 1;
    return a + x;
};

console.log(f(5));  //6
```



注意事项

- `this`体内的`this`对象就是定义时所在的对象(死固定的),而不是运行时那个调用此方法的对象

```
function f(){
    setTimeout(() => {
        console.log(this.name);
    },100);
}

function f1(){
    setTimeout(function(){
        console.log(this.name);
    },100);
}


var name = 'window';
f.call({name:'ziyi2'});     //ziyi2
f1.call({name:'ziyi2'});    //window
```



> 提示: 100ms后执行的函数,在`f`中使用了箭头函数后,`this`就指向了`f`中绑定的对象`{name:'ziyi2'}`,而不使用箭头函数则是全局对象.

在箭头函数中不存在`this,arguments,super,new.target`.都是指向包含它的函数对象的相应的该值!

```
function f(){
    setTimeout(() => {
        console.log(arguments);
    });
}

function f1(){
    setTimeout(function () {
        console.log(arguments);
    });
}

f(1,23,3,4);    //[1, 23, 3, 4]
f1(1,23,3,4);   //[] 
```



> 提示: 箭头函数本身不存在变量`arguments`,所以该变量是`f`函数的属性.由于箭头函数没有`this`,所以也不能使用`call,apply,bind`等方法改变`this`的指向.

- 不可以当做构造函数
- 不可以使用`arguments`对象
- 不可以使用`yield`命令,不能用作`Generator`函数

### 嵌套的箭头函数

```
const plus = a => a + 1;
const mult = a => a * a;
console.log(mult(plus(5))); //36
```



### 函数绑定

箭头函数可以绑定this对象,而不会随着运行时调用对象的变化而变化.`ES7`为了减小显示绑定(`call,apply,bind`)的用法,提出了**函数绑定**.

### 尾调用优化

略

## 对象的扩展

### 属性简写

```
let a = '1';
let b = {a};
console.log(b); //Object { a="1"}
```



```
function f(x,y){
    console.log({x,y}); // console.log({x:x,y:y});
}
f(1,2); //Object { x=1,  y=2}
```



> 提示: `{}` 内的是属性名,同时这个属性名作为变量的值作为属性值.需要注意的是`{}`的属性名总是字符串.

### 方法简写

```
var o = {

    sum(x,y) {  //sum: function(x,y) {
        console.log(x + y);
    },

    mult(x,y) {
        console.log(x * y);
    }
};

o.sum(1,2); //3
```



如果是`Generator`函数

```
var gen = {
    *m() {
        yield 'hello world';
    }
};
```



对象字面量的属性名可以是`JavaScript`表达式

```
var prop = 'ziyi2';
function f(){
    return 'prop';
}

let obj = {
    [prop + '1']: 'ziyi21',
    [f() + '2']: 'prop2'
};

console.log(obj);   //Object { ziyi21="ziyi21",  prop2="prop2"}
```



> 提示: 方法作为一个变量的属性也可以使用`JavaScript`表达式.

### 方法的name属性

### Object.is

与 `===` 的作用基本一致,同时使`NaN`等于自身, `+0`和`-0`不相等.

```
console.log(+0 === -0);         //true
console.log(NaN === NaN);       //false
console.log(Object.is(+0,-0));  //false
console.log(Object.is(NaN,NaN));//true
```



### Object.assign

第一个参数是目标对象,之后的参数都是源对象

```
let target = {a:1};
let source1 = {b:2};
let source2 = {c:3};
Object.assign(target,source1,source2);
console.log(target);    //Object { a=1,  b=2,  c=3}
```



> 提示: 类似于`extend`函数的作用,如果有同名属性,目标对象的属性值会被覆盖.

```
let target = {a:1};
let source1 = {a:2};
let source2 = {a:3};
Object.assign(target,source1,source2);
console.log(target);    //Object {a=3}
```



> 提示: 该方法只拷贝源对象的自身属性(不拷贝继承属性),也不拷贝不可枚举的属性.

该方法实现的是浅拷贝,而不是深拷贝,例如如果源对象某个属性的值是对象,那么目标对象拷贝得到的这个是这个对象的引用

```
let x = {a:{b:2}, c:3};
let y = {};

Object.assign(y,x);

x.a.b = 111;
x.c = 222;
console.log(y.a.b); //111 变了
console.log(y.c);   //3
```



补充一下深拷贝和浅拷贝

```
//浅拷贝
let x = {a:1,b:2};

let y = x;
x.a = 2;            //x对象变了y对象也变了
console.log(y.a);   //2 x对象和y对象引用同一个地址


//深拷贝
let c = {};

for(let key in x){
    c[key] = x[key];
}

x.a = 3;
x.b = 4;            //x和c对象不是引用同一个地址空间
console.log(c);     //Object { a=2,  b=2}
```



当处理数组时,会把数组的索引当成属性来执行

```
let arr = [1,2,3,4,5];
let arr1 = [2,1];
Object.assign(arr,arr1);
console.log(arr);   //[2, 1, 3, 4, 5]
```



### 用途

- 为对象添加属性

```
class Person {
    constructor(name,age) {
        Obeject.assign(this,{name,age});
    }
}
```



- 为对象添加方法

```
class Person {
    constructor(name,age) {
        Obeject.assign(this,{name,age});
    }
}


Object.assign(Person.prototype, {
    fun1() {

    },
    fun2() {

    }
});

//类似于
Person.prototype.fun1 = function() {};
Person.prototype.fun2 = function() {};
```



- 克隆对象

```
function clone(obj){
    return Object.assign({},obj);
}

//拷贝带源对象的继承值
function clone1(obj){
    let proto = Object.getPrototypeOf(obj);
    return Object.assign(Object.create(proto),obj);
}
```



合并返回新对象

```
let merge = (...args) => Object.assign({},...args);
```



### 属性的可枚举性

- `for...in`循环：只遍历对象自身的和继承的可枚举的属性
- `Object.keys()`：返回对象自身的所有可枚举的属性的键名
- `JSON.stringify()`：只串行化对象自身的可枚举的属性

`Object.assign()`会忽略不可枚举属性.操作中引入继承的属性会让问题复杂化,大多数时候,我们只关心对象自身的属性.所以,尽量不要用`for...in`循环,而用`Object.keys()`代替.

### 属性的遍历

- `for...in` 只遍历对象自身的和继承的可枚举的属性（不含`Symbol`属性）.
- `Object.keys()` 包括对象自身的（不含继承的）所有可枚举属性（不含`Symbol`属性）. `Object.getOwnPropertyNames` 包含对象自身的所有属性（不含`Symbol`属性,但是包括不可枚举属性). `Object.getOwnPropertySymbols(obj)` 包含对象自身的所有`Symbol`属性.
- `Reflect.ownKeys(obj)` 返回所有的属性,不管属性名是`Symbol`还是字符串,不管是否可枚举.

遍历的次序规则

- 首先遍历所有属性名为数值的属性,按照数字排序
- 其次遍历所有属性名为字符串的属性,按照生成时间排序
- 最后遍历所有属性名为Symbol值的属性,按照生成时间排序

### `__proto__`,Object.setPrototypeOf,Object.getPrototypeOf

### `__proto__`

`__proto__`属性（前后各两个下划线）,用来读取或设置当前对象的prototype对象.目前,所有浏览器（包括IE11）都部署了这个属性.

### Object.setPrototypeOf

用来设置一个对象的`prototype`对象.它是`ES6`正式推荐的设置原型对象的方法.格式如下

```
Object.setPrototypeOf(obj,prototype)
```



### Object.getPrototypeOf

### Objetct.keys,Objetct.values,Objetct.entries

- `Objetct.keys` 遍历对象的（不含继承的）可遍历的属性名,返回的是数组
- `Objetct.values (ES7)` 遍历对象的（不含继承的）可遍历的属性值,返回的是数组
- `Objetct.entries (ES7)` 遍历对象的（不含继承的）可遍历的属性的键值对,返回的是数组

## Symbol

### 概述

`ES5`中的属性只能是字符串,在别人封装的对象的基础上,增加新的方法可能会出现重复,所以在`ES6`中新增了一种数据类型`Symbol`,也就是说在`ES6`中对象的属性可以是字符串也可以是`Symbol`类型.

> 提示: `ES5`中的数据类型有`Undefined`,`Null`,`Boolean`,`String`,`Number`,`Object`,在`ES6`中新增了`Symbol`.

凡是属性名是Symbol类型,这些属性就都是独一无二的.

```
let s = Symbol();
let s1 = Symbol();

obj = {};
obj[s] = '1';
obj[s1] = '2';

console.log(s === s1);  //false
```



> 提示: `Symbol`和`String`一样.由于它是基本数据类型,所以不需要使用`new`关键字.

即使传入的参数一样,也还是唯一的

```
let s = Symbol('ziyi2');
let s1 = Symbol('ziyi2');

console.log(s === s1);  //false
```



`Symbol`类型的数据不能隐式转换为字符串,所以在对象中使用属性的时候不能使用`.`运算符,因为`.`运算符后面跟的默认都是字符串.

```
let s = Symbol('ziyi2');
let s1 = Symbol('ziyi2');
 
var obj = {};
obj.s = '1';    //.后面的s其实是字符串
console.log(obj['s']);  

obj[s] = '2';

for(let key in obj){    //不能遍历Symbol属性
    console.log(key);   //s 这个是字符串属性
}

console.log(Object.getOwnPropertySymbols(obj));     //[Symbol(ziyi2)] 这个是Symbol属性
console.log(s + '12334');   //can't convert symbol to string
console.log(s.toString() + '1234'); //Symbol(ziyi2)1234
```



> 提示: 只能显示转化为字符串,由于`.`属性后面跟的是字符串,所以不能用`Symbol`变量用作对象的`.`属性.所以`Symbol`值必须放在方括号之中.

```
let s = Symbol('ziyi2');
 
obj = {

    [s](arg) {
        console.log('1');
    }
};

obj[s]('1');    //1
```



> 提示: `Symbol`用来表示对象的属性只能采用`[]`表示法.

可以用来定义不同的常量

```
const status = {
    success: Symbol('success'),
    error: Symbol('error')
};

function f(sta){
    switch(sta){
        case status.success:
            console.log('1');
            break;

        case status.error:
            console.log('2');
            break;

        default:
            break;      
    }
}

f(status.success);  //1
f(status.error);    //2 
```



### 属性遍历

- `Object.getOwnPropertySymbols`
- `for...in`
- `Object.getOwnPropertyNames`
- `Reflect.ownKeys`
- `Object.keys`

> 提示: 注意`Object.keys`和`for...in`的区别.

### Symbol.for,Symbol.keyFor

如果想使用同一个`Symbol`值,`Symbol.for`方法接受一个字符串作为参数,然后先搜索有没有该参数作为名称的`Symbol`值,有则返回,没有则创建一个该字符串为名称的`Symbol`值.

```
let s = Symbol('foo');
let s1 = Symbol.for('foo');
console.log(s === s1);  //false
let s2 = Symbol.for('foo');
console.log(s1 === s2); //true
```



`Symbol.for()`与`Symbol()`这两种写法,都会生成新的`Symbol`.它们的区别是,前者会被登记在全局环境中供搜索,后者不会.`Symbol.keyFor`用于返回登记的值

```
let s = Symbol('foo');
let s1 = Symbol.for('foo');
console.log(s === s1);  //false
let s2 = Symbol.for('foo');
console.log(s1 === s2); //true

console.log(Symbol.keyFor(s));  //undefined
console.log(Symbol.keyFor(s1)); //foo
```



防止导出的对象的属性被覆盖

```
const s = Symbol.for('foo');

function F(name){
    this.name = name;
}

if(!global[s]){
    global[s] = new F('ziyi2');
}

module.exports = global[s]; //导出的全局变量的该属性不会被修改
```



### 其他方法

- Symbol.hasInstance
- Symbol.isConcatSpreadable
- Symbol.species
- Symbol.match
- Symbol.replace
- Symbol.search
- Symbol.split
- Symbol.iterator
- Symbol.toPrimitive
- Symbol.toStringTag
- Symbol.unscopables

## Proxy和Reflect

### Proxy

用于修改某系操作的默认行为,可以理解为对目标对象做了拦截,访问对象之前,先执行拦截,所以可以对外界的访问行文进行过滤和改写.

生成`Proxy`实例,`target`表示要拦截的目标对象,`hander`用来定制拦截行为,注意`hander`也是一个对象

```
let proxy = new Proxy(target,handler);
```



```
let proxy = new Proxy({},{  //{}是对象
    get: function(target,property){
        return 11;
    }
});

console.log(proxy.name);    //11
console.log(proxy.age);     //11
```



> 提示: `hanlder`对象里的拦截函数都有两个参数,一个是目标对象`target`,一个是所要访问的属性`property`. 以上的拦截函数`get`总是返回11,所要访问任何属性都得到11.要使得`Proxy`起作用,必须针对`Proxy`实例（上例是`proxy`对象）进行操作,而不是针对目标对象（上例是空对象）进行操作.

如果`handler`没有设置任何拦截,那就等同于直接通向原对象.

```
let person = {
    name:'ziyi2',
    age: 11
};

let proxy = new Proxy(person,{});   //handler是一个空对象

proxy.name = 'ziyi3';       //相当于修改了person对象的name属性
proxy.home = 'Hangzhou';    //相当于给person对象创建了一个home属性并赋值

console.log(person.name);   //ziyi3
console.log(person.home);   //Hangzhou
```



> 提示: 此时访问`proxy`就等同于访问`person`.

Proxy实例也可以作为其他对象的原型对象

```
let proxy = new Proxy({},{
    get(target,property) {
        return 35;
    }
}); 

let obj = Object.create(proxy); //proxy是obj的原型对象

console.log(obj.name);  //35 
```



### 拦截方法

- `get(target,prop,receiver)` 拦截对象属性的读取,最后一个参数后续说明.
- `set(target,prop,value,receiver)` 拦截对象属性的设置,返回一个布尔值. ...
- `construct(target,args)` 拦截`Proxy`实例作为构造函数调用的操作,比如`new proxy(...args)`.

```
function createArray(...elements) {
  let handler = {
    get(target, propKey, receiver) {
      let index = Number(propKey);
      if (index < 0) {
        propKey = String(target.length + index);
      }
      return Reflect.get(target, propKey, receiver);
    }
  };

  let target = [];
  target.push(...elements);
  return new Proxy(target, handler);
}

let arr = createArray('a', 'b', 'c');
console.log(arr[-1]); //c
```



`construct`方法用于拦截`new`命令

```
var p = new Proxy(function(){},{
    construct(target,args) {
        return {value:args[0]*10};
    }
});

console.log(new p(2).value);    //20
```



> 提示:`construct`方法返回的必须是一个对象,否则会报错

### Reflect

将`Object`对象的一些明显属于语言内部的方法（比如`Object.defineProperty`）,放到`Reflect`对象上.未来的新方法将只部署在Reflect对象上.

修改某些`Object`方法的返回结果,让其变得更合理.比如,`Object.defineProperty(obj, name, desc)`在无法定义属性时,会抛出一个错误,而`Reflect.defineProperty(obj, name, desc)`则会返回`false`

让`Object`操作都变成函数行为.某些`Object`操作是命令式,比如`name in obj`和`delete obj[name]`,而`Reflect.has(obj, name)`和`Reflect.deleteProperty(obj, name)`让它们变成了函数行为.

```
let obj = {
    name: 'ziyi2'
};

console.log('name' in obj);             //true
console.log(Reflect.has(obj,'name'));   //true
```



```
let obj = {
    name: 'ziyi2'
};


console.log('name' in obj);             //true
console.log(Reflect.has(obj,'name'));   //true


Reflect.set(obj,'age',23);
console.log(obj.age);                   //23
```



`Reflect`对象的方法与`Proxy`对象的方法一一对应,只要是`Proxy`对象的方法,就能在`Reflect`对象上找到对应的方法.这就让`Proxy`对象可以方便地调用对应的`Reflect`方法,完成默认行为,作为修改行为的基础.也就是说,不管`Proxy`怎么修改默认行为,你总可以在`Reflect`上获取默认行为

```
Proxy(target, {
  set: function(target, name, value, receiver) {
    var success = Reflect.set(target,name, value, receiver);
    if (success) {
      log('property ' + name + ' on ' + target + ' set to ' + value);
    }
    return success;
  }
});
```



上面代码中,`Proxy`方法拦截`target`对象的属性赋值行为.它采用`Reflect.set`方法将值赋值给对象的属性,然后再部署额外的功能

### Reflect对象的方法

- `Reflect.get(target, name, receiver)` 查找并返回`target`对象的`name`属性,如果没有该属性,则返回`undefined`.如果`name`属性部署了读取函数,则读取函数的`this`绑定`receiver`

```
var obj = {
  get foo() { return this.bar(); },
  bar: function() { ... }
};

// 下面语句会让 this.bar()
// 变成调用 wrapper.bar()
Reflect.get(obj, "foo", wrapper);
```



- `Reflect.set(target, name, value, receiver)` ...

## Set和Map

### Set

ES6提供了新的数据结构`Set`.它类似于数组,但是成员的值都是唯一的,没有重复的值.`Set`本身是一个构造函数,用来生成`Set`数据结构.

```
let s = new Set();
let arr = [1,2,3,3,3,3,3,3,4,5,6,7,8,9];
arr.map(x => s.add(x)); //add方法向Set结构加入成员
for(let i of s){
    console.log(i);     //1,2,3,4,5,6,7,8,9 不会加入重复的值
}
console.log([...s]);    //[1,2,3,4,5,6,7,8,9]
let s1 = new Set(arr);  //接受一个数组参数
console.log([...s1]);   //[1,2,3,4,5,6,7,8,9]
console.log(s1.size);   //9

//数组去重
[...new Set(arr)];
```



> 提示: `Set`不能加重复的`NaN`,`Set`不能加入重复的值,加入的值不会发生类型转换.

判断是否重复,也就是是否相等,使用`===`

```
let s = new Set();
s.add('5');
s.add(5);
console.log([...s]);    //["5", 5]
console.log(s.size);    //2
```



#### Set的方法和属性

属性

- `size` 成员总数
- `constructor` 构造函数

方法

- `add` 添加Set成员,返回Set结构本身
- `delete` 返回一个布尔值,表示是否删除成功
- `has` 判断是否是Set的成员,返回布尔值
- `clear` 清除所有成员,没有返回值

```
let s = new Set();
s.add('5');
s.add(5);

console.log([...s]);    //["5", 5] 转化为数组
console.log(s.size);    //2

let arr = Array.from(s);
console.log(arr);       //["5", 5] 转化为数组

//数组去重方法二

function dedupe(arr){
    return Array.from(new Set(arr));
}

console.log(dedupe([1,1,2,3,4,5,5]));   //[1, 2, 3, 4, 5]
```



#### 遍历

- `keys()`
- `vaules()`
- `entries()`
- `forEach()`

```
let set = new Set(['red', 'green', 'blue']);

for (let item of set.keys()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.values()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.entries()) {
  console.log(item);
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]
```



其实还可以使用`for...of`遍历`Set`.`Set`结构的实例的`forEach`方法,用于对每个成员执行某种操作,没有返回值.

### WeakSet

类似于`Set`略.

### Map

Map是比对象更灵活的一种数据结构,对象本身就就是由键值对组成的,但是属性只能是字符串,Map的键则可以用对象等更复杂方式来表示.

把对象`o`当做`m`的一个键

```
let m = new Map();
let o = {};

m.set(o,'o object');
console.log(m.get(o));  //o object
console.log(m.has(o));  //true

console.log(m.delete(o));   //true
console.log(m.has(o));  //false
```



接受一个数组作为参数,该数组的成员仍然是一个个数组,每个数组中都有两个元素表示键和值

```
let m = new Map([['name','ziyi2'],['age','23']]);
console.log(m.size);        //2
console.log(m.get('name')); //ziyi2
console.log(m.has('name')); //true
```



如果键相同,那么后面的就会把前面的覆盖掉

```
let m = new Map([['name','ziyi2'],['name','23']]);
console.log(m.size);        //1
console.log(m.get('name')); //23
console.log(m.has('name')); //true

m.set('b','ziyi2');
console.log(m.get('b'));    //ziyi2

m.set(['a'],"ziyi2");
console.log(m.get(['a']));  //undefined
```



> 提示: 最后一个是数组,其实是两个不同的地址,所以没办法获取值,就跟对象是一样的

#### 方法和属性

类似于`Set`.

#### 遍历方法

类似于`Set`.也可以使用`...`扩展运算符.

### WeakMap

略.

## Iterator和for...of循环

`ES5`中的表示集合的属性类型只有`Array`和`Object`,`ES6`中又新增了`Map`和`Set`.需要一种统一的接口机制,来处理所有不同的数据结构,遍历器（`Iterator`）就是这样一种机制.它是一种接口,为各种不同的数据结构提供统一的访问机制.任何数据结构只要部署`Iterator`接口,就可以完成遍历操作（即依次处理该数据结构的所有成员）.

`Iterator`的作用有三个：一是为各种数据结构,提供一个统一的、简便的访问接口；二是使得数据结构的成员能够按某种次序排列；三是`ES6`创造了一种新的遍历命令`for...of`循环,`Iterator`接口主要供`for...of`消费.

`Iterator`的遍历过程:

（1）创建一个指针对象,指向当前数据结构的起始位置.也就是说,遍历器对象本质上,就是一个指针对象. （2）第一次调用指针对象的`next`方法,可以将指针指向数据结构的第一个成员. （3）第二次调用指针对象的`next`方法,指针就指向数据结构的第二个成员. （4）不断调用指针对象的`next`方法,直到它指向数据结构的结束位置.

每一次调用`next`方法,都会返回数据结构的当前成员的信息.具体来说,就是返回一个包含`value`和`done`两个属性的对象.其中,`value`属性是当前成员的值,`done`属性是一个布尔值,表示遍历是否结束.

```
function createIterator(array){
    let nextIndex = 0;
    return {
        next () {
            return nextIndex < array.length ? {value: array[nextIndex ++], done: false} :
                                              {value: undefined, done: true};
        }
    };
}


let it = createIterator([1,2]);
console.log(it.next());     //Object { value=1,  done=false}
console.log(it.next());     //Object { value=2,  done=false}
console.log(it.next());     // Object { value=undefined, done=true}
```



上面的函数的作用就是返回一个遍历器对象,对数组执行这个函数,就会返回该数组的遍历器对象(指针对象)`it`.

在`ES6`中,有些数据结构原生具备`Iterator`接口（比如数组）,即不用任何处理,就可以被`for...of`循环遍历,有些就不行（比如对象）.原因在于,这些数据结构原生部署了`Symbol.iterator`属性（详见下文）,另外一些数据结构没有.凡是部署了`Symbol.iterator`属性的数据结构,就称为部署了遍历器接口.调用这个接口,就会返回一个遍历器对象.

### 数据结构的默认Iterator接口

`ES6`规定,默认的`Iterator`接口部署在数据结构的`Symbol.iterator`属性,或者说,一个数据结构只要具有`Symbol.iterator`属性,就可以认为是“可遍历的”（`iterable`）.**调用`Symbol.iterator`\**方法\**,就会得到当前数据结构默认的遍历器生成函数.**

在`ES6`中,有三类数据结构原生具备`Iterator`接口：`Array`、类数组对象、`Set`和`Map`结构.

```
let arr = ['a', 'b', 'c'];
let iter = arr[Symbol.iterator]();

console.log(iter.next()); // { value: 'a', done: false }
console.log(iter.next()); // { value: 'b', done: false }
console.log(iter.next()); // { value: 'c', done: false }
console.log(iter.next()); // { value: undefined, done: true }
```



上面代码中,变量`arr`是一个数组,原生就具有遍历器接口,部署在`arr`的`Symbol.iterator`属性上面.所以,调用这个属性,就得到遍历器对象.

上面提到,原生就部署`Iterator`接口的数据结构有三类,对于这三类数据结构,不用自己写遍历器生成函数,`for...of`循环会自动遍历它们.除此之外,其他数据结构（主要是对象）的`Iterator`接口,都需要自己在`Symbol.iterator`属性上面部署,这样才会被`for...of`循环遍历.

对象（`Object`）之所以没有默认部署`Iterator`接口,是因为对象的哪个属性先遍历,哪个属性后遍历是不确定的,需要开发者手动指定.

类似数组的对象调用数组的`Symbol.iterator`方法

```
let iterable = {
    0: 'a',
    1: 'b',
    2: 'c',
    length: 3,
    [Symbol.iterator]: Array.prototype[Symbol.iterator] //Symbol.iterator方法直接引用数组的Iterator接口
};


for(let item of iterable){
    console.log(item);  //a b c
}

console.log([...iterable]); //["a", "b", "c"]
```



需要注意的是普通对象部署数组的`Symbol.iterator`方法并没有效果.

```
let iterable = {
    a: 'a',
    b: 'b',
    c: 'c',
    length: 3,
    [Symbol.iterator]: Array.prototype[Symbol.iterator] //Symbol.iterator方法直接引用数组的Iterator接口
};


for(let item of iterable){
    console.log(item);  //undefined undefined undefined
}

console.log([...iterable]); //[undefined, undefined, undefined]

let  iter = iterable[Symbol.iterator]();
console.log(iter.next());   //Object { value=undefined,  done=false}
console.log(iter.next());   //Object { value=undefined,  done=false}
console.log(iter.next());   //Object { value=undefined,  done=false}
console.log(iter.next());   //Object { done=true,  value=undefined}
```



如果`Symbol.iterator`方法对应的不是遍历器生成函数（即会返回一个遍历器对象）,解释引擎将会报错

```
var obj = {};

obj[Symbol.iterator] = () => 1;

[...obj] //TypeError: (intermediate value).next is not a function
```



有了遍历器接口(`Iterator`接口),数据结构就可以用`for...of`循环遍历,也可以使用`while`循环遍历

```
let iterable = {
    0: 'a',
    1: 'b',
    2: 'c',
    length: 3,
    [Symbol.iterator]: Array.prototype[Symbol.iterator] //Symbol.iterator方法直接饮用数组的Iterator接口
};


for(let item of iterable){
    console.log(item);  //a b c
}

console.log([...iterable]); //["a", "b", "c"]

let  iter = iterable[Symbol.iterator]();

let result = iter.next();

while(!result.done){
    let x = result.value;
    console.log(x);   //a b c
    result = iter.next();
}
```



### 调用Iterator接口的场合

有一些场合会默认调用`Iterator`接口（即`Symbol.iterator`方法）,例如`for...of`循环

#### 解构赋值

对数组和`Set`结构进行解构赋值时,会默认调用`Symbol.iterator`方法

```
let set = new Set().add('a').add('b').add('c');
let [x,y,z] = set;
let [first,...rest] = set;
console.log(rest);  //["b", "c"]
```



#### `...`扩展运算符

该运算符也会调用默认的`iterator`接口

```
let str = 'hello';
console.log([...str]);  //["h", "e", "l", "l", "o"]

let arr = ['r','e'];
console.log([...str,...arr]);   //["h", "e", "l", "l", "o", "r", "e"]
```



> 提示: 只要某个数据接口部署了`iterator`接口,就可以对它使用扩展运算符,将其转为数组. `let arr = [...iterable];`

#### `yield*`

`yield*`后面跟的是一个可遍历的结构,会调用该结构的遍历器接口

```
let generator = function* (){
    yield 1;
    yield* [2,3,4];
    yield 5;
};

let iterator = generator();

console.log(iterator.next());   //Object { value=1,  done=false}
console.log(iterator.next());   //Object { value=2,  done=false}
console.log(iterator.next());   //Object { value=3,  done=false}
console.log(iterator.next());   //Object { value=4,  done=false}
console.log(iterator.next());   //Object { value=5,  done=false}
console.log(iterator.next());   //Object { value=undefined,  done=true}
```



#### 其他场合

数组的遍历会调用遍历器接口,所以任何接受数组作为参数的场合,其实都调用了遍历器接口

- `for...of`
- `Array.from`
- `Map,Set,WeakMap,WeakSet`
- `Promise.all`
- `Promise.race`

### 字符串的Iterator接口

字符串也是一个类数组对象,也原生具有`Iterator`接口

```
let str = 'hello';

let iterator = str[Symbol.iterator]();

console.log(iterator.next());   //Object { value=h,  done=false}
console.log(iterator.next());   //Object { value=e,  done=false}
console.log(iterator.next());   //Object { value=l,  done=false}
console.log(iterator.next());   //Object { value=l,  done=false}
console.log(iterator.next());   //Object { value=o,  done=false}
console.log(iterator.next());   //Object { value=undefined,  done=true}
```



> 提示:调用`Symbol.iterator`方法返回一个遍历器对象,该对象具有`next`方法,从而实现遍历.

### Iterator接口与Generator函数

`Symbol.iterator`方法的最简单实现

```
let iterable = {};

iterable[Symbol.iterator] = function* () {
    yield 1;
    yield 2;
    yield 3;
};

console.log([...iterable]); //[1,2,3]

//简洁写法


let obj = {
    *[Symbol.iterator]() {
        yield 'hello',
        yield 'world'
    }
};

for(let value of obj){
    console.log(value); //hello world
}

let objIter = obj[Symbol.iterator]();
console.log(objIter.next());    //Object { value="hello",  done=false}
console.log(objIter.next());    //Object { value="world",  done=false}
console.log(objIter.next());    //Object { done=true,  value=undefined} 
```



### 遍历器对象的方法

- next
- return
- throw

> 提示: 在`Generator`中讲解.

### `for...of`

#### Array

`for...of`循环内部调用的是数据接口的`Symbol.iterator`方法.

```
let arr = [1,2];
let iterator = arr[Symbol.iterator]();

for(let value of arr){  //原生调用iterator接口
    console.log(value); //1 2
}

for(let value of iterator){
    console.log(value); //1 2
}
```



以上两种写法相等,需要注意的是,`for...of`可以替代数组中的`forEach`方法.

同时,在`ES5`中的`for...in`方法可以获取键名,但是没办法获取键值,在ES6中使用`for...of`方法可以获取键值.

```
let arr = [1,2];

for(let value of arr){  //获取键值
    console.log(value); //1 2
}


for(let key in arr){    //获取键名(数组中的索引,对象中的属性名)
    console.log(key);   //0 1
}
```



同时,`for...of`在数组的遍历器接口中只返回具有数字索引的属性,这一点跟`for...in`循环也有差异

```
let arr = [1,2];

arr.name = 'arr';


for(let value of arr){  //只遍历数组的索引
    console.log(value); //1 2
}


for(let key in arr){    //当然是遍历所有属性名
    console.log(key);   //0 1 name
}
```



#### Set和Map

`Set`和`Map`和`Array`一样,原生具有`Iterator`接口,可以直接使用`for...of`循环.

```
let name = new Set(['ziyi2','ziyi3']);

for(let e of name){ 
    console.log(e);  //ziyi2 ziyi3
}

let person = new Map();

person.set('name','ziyi2');
person.set('age',23);

for(let [key,value] of person){
    console.log(key + ':' + value); //name:ziyi2 age:23
}
```



> 提示: Set结构遍历时,返回的是一个值,而Map结构遍历时,返回的是一个具有键值对的数组.

#### 计算生成的数据结构

以下三个方法调用后返回遍历器对象

- entries 遍历`[键名,键值]`组成的数组.`Map`结构的`Iterator`接口,默认就是调用`entries`方法.
- keys 遍历所有的键名
- values 遍历所有的键值

```
let arr = [1,2];
arr.name = 'arr';

for(let key of arr.keys()){ //arr.keys()返回的是数组的遍历器对象
    console.log(key);       //0 1
}

console.log(arr.keys().next()); //Object { value=0,  done=false}

for(let value of arr){
    console.log(value);     //1,2
}   

//该方法类似于以上遍历
for(let value of arr.values()){ //arr.values is not a function
    console.log(value);
}


for(let pair of arr.entries()){
    console.log(pair);  //[0, 1] [1, 2]
}
```



#### 类数组对象

`DOM NodeList`对象,`arguments`对象都是类数组对象.

```
let ps = document.querySelectorAll('p');


for(let p of ps){   //给所有的p元素添加class属性
    p.classList.add('test');    
}

for(let key in ps){
    console.log(key);   //0,1,2,3,4,item,length
}
```



当然并不是所有的类数组对象都具有`iterator`接口

```
let obj = {
    0: 'a',
    1: 'b',
    length:2
};


for(let key in obj){
    console.log(key);   //0,1,length
}

for(let value of obj){
    console.log(value); //obj is not iterable
}
```



此时有两种方法可以使它拥有`iterator`接口

```
let obj = {
    0: 'a',
    1: 'b',
    length:2,
    [Symbol.iterator]: Array.prototype[Symbol.iterator] //部署Symbol.iterator属性
};


for(let value of obj){
    console.log(value); //a b
}


let arr1 = {
    0: 'a',
    1: 'b',
    length: 2
};

for(let value of Array.from(arr1)){ //将类数组对象转化为数组
    console.log(value); //a b
}
```



#### 比较

`for...in`循环的缺点

- 遍历数组的键名时以字符串'0','1'.'2'作为键名
- 不仅遍历数字键名,还会遍历其他键,甚至包括原型链上的键(此时使用Object.keys()比较合适).
- 以任意顺序遍历键名

`for...of`优点

- 可以与`break`,`continue`,`return`配合使用
- 提供了遍历所有数据结构的统一操作接口

```
let obj = {
    0: 'a',
    1: 'b',
    length:2
};

for(let value in obj){
    console.log(value); //0
    break;              //中断遍历,也可以! 
}

for(let value of Array.from(obj)){
    console.log(value); //a

    if(value === 'a'){
        break;          //中断遍历
    }
}
```



## Generator

`Generator`函数是`ES6`提供的一种异步编程解决方案,语法行为与传统函数完全不同.`Generator`函数是一个状态机,封装了多个内部状态.执行`Generator`函数会返回一个遍历器对象,也就是说,`Generator`函数除了状态机,还是一个遍历器对象生成函数.返回的遍历器对象,可以依次遍历`Generator`函数内部的每一个状态. 形式上,`Generator`函数是一个普通函数,但是有两个特征.一是,`function`关键字与函数名之间有一个星号；二是,函数体内部使用`yield`语句,定义不同的内部状态（`yield`语句在英语里的意思就是“产出”）.

```
function* personGen(){
    yield 'ziyi2';      //此时有ziyi2,xx3和ending三个状态
    yield 'xx3';
    return 'ending';
}

let p = personGen();    //Generator函数返回的是一个遍历器对象

console.log(p.next());  //Object { value="ziyi2",  done=false}
console.log(p.next());  //Object { value="xx3",  done=false}
console.log(p.next());  //Object { value="ending",  done=true}
console.log(p.next());  //Object { value=undefined,  done=true}
```



调用`Generator`函数后,该函数并不执行,返回的也不是函数运行结果,而是一个指向内部状态的指针对象,也就是上一节介绍的遍历器对象`Iterator Object`.必须调用遍历器对象的`next`方法,使得指针移向下一个状态,每次调用`next`方法,内部指针就从函数头部或上一次停下来的地方开始执行,直到遇到下一个`yield`语句（或`return`语句）为止.换言之,`Generator`函数是分段执行的,`yield`语句是暂停执行的标记,而`next`方法可以恢复执行.

第一次调用,`Generator`函数开始执行,直到遇到第一个`yield`语句为止.`next`方法返回一个对象,它的`value`属性就是当前`yield`语句的值`ziyi2`,`done`属性的值`false`,表示遍历还没有结束.

第二次调用,`Generator`函数从上次`yield`语句停下的地方,一直执行到下一个`yield`语句.`next`方法返回的对象的`value`属性就是当前`yield`语句的值`xx3`,`done`属性的值`false`,表示遍历还没有结束.

一直执行到`return`语句（如果没有`return`语句,就执行到函数结束）.`next`方法返回的对象的`value`属性,就是紧跟在`return`语句后面的表达式的值（如果没有`return`语句,则`value`属性的值为`undefined`）,`done`属性的值`true`,表示遍历已经结束.

`ES6`没有规定,`function`关键字与函数名之间的星号,写在哪个位置.下面写法都是可以的,一般采用第二种

```
function * f(){};
function* f(){};
function *f(){};
function*f(){};
```



### yield

由于`Generator`函数返回的遍历器对象,只有调用`next`方法才会遍历下一个内部状态,所以其实提供了一种可以暂停执行的函数.`yield`语句就是暂停标志.

- 遇到`yield`语句,就暂停执行后面的操作(比如有一个加法操作,没有遇到该`yield`之前是不会执行该语句的,等于手动的'惰性求值'),并将紧跟在`yield`后面的那个表达式的值,作为返回的对象的`value`属性值.
- 下一次调用`next`方法时,再继续往下执行,直到遇到下一个`yield`语句.
- 如果没有再遇到新的`yield`语句,就一直运行到函数结束,直到`return`语句为止,并将`return`语句后面的表达式的值,作为返回的对象的`value`属性值.
- 如果该函数没有`return`语句,则返回的对象的`value`属性值为`undefined`.

`yield`语句与`return`语句既有相似之处,也有区别.相似之处在于,都能返回紧跟在语句后面的那个表达式的值.区别在于每次遇到`yield`,函数暂停执行,下一次再从该位置继续向后执行,而`return`语句不具备位置记忆的功能.一个函数里面,只能执行一次（或者说一个）`return`语句,但是可以执行多次（或者说多个）`yield`语句.正常函数只能返回一个值,因为只能执行一次`return`；`Generator`函数可以返回一系列的值,因为可以有任意多个`yield`.从另一个角度看,也可以说`Generator`生成了一系列的值,这也就是它的名称的来历（在英语中,`generator`这个词是“生成器”的意思）.

`Generator`函数可以不用`yield`语句,这时就变成了一个单纯的暂缓执行函数

```
function * fGen(){
    console.log('此时执行');
}

let f = fGen();     //此时并没有输出
f.next();           //此时执行
```



`yield`语句不能用在普通函数里,否则会报错

```
function * fGen(){
    (function(){        //不能把yield放在普通函数里
        yield 'ziyi2';
        yield 'xx3';
        return 'ending'
    })();
}


let f = fGen(); //error 报错
```



### Iterator接口关系

对象的`Symbol.iterator`方法相当于一个遍历器生成函数,调用该函数会返回该对象的遍历器对象. 由于`Generator`函数就是遍历器生成函数,因此把`Generator`赋值给对象的`Symbol.iterator`属性,从而使得该对象具有`Iterator`接口.

```
let myIterable = {};

myIterable[Symbol.iterator] = function* (){
    yield 1;
    yield 2;
    yield 3;
};

console.log([...myIterable]);   //[1, 2, 3]
```



当然`Generator`函数执行后返回的遍历器对象本身也具有`Symbol.iterator`属性,执行后和自身相同

```
function* gen(){
    yield 1;
    yield 2;
};

let g = gen();

let s = g[Symbol.iterator]();

let bool = s ===  g;
console.log(bool);  //true

console.log(g.next());  //Object { value=1,  done=false}
console.log(s.next());  //Object { value=2,  done=false}
```



### next方法的参数

### for...of循环

一旦`next`方法的返回对象的`done`属性为`true`,`for...of`循环就会中止,且不包含该返回的对象,所以`return`语句返回的值就不会被遍历

```
function *f(){
    yield 1;
    yield 2;
    yield 3;
    yield 4;
    return 6;
}

for(let value  of f()){
    console.log(value); //1 2 3 4
}
```



> 提示: 使用`for...of`语句时不需要再使用`next`方法.

`for...of`,扩展运算符`...`,解构赋值和`Array.from`方法内部调用的,都是遍历器接口,所以可以将Generator函数返回的Iterator对象作为参数.

```
function *f(){
    yield 1;
    yield 2;
    yield 3;
    return 4;
    yield 5;

}


console.log([...f()]);          //[1, 2, 3]
console.log(Array.from(f()));   //[1, 2, 3]

let [...arr] = f();
console.log(arr);               //[1, 2, 3]

let [x,y,z] = f();
console.log(x);                 //1
console.log(y);                 //2
console.log(z);                 //3


for(let value of f()){
    console.log(value);         //1 2 3
}   
```



通过Generator函数为原生的对象加上`Iterator`接口

```
function* objEntries(obj){
    let keys = Reflect.ownKeys(obj);    //返回所有的属性,不管属性名是`Symbol`还是字符串,不管是否可枚举.

    for(let key of keys){
        yield [key,obj[key]];
    }
}


let person = {one:'ziyi2',two:'ziyi3',three:'ziyi4'};

for(let [key,value] of objEntries(person)){
    console.log(`${key}:${value}`);
}

//one:ziyi2
//two:ziyi3
//three:ziyi4
```



以上原生的`person`对象是不具备`Iterator`接口的,通过`Generator`函数为它加上遍历器接口,就可以使用`for...of`遍历了.当然加上遍历器的另外一个方法是将`Generator`函数加到对象`Symbol.iterator`属性上面.

```
function* objEntries(){
    //let keys = Reflect.ownKeys(this); //返回所有的属性,不管属性名是`Symbol`还是字符串,不管是否可枚举.

    let keys = Object.keys(this);       //返回对象自身的所有可枚举的属性的键名

    for(let key of keys){
        yield [key,this[key]];
    }
}


let person = {one:'ziyi2',two:'ziyi3',three:'ziyi4'};

person[Symbol.iterator] = objEntries;

for(let [key,value] of person){
    console.log(`${key}:${value}`);
}

//one:ziyi2
//two:ziyi3
//three:ziyi4
```



#### Generator.prototype.throw

`Generator`函数返回的遍历器对象,都有一个`throw`方法,可以在函数体外抛出错误,然后在`Generator`函数体内捕获.

```
var g = function* () {
  try {
    yield;
  } catch (e) {
    console.log('内部捕获', e);
  }
};

var i = g();
console.log(i.next());  //Object {value: undefined, done: false}

try {
  i.throw('a');
  i.throw('b');
} catch (e) {
  console.log('外部捕获', e);
}
// 内部捕获 a
// 外部捕获 b
```



上面代码中,遍历器对象`i`连续抛出两个错误.第一个错误被`Generator`函数体内的`catch`语句捕获.`i`第二次抛出错误,由于`Generator`函数内部的`catch`语句已经执行过了,不会再捕捉到这个错误了,所以这个错误就被抛出了`Generator`函数体,被函数体外的`catch`语句捕获.

`throw`方法可以接受一个参数,该参数会被`catch`语句接收,建议抛出`Error`对象的实例

```
var g = function* () {
  try {
    yield;
  } catch (e) {
    console.log( e);    //Error: 出错了!
  }
};


let i = g();
i.next();
i.throw(new Error('出错了!'));
```



> 提示: 不要混淆遍历器对象的`throw`方法和全局的`throw`命令.上面代码的错误,是用遍历器对象的`throw`方法抛出的,而不是用`throw`命令抛出的.后者只能被函数体外的`catch`语句捕获.

如果`Generator`函数内部没有部署`try...catch`代码,那么遍历器对象抛出的异常将会被外部的`catch`代码块捕获

```
var g = function* () {
  try {
    yield;
  } catch (e) {
    console.log('内部捕获',e);  
  }
};

let i = g();
i.next();

try {
    throw new Error('a');   
    throw new Error('b');
} catch(e){
    console.log('外部捕获',e);
}

//外部捕获 Error: a
```



函数体外的`catch`语句块捕获了抛出`a`错误以后,就不会在继续`try`代码块里面剩余的语句了.

```
let g = function* () {
    while(true) {
        yield;
        console.log('内部捕获',e);
    }
};


let i = g();
console.log(i.next());  //Object { done=false,  value=undefined}

try {
    i.throw('a');
    i.throw('b');
} catch (e) {
    console.log('外部捕获',e);
}

//外部捕获 a
```



> 提示: 如果外部和函数内部都没有部署`try...catch`代码块,那么程序将会报错,中断执行.

```
let gen = function* () {
    try {
        yield console.log('a');
    } catch (e) {
        console.log('error');
    }

    yield console.log('b');
    yield console.log('c');
};


let g = gen();

g.next();       //a
g.throw();      //error 
g.next();       //b
g.next();       //c
console.log(g.next());  //Object { done=true,  value=undefined}
g.throw();      //uncaught exception: undefined
```



```
let gen = function* () {
    try {
        yield console.log('a');
    } catch (e) {
        console.log('error');
    }

    yield console.log('b');
    yield console.log('c');
};


let g = gen();

g.next();       //a
g.next();       //b

try {
    throw new Error();
} catch(e) {
    g.next();   //c
}
```



`Generator`函数体外抛出的错误,可以在函数体内捕获,反过来,`Generator`函数体内抛出的错误,也可以被函数体外的`catch`捕获

```
let gen = function* () {
    let x = yield 3;
    let y = x.toUpperCase();
    yield y;
};

let it = gen();

console.log(it.next());   //Object { value=3,  done=false}

try {
    it.next(43);
} catch (err) {
    console.log(err);     //x.toUpperCase is not a function
}
```



`yield`句本身没有返回值,或者说总是返回`undefined`.`next`方法可以带一个参数,该参数就会被当作上一个`yield`语句的返回值.

```
let gen = function* () {
    let x = yield 3;        //本身没有返回值,返回的总是undefined
    let y = x.toUpperCase();
    yield y;
};

let it = gen();

console.log(it.next());     //Object { value=3,  done=false}
console.log(it.next());     //TypeError: x is undefined 
```



一旦`Generator`执行过程中抛出错误,且没有被内部捕获,就不会再执行下去了,如果还调用`next`,将返回`{value:undeinfed,done:true}`

```
let gen = function* () {
    yield 3;
    console.log('throw an error');
    throw new error('error:next is done');
    yield 2;
    yield 3;
}

let g = gen();

try {
    console.log(g.next());   //Object { value=3,  done=false}
} catch(e) {
    console.log('first next error' );
}

try {
    console.log(g.next());
} catch(e) {
    console.log('second next error' );  //throw an error second next error
}

try {
    console.log(g.next());  // Object { done=true,  value=undefined}
} catch(e) {
    console.log('third next error' );
}
```



#### Generator.prototype.return

```
return`方法用来终结遍历状态,就好像在`Generator`函数中遇到了`return
let gen = function* () {
    yield 1;
    yield 2;
    yield 3;
}

let g = gen();

console.log(g.next());      // Object { value=1,  done=false}
console.log(g.return('4')); // Object { value="4",  done=true}
console.log(g.next());      // Object { done=true,  value=undefined}
```



> 提示: 如果`return`方法不传入参数,那么返回的是`undefined`

如果`Generator`函数内部有`try...finally`代码块

```
let gen = function* () {
    yield 1;
    try {
        yield 2;
        yield 3;
    } finally {
        yield 4;
        yield 5;
    }
    yield 6;
}

let g = gen();

console.log(g.next());      //Object { value=1,  done=false}
console.log(g.next());      //Object { value=2,  done=false}
console.log(g.return('7')); //Object { value="7",  done=true}
console.log(g.next());      //Object { value=4,  done=false}
console.log(g.next());      //Object { value=5,  done=false}
```



> 提示:调用`return`后,先执行`finally`中的代码块,执行完后再执行`return`方法

#### `yield*`

在`Generator`函数内部要调用`Generator`函数是不行的,此时可以用`yield*`来代替

```
function* f() {
    yield 1;
    yield 2;
    yield* g();
}

function* g() {
    yield 3;
    yield 4;
}


for(let value of f()){
    console.log(value); //1 2 3 4
}
```



如果`yield`后面是一个遍历器对象,那么需要`yield`命令后面加上`*`,表明它返回的是一个遍历器对象,否则返回的不是遍历值,而是该遍历器对象

```
function* f() {
    yield 1;
    yield 2;
    yield* g();         //返回遍历值            
    yield g();          //这里返回的是遍历器对象
}

function* g() {
    yield 3;
    yield 4;
}


for(let value of f()){
    console.log(value); //1 2 3 4  Generator {}
}
```



遍历递归效果

```
let f = (function* () {
    yield 'hello';
    yield 'bye';
}());


let g = (function* () {
    yield 'hello forward';
    yield* f;
    yield 'bye end';
}());

for(let value of g) {
    console.log(value); //'hello forward'  'hello' 'bye' 'bye end'
}
```



以上代码等同于

```
var f = (function* () {
    yield 'hello';
    yield 'bye';
}());

let g_copy = (function* () {
    yield 'hello forward';
    for(let value of f) {
        yield value;
    }
    yield 'bye end';

}());

for(let value of g_copy) {
    console.log(value); //'hello forward'  'hello' 'bye' 'bye end'
}
```



由于数组原生支持`Iterator`接口,所以可以被`yield*`遍历

```
let g = (function* () {
    yield 'hello forward';
    yield* ['hello','bye'];
    yield 'bye end';

}());

for(let value of g) {
    console.log(value); //'hello forward'  'hello' 'bye' 'bye end'
}
```



> 提示: 任何具有`Iterator`接口的数据接口,都可以被`yield*`遍历

如果`Generator`函数中调用的`Generator`函数具有`return`语句,那么就可以在这个被调用的`Generator`函数中返回数据给调用`Generator`的`Generator`函数

```
function* f() {
    yield 2;
    yield 3;
    return 'return value';
}

function* g() {
    yield 1;
    let r = yield* f();     //r是f()中的返回值 注意与yield中的返回值的区别
    console.log('r:' + r); 
    yield 4;
}

let g1 = g();


console.log(g1.next()); // Object { value=1,  done=false}
console.log(g1.next()); // Object { value=2,  done=false}
console.log(g1.next()); // Object { value=3,  done=false}
console.log(g1.next()); // r:return value  Object { value=4,  done=false}
console.log(g1.next()); // Object { done=true,  value=undefined}
```



> 提示: 注意与`yield`中的返回值的区别,`yield`句本身没有返回值,或者说总是返回`undefined`.`next`方法可以带一个参数,该参数就会被当作上一个`yield`语句的返回值.

```
function* iterTree(tree) {
    if(Array.isArray(tree)) {
        // tree.forEach(function(item,index,arr){
        //  yield* iterTree(item);  //不能放在普通函数内!!!!
        // })
        
        for(let value of tree){
            yield* iterTree(value);
        }
        

    } else {
        yield tree;
    }
}


const tree = [[1,2,3],4,5,[6,[7,8]]];

for(let value of iterTree(tree)) {
    console.log(value); //1 2 3 4 5 6 7 8
}
```



#### Generator作为对象的属性

```
let obj = {

    f: function* () {
        yield 1;
        yield 2;
        yield 3;
    },

    *g() {              //简写方式 *表示这个属性是Generator函数
        yield 1;
        yield 2;
    }
}


for(let value of obj.f()){
    console.log(value); //1 2 3
}

for(let value of obj.g()){
    console.log(value); //1 2
}
```



`Generator`函数总是返回一个遍历器,`ES6`规定这个遍历器是`Generator`函数的实例,也继承了`Generator`函数的`prototype`对象上的方法

```
function* g() {}
g.prototype.say = () => 'hi';
let obj = g();  //返回的遍历器obj是g的实例
console.log(obj.say()); //hi
```



> 提示: 不能把`Generator`函数当做普通的构造函数,并且不能和new命令结合

#### 状态机

```
//ES5
let ticking = true;

let clock = () => {
    if(ticking) {
        console.log('Tick!');
    } else {
        console.log('Tock!');
    }

    ticking = !ticking;
}

clock();    //'Tick!'
clock();    //'Tock!'
clock();    //'Tick!'

//ES6
let status = function*() {
    while(true) {
        console.log('Tick!');
        yield;
        console.log('Tock!');
        yield;
    }
}


let x = status();

x.next();   //'Tick!'
x.next();   //'Tock!'
x.next();   //'Tick!'
```



#### 协程

协程（coroutine）是一种程序运行的方式,可以理解成“协作的线程”或“协作的函数”.协程既可以用单线程实现,也可以用多线程实现.前者是一种特殊的子例程,后者是一种特殊的线程.

（1）协程与子例程的差异

传统的“子例程”（subroutine）采用堆栈式“后进先出”的执行方式,只有当调用的子函数完全执行完毕,才会结束执行父函数.协程与其不同,多个线程（单线程情况下,即多个函数）可以并行执行,但是只有一个线程（或函数）处于正在运行的状态,其他线程（或函数）都处于暂停态（suspended）,线程（或函数）之间可以交换执行权.也就是说,一个线程（或函数）执行到一半,可以暂停执行,将执行权交给另一个线程（或函数）,等到稍后收回执行权的时候,再恢复执行.这种可以并行执行、交换执行权的线程（或函数）,就称为协程.

从实现上看,在内存中,子例程只使用一个栈（stack）,而协程是同时存在多个栈,但只有一个栈是在运行状态,也就是说,协程是以多占用内存为代价,实现多任务的并行.

（2）协程与普通线程的差异

不难看出,协程适合用于多任务运行的环境.在这个意义上,它与普通的线程很相似,都有自己的执行上下文、可以分享全局变量.它们的不同之处在于,同一时间可以有多个线程处于运行状态,但是运行的协程只能有一个,其他协程都处于暂停状态.此外,普通的线程是抢先式的,到底哪个线程优先得到资源,必须由运行环境决定,但是协程是合作式的,执行权由协程自己分配.

由于ECMAScript是单线程语言,只能保持一个调用栈.引入协程以后,每个任务可以保持自己的调用栈.这样做的最大好处,就是抛出错误的时候,可以找到原始的调用栈.不至于像异步操作的回调函数那样,一旦出错,原始的调用栈早就结束.

Generator函数是ECMAScript 6对协程的实现,但属于不完全实现.Generator函数被称为“半协程”（semi-coroutine）,意思是只有Generator函数的调用者,才能将程序的执行权还给Generator函数.如果是完全执行的协程,任何函数都可以让暂停的协程继续执行.

如果将Generator函数当作协程,完全可以将多个需要互相协作的任务写成Generator函数,它们之间使用yield语句交换控制权.

#### 应用

- 异步操作的同步化表达

```
function* ajax() {
    let result = yield request('/url'); //result就是next传入的参数response
    let resp = JSON.parse(result);
    console.log(resp.value);
}


function request(url) {
    makeAjax(url,(response => {
        it.next(response);
    }))
}

let it = ajax();
it.next();
```



使用`yield`语句可以手动逐行读取文件

```
function* numbers() {
  let file = new FileReader("numbers.txt");
  try {
    while(!file.eof) {
      yield parseInt(file.readLine(), 10);  //转换为十进制
    }
  } finally {
    file.close();
  }
}
```



- 解决回调金字塔

回调金字塔

```
step1(function (value1) {
  step2(value1, function(value2) {
    step3(value2, function(value3) {
      step4(value3, function(value4) {
        // Do something with value4
      });
    });
  });
});
```



采用`Promise`写法

```
Q.fcall(step1)
  .then(step2)
  .then(step3)
  .then(step4)
  .then(function (value4) {
    // Do something with value4
  }, function (error) {
    // Handle any error from step1 through step4
  })
  .done();
```



`Generator`写法

```
function* longRunningTask() {
  try {
    var value1 = yield step1();
    var value2 = yield step2(value1);
    var value3 = yield step3(value2);
    var value4 = yield step4(value3);
    // Do something with value4
  } catch (e) {
    // Handle any error from step1 through step4
  }
}
```



然后,使用一个函数,按次序自动执行所有步骤

```
scheduler(longRunningTask());

function scheduler(task) {
  setTimeout(function() {
    var taskObj = task.next(task.value);
    // 如果Generator函数未结束,就继续调用
    if (!taskObj.done) {
      task.value = taskObj.value
      scheduler(task);
    }
  }, 0);
}
```



注意,`yield`语句是同步运行,不是异步运行（否则就失去了取代回调函数的设计目的了）.实际操作中,一般让`yield`语句返回`Promise`对象.

```
var Q = require('q');

function delay(milliseconds) {
  var deferred = Q.defer();
  setTimeout(deferred.resolve, milliseconds);
  return deferred.promise;
}

function* f(){
  yield delay(100);
};
```



- 作为数据结构

```
function *doStuff() {
  yield fs.readFile.bind(null, 'hello.txt');
  yield fs.readFile.bind(null, 'world.txt');
  yield fs.readFile.bind(null, 'and-such.txt');
}

for (task of doStuff()) {
  // task是一个函数,可以像回调函数那样使用它
}
```



## Promise

### Promise概述

- 对象的状态不受外界影响 `Promise`对象代表一个异步操作,有三种状态,`Pending(待决)`,`Resolved(成功)`和 `Rejected(失败)`.只有异步操作的结果,可以决定当前是哪一种状态,任何其他操作都无法改变这个状态,这也是Promise这个名字的由来,它的英语意思就是“承诺”,表示其他手段无法改变.
- 一旦状态改变,就不会再变,任何时候都可以得到这个结果

`Promise`对象的状态改变,只有两种可能：从`Pending`变为`Resolved`和从`Pending`变为`Rejected`,只要这两种情况发生,状态就凝固了,不会再变了,会一直保持这个结果

`Promise`也有一些缺点.首先,无法取消`Promise`,一旦新建它就会立即执行,无法中途取消.其次,如果不设置回调函数,`Promise`内部抛出的错误,不会反应到外部.第三,当处于`Pending`状态时,无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）. 如果某些事件不断地反复发生,一般来说,使用`stream`模式是比部署`Promise`更好的选择

### 用法

```
let promise = new Promise((resolve,reject) => {

    //异步操作
    
    // f(function(){
    //  if(err){
    //      reject(error);
    //  } 
    //  resolve(value);
    // })
    
    //操作成功
    resolve(value); //status:Pending -> Resolved

    //操作失败
    reject(error);  //status:Pending -> Rejected    

});

//Promise实例生成以后,可以用then方法分别指定Resolved状态和Reject状态的回调函数.
promise.then(value => {
    //success
},error => {
    //failed
})
```



> 提示: 在`Promise`对象中的`resolve`和`reject`会将参数(这里是`value`和`error`)传递出去,而`then`中的成功和失败方法都会接受相应的参数

简单的例子

```
function timeOut(ms) {
    return new Promise((resolve,reject) => {
        setTimeout(resolve,ms,'done');
    });
}

timeOut(5000).then((value) => {
    console.log(value);     //done
});
```



新建Promise实例的时候会立即执行

```
let promise = new Promise((resolve,reject) => {
    console.log('new promise running!');
    resolve('resolve running!');
});

promise.then((value) => {
    console.log(value);
})

console.log('global running!');



//new promise running!
//global running!
//resolve running!
```



ajax实例

```
var getJSON = function(url) {
  var promise = new Promise(function(resolve, reject){
    var client = new XMLHttpRequest();
    client.open("GET", url);
    client.onreadystatechange = handler;
    client.responseType = "json";
    client.setRequestHeader("Accept", "application/json");
    client.send();

    function handler() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
  });

  return promise;
};

getJSON("/posts.json").then(function(json) {
  console.log('Contents: ' + json);
}, function(error) {
  console.error('出错了', error);
});
```



```
resolve`函数的参数除了正常的值以外,还可能是另一个`Promise实例
```

`p2`的`resolve`方法将`p1`作为参数,即一个异步的操作结果返回另一个异步操作, **这时`p1`的状态就会传递给`p2`,`p1`的状态决定了`p2`的状态**,如果`p1`状态是`Pending`,那么`p2`的回调函数就会等待`p1`的状态改变,如果`p1`的状态已经是`Resolved`或`Rejected`,那么`p2`的回调函数就会立刻执行

此时p2会等待p1的状态执行完毕在执行

```
let p1 = new Promise((resolve,reject) => {
    setTimeout(() => resolve('p1 resolve!'),1000);
});

let p2 = new Promise((resolve,reject) => {
    setTimeout(() => resolve(p1), 500);
});

p2.then(value => console.log(value));   //p1 resolve
```



此时p2立即执行,因为p1的状态先执行完毕

```
let p1 = new Promise((resolve,reject) => {
    setTimeout(() => resolve('p1 resolve!'),500);
});

let p2 = new Promise((resolve,reject) => {
    setTimeout(() => resolve(p1), 1000);
});

p2.then(value => console.log(value));   //p1 resolve
```



`p1`的状态是`Rejected`,所以`p2`执行后也是触发`Rejected`状态对应的回调函数

```
let p1 = new Promise((resolve,reject) => {
    setTimeout(() => reject('p1 reject!'),500);
});

let p2 = new Promise((resolve,reject) => {
    setTimeout(() => resolve(p1), 1000);    //resolve接受一个Promise实例作为参数
});

p2.then(value => console.log('resolve ' + value),   
        error => console.log('reject ' + error));   //reject p1 reject
```



### Promise.prototype.then

为`Promise`实例添加状态改变时对应的回调函数,第一个参数`Resolve`状态对应的回调函数,第二个参数是`Rejected`状态对应的回调函数,也可以进行链式调用,比如`ajax`请求时,连续需要发送好几个`ajax`,但是后一个`ajax`需要在前一个`ajax`请求成功时才触发,此时非常适用!

采用链式`then`,前一个异步操作在执行回调函数时,如果返回的还是一个`Promise`对象(另外一个异步操作),那么后一个回调函数就会等待该`Promise`对象的状态的改变从而触发相应的回调函数.

```
let p = new Promise((resolve,reject) => {
    resolve('resolve data!');
});

p.then((data) => {
    console.log(data);  //resolve data
    return p;
}).then((data) => {
    console.log(data);  //resolve data
    return p;
}).then((data) => {
    console.log(data);  //resolve data;
});
```



> 提示: 通常两个异步操作如果同时执行,它们的回调函数的触发时间的先后是不一定的,要看异步操作的执行状态时间,但是采用链式的形式可以指定一组按照顺序执行的异步操作

### Promise.prototype.catch

如果异步操作抛出错误,状态就会变为`Rejected`,就会调用`catch`方法指定的回调函数,处理这个错误

```
let p = new Promise((resolve,reject) => {
    throw new Error('promise Error!');
});

//方法一
p.then((val) => console.log('resolve:'))
 .catch((err) => console.log('err:' + err));
//err:Error: promise Error!

//方法二,等同于方法一
p.then((val) => console.log('resolve:'))
 .then(null,(err) => console.log('err:' + err));
//err:Error: promise Error!
```



`Promise`一旦状态发生变化时不能被改变的

```
let promise = new Promise((resolve,reject) => {
    resolve('resolve data!');
    reject('reject data!');         //等于没有设置
    throw new Error('error data!'); //等于没有抛出
});


promise.then((value) => console.log(value),
             (error) => console.log(error))
       .catch(err => console.log(err));

//resolve data!
```



尽管返回的仍然是`promise`,只要任何一个出错,都会被最后的`catch`捕获

```
let promise = new Promise((resolve,reject) => {
    throw new Error('error data!');
});


promise.then(() => promise)
       .then(() => promise)
       .catch(err => console.log(err)); //Error: error data!
```



一般来说,不要在`then`方法里面定义`Rejected`状态的回调函数（即`then`的第二个参数）,总是使用`catch`方法

```
let promise = new Promise((resolve,reject) => {
    reject('reject data!');
});

//bad
promise.then((data) => console.log(data),
             (err)  => console.log(err));   //reject data!

//good 
promise.then((data) => console.log(data))
       .catch((err) => console.log(err));   //reject data!
```



> 提示:使用`catch`不仅能够捕获状态`Rejected`,还能捕获异常错误.因此建议使用`catch`而不是使用`then`方法的第二个参数

`Nodejs`有一个`unhandledRejection`事件,专门监听未捕获的`reject`错误.

```
process.on('unhandledRejection', function (err, p) {
  console.error(err.stack)
});
```



> 提示: `p`是报错的`Promise`实例

`catch`方法返回的还是一个`Promise`对象

```
let promise = new Promise((resolve,reject) => {
    reject(x+4);
});


promise.then((value) => console.log('resolve1:' + value))
       .catch((err)  => console.log('err1 ' + err))     //err1 ReferenceError: x is not defined
       .then((value) => console.log('resolve2'))        //resolve2
       // .catch((err)  => console.log('err2 ' + x))
       // .catch((err)  => console.log('err3 ' + x));
```



如果没有错误,则会跳过`catch`方法

```
let promise = new Promise((resolve,reject) => {
    resolve('resolve');
});


promise      
       .catch((err)  => console.log('err2 ' + err)) 
       .then((value) => console.log('resolve1:' + value));

//resolve1: resolve
```



`catch`方法还能再抛出错误

```
let promise = new Promise((resolve,reject) => {
    reject(x+4);
});


promise.then((value) => console.log('resolve1:' + value))
       .catch((err)  => console.log('err1 ' + x))       
       .catch((err)  => console.log('err2 ' + err));    //err2 ReferenceError: x is not defined
```



> 提示: 第一个`catch`有错误后在第二个`catch`中抛出

### Promise.all

接收一个数组作为参数或者该参数具有`Iterator`接口,每一个成员都必须是`Promise`实例.

以下实例中只有`p,p1,p2`的状态都变成`Resolved`,`ps`的状态才会变成`Resolved`,改参数的所有`Promise`实例的返回值都将以数组的形式传递给`ps`的回调函数

```
let p = new Promise((resolve,reject) => {
    resolve('resolve:p');
});

let p1 = new Promise((resolve,reject) => {
    resolve('resolve:p1');
});

let p2 = new Promise((resolve,reject) => {
    resolve('resolve:p3');
});

let ps = Promise.all([p,p1,p2]);
ps.then((values) => console.log(values));   //["resolve:p", "resolve:p1", "resolve:p3"]
```



只要有一个状态是`Rejected`,`ps`的状态就变成`Rejected`,此时第一个被`Rejecte`的实例的返回值会传递给`ps`的回调函数

```
let p = new Promise((resolve,reject) => {
    resolve('resolve:p');
});


let p1 = new Promise((resolve,reject) => {
    reject('reject:p1');
});



let p2 = new Promise((resolve,reject) => {
    resolve('resolve:p3');
});



let ps = Promise.all([p,p1,p2]);
ps.then((values) => console.log(values))    
  .catch((err) => console.log(err));    //reject:p1
```



### Promise.race

race,比赛的意思,所以和all正好相反的是,只要有一个实例率先改变状态,`ps`的状态就跟着改变了,那个率先改变的`Promise`实例的返回值,就传递给`ps`的回调函数,这种方法可以用作超时处理

```
let p = new Promise((resolve,reject) => {
    setTimeout(() => {
        resolve('task 1')
    },1000);
});


let p1 = new Promise((resolve,reject) => {
    setTimeout(() => {
        reject('time out 2')
    },2000);
});

let ps = Promise.race([p,p1]);
ps.then((value) => console.log('resolve:' + value)) //resolve:task 1
  .catch((err) => console.log('err:' + err));       //err:time out 2
```



> 提示:如果任务一是一个`ajax`请求,那么我们就可以设定多少时间没有处理就拒绝处理,返回超时提醒

### Promise.resolve

将现有对象转换为`Promise`对象

```
let p = Promise.resolve('resolve value');

//等同于
//let p = new Promise((resolve) => resolve('resolve value'));

p.then((value) => console.log(value));  //resolve value



let p1 = Promise.resolve($.ajax('url'));

//将Jquery生成的deferred对象转为一个新的Promise对象
```



`$.ajax()`操作完成后,如果使用的是低于1.5.0版本的`jQuery`,返回的是`XHR`对象,你没法进行链式操作；如果高于1.5.0版本,返回的是`deferred`对象,可以进行链式操作,具体详见[jQuery的deferred对象详解](http://www.ruanyifeng.com/blog/2011/08/a_detailed_explanation_of_jquery_deferred_object.html)

- 参数是Promise实例
- 参数是thenable对象

```
let obj = {
    then(resolve,reject) {
        resolve('resolve value');
    }
};
let p = Promise.resolve(obj);
p.then((value) => console.log(value));  //resolve value
```



> 提示: `Promise.resolve`方法会将这个对象转为`Promise`对象,然后就立即执行`obj`对象的`then`方法.该方法执行后,对象`p`的状态就变为`resolved`

- 参数不是具有then方法的对象,或根本就不是对象

```
let p = Promise.resolve('resolve');
p.then((value) => console.log(value));  //resolve
```



- 不带任何参数

```
setTimeout(() => console.log('setTimeout'),0);

Promise.resolve().then(() => console.log('promise'));

console.log('global');

//global        立即执行
//promise       本轮'事件循环'结束时执行
//setTimeout    下一轮'事件循环'开始时执行
```



> 提示: 关于事件循环可以查看[JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)

### Promise.reject

与`resove`方法对应

```
let p = Promise.reject('reject!');
p.catch((err) => console.log(err)); //reject
```



### done

该方法总是处于回调链的尾端,保证任何可能的错误最终都可以捕捉到

```
asyncFunc()
  .then(f1)
  .catch(r1)
  .then(f2)
  .done();

Promise.prototype.done = function (onFulfilled, onRejected) {
  this.then(onFulfilled, onRejected)
    .catch(function (reason) {
      // 抛出一个全局错误
      setTimeout(() => { throw reason }, 0);
    });
};
```



### finally

`finally`方法用于指定不管`Promise`对象最后状态如何,都会执行的操作

```
server.listen(0)
  .then(function () {
    // run test
  })
  .finally(server.stop);

Promise.prototype.finally = function (callback) {
  let P = this.constructor;
  return this.then(
    value  => P.resolve(callback()).then(() => value),
    reason => P.resolve(callback()).then(() => { throw reason })
  );
};
```



### 应用

#### 加载图片

```
const preloadImage = function (path) {
  return new Promise(function (resolve, reject) {
    var image = new Image();
    image.onload  = resolve;
    image.onerror = reject;
    image.src = path;
  });
};
```



#### `Generator`函数与`Promise`的结合

```
function getFoo() {
    return new Promise((resolve,reject) => {
        resolve('foo');
    })
}


let g = function* () {
    try {
        let foo = yield getFoo(); //返回Promise对象
        console.log(foo);
    } catch(e) {
        console.log(e);
    }
};


function run(generator) {
    let it = generator();

    function go(result) {
        if(result.done) return result.value;    //result.value:undefined

        return result.value
            .then((value) => {
                return go(it.next(value));  //it.next('foo')  go(Object { done=true,  value=undefined})
            })
            .catch((error) => {
                return go(it.throw(error));
            });
    }

    go(it.next());  //Object { value=Promise,  done=false}

}

run(g);
```



异步操作按顺序执行

```
//按顺序执行

let p = new Promise((resolve,reject) => {
    reject('reject:p');
});

let p1 = new Promise((resolve,reject) => {
    resolve('resolve:p1');
});

let p2 = new Promise((resolve,reject) => {
    resolve('resolve:p2');
});


function* g() {
    try {
        yield p;
        yield p1;
        yield p2;
        //可以采用yield* [p,p1,p2]代替
    } catch(e) {
        console.log(e);
    }
}


function run(generator) {
    let it = generator();

    let result = it.next();

    while(!result.done) {
        result.value.then((value) => console.log('resolve:' + value))
                    .catch((err)  => console.log('reject:' + err));

        result = it.next();         
    }
}

run(g);

//resolve:resolve:p1
//resolve:resolve:p2
//reject:reject:p
```



只要有一个是拒绝状态,就返回拒绝状态

```
let p = new Promise((resolve,reject) => {
    reject('reject:p');
});

let p1 = new Promise((resolve,reject) => {
    resolve('resolve:p1');
});

let p2 = new Promise((resolve,reject) => {
    resolve('resolve:p2');
});


function* g() {
    try {
        let value = yield  Promise.all([p,p1,p2]);
        console.log('try:' + value);        
    } catch(e) {
        console.log('catch:' + e);      //catch:reject:p
    }
}

function run(generator) {
    let it = generator();
    it.next().value.then((value) => it.next(value))
                   .catch((err)  => it.throw(err));
}

run(g);
```



全部状态是`Resolved`,则返回`Resolved`

```
let p = new Promise((resolve,reject) => {
    resolve('resolve:p');
});

let p1 = new Promise((resolve,reject) => {
    resolve('resolve:p1');
});

let p2 = new Promise((resolve,reject) => {
    resolve('resolve:p2');
});


function* g() {
    try {
        let value = yield  Promise.all([p,p1,p2]);
        console.log('try:' + value);    //try:resolve:p,resolve:p1,resolve:p2   
    } catch(e) {
        console.log('catch:' + e);      
    }
}


function run(generator) {
    let it = generator();
    it.next().value.then((value) => it.next(value))
                   .catch((err)  => it.throw(err));
}

run(g);
```



## 异步操作和Async

### Promise

```
fs.readFile(fileA, function (err, data) {
  fs.readFile(fileB, function (err, data) {
    // ...
  });
});
```



Promise可以解决`callback hell`

```
var readFile = require('fs-readfile-promise');

readFile(fileA)
.then(function(data){
  console.log(data.toString());
})
.then(function(){
  return readFile(fileB);   //可以继续返回Promise对象
})
.then(function(data){
  console.log(data.toString());
})
.catch(function(err) {
  console.log(err);
});
```



缺陷:`Promise`的最大问题是代码冗余,原来的任务被`Promise`包装了一下,不管什么操作,一眼看去都是一堆`then`,原来的语义变得很不清楚.

### Generator

协程

```
function* asyncTasks() {
    let value = yield new Promise((resolve) => resolve('task A'));
    console.log(value); //task A
}

let task = asyncTasks();
task.next().value.then(value => task.next(value)); 
```



协程遇到`yield`命令就暂停,等到执行权返回,再从暂停的地方继续往后执行.它的最大优点,就是代码的写法非常像同步操作,如果去除`yield`命令,简直一模一样.

#### Generator函数的数据交换和错误处理

`Generator`函数可以暂停执行和恢复执行,这是它能封装异步任务的根本原因.除此之外,它还有两个特性,使它可以作为异步编程的完整解决方案：函数体内外的数据交换和错误处理机制. `next`方法返回值的`value`属性,是`Generator`函数向外输出数据；`next`方法还可以接受参数,这是向`Generator`函数体内输入数据.

```
function* gen(x) {
    try {
        var y = yield x + 2;　　　 
    } catch(e) {
        console.log(e);
    }
    return y;
}

let g = gen(2);
console.log(g.next());  //Object { value=4,  done=false}

//此时传入给y的值是5,也就是传入给上一个yield的返回值(作为上个阶段异步任务的返回结果)
console.log(g.next(5)); //Object { value=5,  done=true}
```



#### 异步任务

```
function* gen() {
    try {
        var y = yield new Promise((resolve,reject) => resolve('resolve task')); 
    } catch(e) {
        console.log(e);
    }
    console.log(y);
}


let result = gen();

result.next().value
      .then(data => {
        return data +　' add some content';
      })
      .then(data => {
        result.next(data);  //resolve task add some content
      });
```



### Thunk

传值调用(C语言就是这么干的)

```
function f(m) {     //m=7
    return m*2;
}

let x= 2;
f(x+5); //7*2 传入f的参数是先经过计算后的7
```



传名调用

```
function f(m,b) {   //m=x+5
    return b*2;
}

let x= 2;
f(x+5,7);   //传入f的参数先不计算,因为根本没用到,先计算就造成了性能消耗
```



#### Thunk函数传名调用

```
let x = 5;

let thunk = () => x + 5;

function f(thunk) {
    return thunk() * 2; //传入之后才计算
}

console.log(f(thunk));  //20
```



#### JavaScript中的Thunk

`JavaScript`本身是传值调用的.在`JavaScript`语言中,`Thunk`函数替换的不是表达式,而是多参数函数,将其替换成单参数的版本,且只接受回调函数作为参数.

```
//多参数,其中最后一个是回调函数
fs.readFile(fileName,callback);

let thunk = fileName => {
    return (callback) => fs.readFile(fileName,callback); 
}


let readFileThunk = thunk(fileName);
readFileThunk(callback);    //只有一个回调函数作为参数了
```



`Thunk`函数转换器

```
//ES5
var Thunk = function(fn) {
    return function() {
        //将arguments类数组转换成真正的数组
        var args = Array.prototype.slice.call(arguments);
        return function (callback) {
            args.push(callback);    //这里就可以使用数组的方法了
            return fn.apply(this,args);
        }   
    }
}


//ES6
let Thunk = (fn) => {   //fn是需要转换的多参数带有回调函数的函数
    return (...args) => {
        return (callback) => {
            return fn.call(this,...args,callback);
        }
    }
}

//使用方法
let readFileThunk = Thunk(fs.readFile);
readFileThunk(fileName)(callback);
```



#### Thunkify模块

生产环境的转换器,使用`Thunkify`模块

```
npm install thunkify
```



使用方法

```
const thunkify = require('thunkify');
const fs = require('fs');

let read = thunkify(fs.readFile);
read('app.txt')(callback);
```



#### Thunk函数的应用

首先来看看`Generator`函数的自动执行

```
function* gen() {
    //...
}

let g = gen();
let res = g.next();

while(!res.done) {
    console.log(res.value);
    res = g.next();
}
```



需要注意的是这种方法不适合异步操作,如果必须保证前一步执行完才可以进行后一步操作,那么Thunk函数就非常有用了

```
const fs = require('fs');
const thunkify = require('thunkify');
const readFile = thunkify(fs.readFile);
//使用方法 readFile(fileName)(callback);

function* gen() {
    let f1 = yield readFile('1.txt');   //返回的是有一个回调函数作为参数的函数
    console.log(f1.toString());
    let f2 = yield readFile('2.txt');
    console.log(f2.toString()); 
}


let g = gen();

let f1 = g.next();

f1.value(function(err,data){
    if(err) throw err;
    let f2 = g.next(data);
    f2.value(function(err,data){
        if(err) throw err;
        g.next(data);
    });
});
```



#### Thunk函数的自动流程管理

自动执行Generator函数

```
const fs = require('fs');
const thunkify = require('thunkify');
const readFile = thunkify(fs.readFile);
//使用方法 readFile(fileName)(callback);

function* gen() {
    let f1 = yield readFile('1.txt');   //返回的是Promise对象
    let f2 = yield readFile('2.txt');
    let f3 = yield readFile('2.txt');
    let f4 = yield readFile('2.txt');
}


function run(fn) {
    let gen = fn();

    function next(err,data) {   //next是Thunk的回调函数
        let result = gen.next(data);
        if(result.done) return;
        result.value(next);
    }
}

run(gen);   
```



> 提示:使用这种方法跟在`yield`后面的必须是thunk函数,当然`Promise`也可以做到这个

### co

该模块可以让你不用编写`Generator`函数的执行器

```
const fs = require('fs');
const thunkify = require('thunkify');
const readFile = thunkify(fs.readFile);
const co = require('co');

function* gen() {
    let f1 = yield readFile('1.txt');   //返回的是Thunk函数,该函数只有一个参数就是回调函数
    let f2 = yield readFile('2.txt');
    let f3 = yield readFile('2.txt');
    let f4 = yield readFile('2.txt');
}

co(gen);    //自动执行Generator函数

//返回的是一个Primise对象
co(gen).then(() => {
    console.log('Generator done!');
})
```



#### 原理

前面说过,`Generator`就是一个异步操作的容器.它的自动执行需要一种机制,当异步操作有了结果,能够自动交回执行权.两种方法可以做到这一点

- 回调函数,将异步操作包装成`Thunk`函数,在回调函数中交回执行权
- `Promise`对象,将异步操作包装成`Promise`对象,用`then`方法交回执行权

`co`模块其实就是将两种自动执行器（`Thunk`函数和`Promise`对象）,包装成一个模块.使用`co`的前提条件是,`Generator`函数的`yield`命令后面,只能是`Thunk`函数或`Promise`对象

#### 基于Promise对象的自动执行

```
const fs = require('fs');

//之前是使用了Thunk函数,返回的是回调函数,这里返回Promise对象
let readFile = function(fileName) {
    return new Promise((resolve,reject) => {
        fs.readFile(fileName,(err,data) => {
            if(err) {
                return reject(err);
            }
            resolve(data);
        });
    });
}



function* gen() {
    let f1 = yield readFile('1.txt');   //返回的是Thunk函数,该函数只有一个参数就是回调函数
    let f2 = yield readFile('2.txt');
    console.log(f1.toString() + f2.toString());
}


//手动执行
let g = gen();
g.next().value.then((data) => {
    g.next().value.then((data) => {
        g.next(data);
    });
});


//自动执行器
function run(gen) {
    let g = gen();

    function next(data) {
        let result = g.next(data);
        if(result.done) return result.value;
        result.value.then((data) => next(data));
    }

    next();
}

run(gen);
```



#### 处理并发的异步操作

`co`支持并发的异步操作,即允许某些操作同时进行,等到它们全部完成,才进行下一步

```
// 数组的写法
co(function* () {
  var res = yield [
    Promise.resolve(1),
    Promise.resolve(2)
  ];
  console.log(res);
}).catch(onerror);

// 对象的写法
co(function* () {
  var res = yield {
    1: Promise.resolve(1),
    2: Promise.resolve(2),
  };
  console.log(res);
}).catch(onerror);
```



上面的代码允许并发两个`somethingAsync`异步操作,等到它们全部完成,才会进行下一步

```
co(function* () {
  var values = [n1, n2, n3];
  yield values.map(somethingAsync);
});

function* somethingAsync(x) {
  // do something async
  return y
}
```



### async

```
var fs = require('fs');

var readFile = function (fileName) {
  return new Promise(function (resolve, reject) {
    fs.readFile(fileName, function(error, data) {
      if (error) reject(error);
      resolve(data);
    });
  });
};

var gen = function* (){
  var f1 = yield readFile('/etc/fstab');
  var f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```



如果写成`async`函数

```
var asyncReadFile = async function (){
  var f1 = await readFile('/etc/fstab');
  var f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```



将`*`替换成`async`,`yield`替换成`await`

- 内置执行器. `Generator`函数的执行必须靠执行器,所以才有了`co`模块,而`async`函数自带执行器.也就是说,`async`函数的执行,与普通函数一模一样,只要一行.

```
var result = asyncReadFile();
```



- 更好的语义 `async`和`await`,比起`*`和`yield`,语义更清楚了.`async`表示函数里有异步操作,`await`表示紧跟在后面的表达式需要等待结果
- 更广的适用性 `co`模块约定,`yield`命令后面只能是`Thunk`函数或`Promise`对象,而`async`函数的`await`命令后面,可以是`Promise`对象和原始类型的值（数值、字符串和布尔值,但这时等同于同步操作
- 返回值是`Promise` `async`函数的返回值是`Promise`对象,这比`Generator`函数的返回值是`Iterator`对象方便多了

略.

## Class

```
'use strict';

//ES5
function Person(name,age) {
    this.name = name;
    this.age = age;
}

Person.prototype.sayInfo = function() {
    console.log(this.name + ':' + this.age);
}


var p = new Person('ziyi2',23);
p.sayInfo();    //ziyi2:23



//ES6
class People {
    //构造方法
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }
    //不需要,分隔
    //类的所有方法都定义在类的prototype属性上
    sayInfo() {
        console.log(`${this.name}:${this.age}`);
    }

    //等同于
    // People.prototype.sayInfo = function() {
    // console.log(`${this.name}:${this.age}`);
    // }
}


let p1 = new People('ziyi3',35);
p1.sayInfo();   //ziyi3:35
```



添加新的方法到原型对象

```
Object.assign(People.prototype, {
    sayName() {
        console.log(this.name);
    },

    sayAge() {
        console.log(this.age);
    }
});


p1.sayAge();    ///35
```



基本性质与`ES5`不一致的是,类的内部所有定义的方法,都是不可枚举的

```
//Object.keys返回对象自身的所有可枚举的属性的键名
let keys = Object.keys(People.prototype);
console.log(keys);  //["sayName", "sayAge"] sayInfo不可枚举

keys = Object.keys(People);
console.log(keys);  //[] 

keys = Object.getOwnPropertyNames(People);
console.log(keys);  //["prototype", "length", "name"]

keys = Object.getOwnPropertyNames(People.prototype);
console.log(keys);  //["constructor", "sayInfo", "sayName", "sayAge"]
```



属性名可以采用表达式的形式

```
let x = 'ziyi2';
let property = () => `Greeting ${x}`;

class People {
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }

    [property()]() {
        console.log(this.name);
    }

    
}


let p1 = new People('ziyi3',35);

p1[property()]();       //ziyi3
p1['Greeting ziyi2'](); //ziyi3
```



### class不存在变量提升

```
let x = 'ziyi2';

new People();   //can't access lexical declaration `People' before initialization

class People {
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }
}
```



> 提示:这样可以保证子类在父类之后定义

继承

```
let x = 'ziyi2';

class People {
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }
}

class Father extends People {
    sayName() {
        console.log(this.name);
    }
}

let p = new Father('ziyi2',23);
p.sayName();    //ziyi2
```



### class表达式

`Class`也可以使用表达式的形式定义

```
const P = class People {
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }

    sayName() {
        console.log(`${this.name} : ${this.age}`);
    }
}

let p = new P('ZIYI2',34);
p.sayName();    //ZIYI2 : 34
```



> 提示:此时类的名字是`P`而不是`People`,`People`在内部代码可用,指代当前类

```
const P = class {
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }

    sayName() {
        console.log(`${this.name} : ${this.age}`);
    }
}

let p = new P('ZIYI2',34);
p.sayName();        //ZIYI2 : 34
```



> 提示:可以省略People

### 私有方法

采用`_`命令的函数当做私有函数

```
const P = class {
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }

    //私有方法,此时外部仍然可以调用这个方法,这种命令方式是自我的约定
    _sayName() {
        console.log(`${this.name} : ${this.age}`);
    }
}

let p = new P('ZIYI2',34);
p._sayName();       //ZIYI2 : 34
```



导出的时候如果第三方无法轻易获取,也可以当做私有方法,`bar`和`snaf`都是`Symbol`值,导致第三方无法获取到它们,因此达到了私有方法和私有属性的效果

```
const bar = Symbol('bar');
const snaf = Symbol('snaf');

export default subclassFactory({

  // 公有方法
  foo (baz) {
    this[bar](baz);
  }

  // 私有方法
  [bar](baz) {
    return this[snaf] = baz;
  }

  // ...
});
```



### 严格模式

类和模块的内部,默认就是严格模式,所以不需要使用`use strict`指定运行模式.只要你的代码写在类或模块之中,就只有严格模式可用. 考虑到未来所有的代码,其实都是运行在模块之中,所以ES6实际上把整个语言升级到了严格模式

### name

```
class P {}
console.log(P.name);    //P
```



### class继承

使用`extends`关键字实现继承

```
class Person {
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }

    sayInfo() {
        console.log(`${this.name} : ${this.age}`);
    }
}


class Father extends Person     //此时和父类一模一样

let f = new Father('father',56);
f.sayInfo();    //father : 56
```



`super`关键字用来指代父类的构造函数

```
class Person {
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }

    sayInfo() {
        //console.log(`${this.name} : ${this.age}`);
        return `${this.name} : ${this.age}`
    }
}


class Father extends Person {
    constructor(name,age,job) {
        super(name,age);    //调用父类的构造函数
        this.job = job;
    } 

    sayFatherInfo() {
        console.log(`${super.sayInfo()} : ${this.job}`);    //调用父类的sayInfo()
    }

}

let f = new Father('father',56,'doctor');
f.sayFatherInfo();  //father : 56 : doctor
```



子类必须在constructor方法中调用`super`方法,否则会报错,因为子类没有自己的`this`对象,而是继承父类的`this`对象,如果不调用`super`方法,子类就得不到`this`对象

```
class Person {
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }

    sayInfo() {
        //console.log(`${this.name} : ${this.age}`);
        return `${this.name} : ${this.age}`
    }
}


class Father extends Person {
    constructor(name,age,job) {
        
    } 
}

let f = new Father();   //|this| used uninitialized in Father class constructor
```



> 提示:`ES5`的继承,实质是先创造子类的实例对象this,然后再将父类的方法添加到`this`上面（`Parent.apply(this)`）.`ES6`的继承机制完全不同,实质是先创造父类的实例对象`this`（所以必须先调用`super`方法）,然后再用子类的构造函数修改`this`

如果子类不定义`constructor`方法,会被默认添加

```
class Person {
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }

    sayInfo() {
        console.log(`${this.name} : ${this.age}`);
        //return `${this.name} : ${this.age}`
    }
}


class Father extends Person {}

//等同于
// class Father extends Person {
//  constructor(...args) {
//      super(...args);
//  }
// }

let f = new Father('ziyi2',23); 
f.sayInfo();    //ziyi2 : 23
```



由于子类的`this`是继承自父类的`this`对象,所以必须是先调用`super`后才能使用`this`,否则会报错

```
class Person {
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }

    sayInfo() {
        console.log(`${this.name} : ${this.age}`);
        //return `${this.name} : ${this.age}`
    }
}


class Father extends Person {
    constructor(...args) {
        this.name = 23; //|this| used uninitialized in Father class constructor
        super(...args);
    }
}

let f = new Father('ziyi2',23); 
```



```
class Person {
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }

    sayInfo() {
        console.log(`${this.name} : ${this.age}`);
        //return `${this.name} : ${this.age}`
    }
}


class Father extends Person {
    constructor(...args) {
        super(...args);
    }
}

let f = new Father('ziyi2',23); 


console.log(f instanceof Father);   //true
console.log(f instanceof Person);   //true 与ES5行为一致
```



### prototype和__proto__

- prototype 子类的__proto__属性,表示构造函数的继承,总是指向父类
- **proto** 子类prototype属性的__proto__属性,表示方法的继承,总是指向父类的prototype属性

```
class Person {}
class Father extends Person {}

console.log(Father.__proto__ === Person)    //true
console.log(Father.prototype.__proto__=== Person.prototype);    //true
```



### Extends的继承目标

只要`B`有`prototype`属性,就能被`A`继承

```
class A extends B {}
```



### Object.getPrototypeOf()

从子类上获取父类

```
class B {}
class A extends B {}
let bool = Object.getPrototypeOf(A) === B;
console.log(bool);  //true
```



### super

- 作为函数调用时（即`super(...args)`）,`super`代表父类的构造函数
- 作为对象调用时（即`super.prop`或`super.method()`）,`super`代表父类.注意,此时`super`即可以引用父类实例的属性和方法,也可以引用父类的静态方法

### 原生构造函数的继承

- Boolean
- Number
- String
- Array
- Date
- Function
- RegExp
- Error
- Object

继承原生的构造函数

```
class MyArray extends Array {
  constructor(...args) {
    super(...args);
  }
}

var arr = new MyArray();
arr[0] = 12;
arr.length // 1

arr.length = 0;
arr[0] // undefined
```



### getter和setter

```
class MyClass {
  constructor() {
    // ...
  }
  get prop() {
    return 'getter';
  }
  set prop(value) {
    console.log('setter: '+value);
  }
}

let inst = new MyClass();

inst.prop = 123;
// setter: 123

inst.prop
// 'getter'
```



### Class的Generator方法

`Foo`类的`Symbol.iterator`方法前有一个星号,表示该方法是一个`Generator`函数.`Symbol.iterator`方法返回一个`Foo`类的默认遍历器,`for...of`循环会自动调用这个遍历器.

```
class Foo {
  constructor(...args) {
    this.args = args;
  }
  * [Symbol.iterator]() {
    for (let arg of this.args) {
      yield arg;
    }
  }
}

for (let x of new Foo('hello', 'world')) {
  console.log(x);
}
// hello
// world
```



### Class的静态属性和实例属性

静态属性指的是`Class`本身的属性,即`Class.propname`,而不是定义在实例对象（`this`）上的属性

```
class B {}
console.log(B.name);    //B

B.version = 1.0;
console.log(B.version); //1
```



`ES6`明确规定,`Class`内部只有静态方法,没有静态属性

```
class B {
    version:1.0
}
console.log(B.version); //bad method definition
```



`ES7`有一个静态属性的提案,目前`Babel`转码器支持

```
class MyClass {
  version = 42;

  constructor() {
    console.log(this.version); // 42
  }
}
```



以前,我们定义实例属性,只能写在类的constructor方法里面

```
class ReactCounter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }
}
```



有了新的写法以后,可以不在`constructor`方法里面定义

```
class ReactCounter extends React.Component {
  state = {
    count: 0
  };
}
```



`ES7`中类的静态属性只要在上面的实例属性写法前面,加上`static`关键字就可以了

```
class MyClass {
  static myStaticProp = 42;

  constructor() {
    console.log(MyClass.myProp); // 42
  }
}
```



```
// 老写法
class Foo {
}
Foo.prop = 1;

// 新写法
class Foo {
  static prop = 1;
}
```



### new.target

```
new.target`属性返回`new`命令作用于的那个构造函数(当前类),如果构造函数不是通过new命令调用的,`new.target`会返回`undefined
class Person {
    constructor(name,age) {
        console.log(new.target === Person); //true
        this.name = name;
        this.age = age;
    }
}

let obj = new Person('ziyi2',23);
```



> 提示: 子类继承父类时,`new.target`返回子类

```
class Person {
    constructor(name,age) {
        console.log(new.target);    //Father()
        this.name = name;
        this.age = age;
    }
}

class Father extends Person {
    constructor() {
        super();
    }
}

let p = new Father('ziyi2',23);
```



如果父类当做基类只用于被继承就非常有用了

```
class Person {
    constructor(name,age) {
        if(new.target === Person) {
            throw new Error('error');   
        }
        this.name = name;
        this.age = age;
    }
}

class Father extends Person {
    constructor() {
        super();
    }
}

let p = new Person('ziyi2',23); //Error: error
```



### Mixin模式的实现

略.

### Decorator

这是ES7的内容,目前Babel转换器已经支持.

## Module

`CommonJS`规范,运行时加载,因为只有运行时才能得到这个对象,导致完全没办法在编译时做“静态优化”

```
let {stat,readFile,exists} = require('fs');

//等同于
let _fs = require('fs');
let state = _fs.state, 
    exists = _fs.exists,
    readFile = _fs.readFile;
```



`ES6`模块不是对象,而是通过`export`命令显式指定输出的代码,输入时也采用静态命令的形式

```
import {state,exists,readFile} from 'fs';
```



上面代码的实质是从`fs`模块加载3个方法,其他方法不加载,需要注意的是不是在运行时先获取整个`fs`模块对象,这种加载称为“编译时加载”,即`ES6`可以在编译时就完成模块加载,效率要比`CommonJS`模块的加载方式高,当然,这也导致了没法引用`ES6`模块本身,因为它不是对象.

- 不再需要UMD模块格式了,将来服务器和浏览器都会支持ES6模块格式
- 将来浏览器的新API就能用模块格式提供,不再必要做成全局变量或者`navigator`对象的属性
- 不再需要对象作为命名空间（比如`Math`对象）,未来这些功能可以通过模块提供

浏览器使用ES6模块的语法如下,在网页中插入一个模块`foo.js`,由于`type`属性设为`module`,所以浏览器知道这是一个`ES6`模块

```
<script type="module" src="foo.js"></script>
```



`Node`的默认模块格式是`CommonJS`,目前还没决定怎么支持`ES6`模块.所以,只能通过`Babel`这样的转码器,在`Node`里面使用`ES6`模块

### 严格模式

`ES6`的模块自动采用严格模式,不管你有没有在模块头部加上`"use strict"`;

- 变量必须声明后再使用
- 函数的参数不能有同名属性,否则报错
- 不能使用`with`语句
- 不能对只读属性赋值,否则报错
- 不能使用前缀0表示八进制数,否则报错
- 不能删除不可删除的属性,否则报错
- 不能删除变量`delete prop`,会报错,只能删除属性`delete global[prop]`
- `eval`不会在它的外层作用域引入变量
- `eval`和`arguments`不能被重新赋值
- `arguments`不会自动反映函数参数的变化
- 不能使用`arguments.callee`
- 不能使用`arguments.caller`
- 禁止`this`指向全局对象
- 不能使用`fn.caller`和`fn.arguments`获取函数调用的堆栈
- 增加了保留字（比如`protected`、`static`和`interface`）

上面这些限制,模块都必须遵守.

### export

`export`命令用于规定模块的对外接口,`import`命令用于输入其他模块提供的功能,对外输出两个变量

```
//profile.js
export var firstName = 'ziyi2'
export var lastName = 'ziyi3';
```



更清晰的写法,与上面方法相同,但是写在尾部更直观

```
//profile.js
var firstName = 'ziyi2'
var lastName = 'ziyi3';
export {firstName,lastName};
```



常规写法

```
//输出函数或者类
export function f(x,y) {
    return x*y;
}


//输出的变量还可以重命名,使用as关键字
function f() {}
function f1() {}

export {
    f as function1
    f1 as function2
};


//error写法,会报错
export 1;
var m = 1;
export m;


//写成接口形式
export var m = 1;

var m = 1;
export {m};

var m1 = 1;
export {m1 as m};


//error写法
function f() {}
export f;

//写成接口形式
export function f() {};

function f() {}
export {f};


//动态绑定关系
export var name = 'ziyi2';
setTimeout(() => foo = 'baz', 500);
//500ms后的值为baz,导出的值也会变成baz,CommonJS规范因为输出的是值得缓存,所以不存在动态更新


//error写法
function foo() {
    export default 'bar'    //SyntaxError
}

//export可以出现在模块的任何位置都是必须处于顶层作用域,否则会报错
```



### import

```
//profile.js
var firstName = 'ziyi2'
var lastName = 'ziyi3';
export {firstName,lastName};

//main.js
import {firstName,lastName} from './profile';

//import命令接收一个对象,里面指定其他模块导入的变量名
//需要注意的是{}中的变量名必须与被导入模块对外接口的名称相同

//重命名
import {firstName as name} from './profile';


//import会有提升作用
console.log(firstName); //不会报错
import {firstName,lastName} from './profile';


//import语句执行所加载的模块,执行babel-profill模块,但是不输入任何值
import 'babel-profill'
```



### 模块的整体加载

```
//用*指定一个对象,所有的输出值都加载到这个对象上面

//circle.js
export function area(radius) {
    //some code
}

export function circumference(radius) {
    //some code
}

//main.js
import * as circle from './circle';
circle.area(3);
circle.circumference(3);
```



### export default

使用`import`命令的时候,用户需要知道所要加载的变量名或函数名,否则无法加载,用户肯定希望快速上手,未必愿意阅读文档,去了解模块有哪些属性和方法,为了给用户提供方便,让他们不用阅读文档就能加载模块,就要用到`export default`命令,为模块指定默认输出,加载该模块时,`import`命令可以为该匿名函数指定任意名字

```
//default.js
export default function() {
    console.log('foo');
}

//main.js
import defaultName from './default';
defaultName();
```



非匿名函数也是可以的

```
//default.js
export default function foo() {
    console.log('foo');
}

//或者
function foo() {
    console.log('foo');
}

export default foo;
```



使用`export default`时,对应的`import`语句不需要使用大括号；不使用`export default`时,对应的`import`语句需要使用大括号

```
//默认输出和正常输出
// 输出
export default function crc32() {
  // ...
}
// 输入
import crc32 from 'crc32';

// 输出
export function crc32() {
  // ...
};
// 输入
import {crc32} from 'crc32';
```



本质上,`export default`就是输出一个叫做`default`的变量或方法,然后系统允许你为它取任意名字

```
// modules.js
function add(x, y) {
  return x * y;
}
export {add as default};
// 等同于
// export default add;

// app.js
import { default as xxx } from 'modules';
// 等同于
// import xxx from 'modules';
```



正是因为`export default`命令其实只是输出一个叫做`default`的变量,所以它后面不能跟变量声明语句,`export default a`的含义是将变量`a`的值赋给变量`default`

```
// 正确
export var a = 1;

// 正确
var a = 1;
export default a;

// 错误
export default var a = 1;
```



输入jQuery

```
import $ from 'jquery';
```



如果想在一条`import`语句中,同时输入默认方法和其他变量,可以写成下面这样

```
import customName, { otherMethod } from './export-default';
```



`export default`也可以用来输出类

```
// MyClass.js
export default class { ... }

// main.js
import MyClass from 'MyClass';
let o = new MyClass();
```



### 模块的继承

假设有一个`circleplus`模块,继承了`circle`模块

```
// circleplus.js

export * from 'circle';
export var e = 2.71828182846;
export default function(x) {
  return Math.exp(x);
}
```



上面代码中的`export *`,表示再输出`circle`模块的所有属性和方法,注意,`export *`命令会忽略`circle`模块的`default`方法

也可以改名后再输出

```
// circleplus.js
export { area as circleArea } from 'circle';

// main.js
import * as math from 'circleplus';
```



### ES6模块加载的实质

`ES6`模块加载的机制,与`CommonJS`模块完全不同.`CommonJS`模块输出的是一个值的拷贝,而`ES6`模块输出的是值的引用,`CommonJS`模块输出的是被输出值的拷贝,也就是说,一旦输出一个值,模块内部的变化就影响不到这个值

```
// lib.js
var counter = 3;
function incCounter() {
  counter++;
}
module.exports = {
  counter: counter,
  incCounter: incCounter,
};

// main.js
var mod = require('./lib');

console.log(mod.counter);  // 3
mod.incCounter();
console.log(mod.counter); // 3
```



`ES6`模块的运行机制与`CommonJS`不一样,它遇到模块加载命令`import`时,不会去执行模块,而是只生成一个动态的只读引用.等到真的需要用到时,再到模块里面去取值,换句话说,`ES6`的输入有点像`Unix`系统的“符号连接”,原始值变了,`import`输入的值也会跟着变.因此,`ES6`模块是动态引用,并且不会缓存值,模块里面的变量绑定其所在的模块

```
// lib.js
export let counter = 3;
export function incCounter() {
  counter++;
}

// main.js
import { counter, incCounter } from './lib';
console.log(counter); // 3
incCounter();
console.log(counter); // 4
```



由于`ES6`输入的模块变量,只是一个“符号连接”,所以这个变量是只读的,对它进行重新赋值会报错

```
// lib.js
export let obj = {};

// main.js
import { obj } from './lib';

obj.prop = 123; // OK
obj = {}; // TypeError
```



main.js从`lib.js`输入变量`obj`,可以对`obj`添加属性,但是重新赋值就会报错.因为变量obj指向的地址是只读的,不能重新赋值,这就好比`main.js`创造了一个名为`obj`的`const`变量.

`export`通过接口,输出的是同一个值.不同的脚本加载这个接口,得到的都是同样的实例

```
// mod.js
function C() {
  this.sum = 0;
  this.add = function () {
    this.sum += 1;
  };
  this.show = function () {
    console.log(this.sum);
  };
}

export let c = new C();
```



### 循环加载

“循环加载”（circular dependency）指的是,`a`脚本的执行依赖`b`脚本,而`b`脚本的执行又依赖`a`脚本,通常,“循环加载”表示存在强耦合,如果处理不好,还可能导致递归加载,使得程序无法执行,因此应该避免出现

#### CommonJS

```
{
  id: '...',
  exports: { ... },
  loaded: true,
  ...
}
```



`CommonJS`的一个模块,就是一个脚本文件.`require`命令第一次加载该脚本,就会执行整个脚本,然后在内存生成一个对象,上面代码就是`Node`内部加载模块后生成的一个对象.该对象的`id`属性是模块名,`exports`属性是模块输出的各个接口,`loaded`属性是一个布尔值,表示该模块的脚本是否执行完毕.其他还有很多属性,这里都省略了,以后需要用到这个模块的时候,就会到`exports`属性上面取值.即使再次执行`require`命令,也不会再次执行该模块,而是到缓存之中取值.也就是说,`CommonJS`模块无论加载多少次,都只会在第一次加载时运行一次,以后再加载,就返回第一次运行的结果,除非手动清除系统缓存

```
//a.js
exports.done = false;
var b = require('./b.js');
console.log('在 a.js 之中,b.done = %j', b.done);
exports.done = true;
console.log('a.js 执行完毕');

//b.js
exports.done = false;
var a = require('./a.js');
console.log('在 b.js 之中,a.done = %j', a.done);
exports.done = true;
console.log('b.js 执行完毕');

//main.js
var a = require('./a.js');
var b = require('./b.js');    //[main.js执行到第二行时,不会再次执行b.js,而是输出缓存的b.js的执行结果]    
console.log('在 main.js 之中, a.done=%j, b.done=%j', a.done, b.done);

//console
在 b.js 之中,a.done = false  [在b.js之中,a.js没有执行完毕,只执行了第一行]
b.js 执行完毕
在 a.js 之中,b.done = true
a.js 执行完毕
在 main.js 之中, a.done=true, b.done=true
```



由于`CommonJS`模块遇到循环加载时,返回的是当前已经执行的部分的值,而不是代码全部执行后的值,两者可能会有差异,所以,输入变量的时候,必须非常小心

```
var a = require('a'); // 安全的写法
var foo = require('a').foo; // 危险的写法

exports.good = function (arg) {
  return a.foo('good', arg); // 使用的是 a.foo 的最新值
};

exports.bad = function (arg) {
  return foo('bad', arg); // 使用的是一个部分加载时的值
};
```



#### ES6

```
// a.js如下
import {bar} from './b.js';
console.log('a.js');
console.log(bar);
export let foo = 'foo';

// b.js
import {foo} from './a.js';
console.log('b.js');
console.log(foo);
export let bar = 'bar';

//console
$ babel-node a.js
b.js
undefined
a.js
bar
```



`a.js`的第一行是加载`b.js`,所以先执行的是`b.js`.而`b.js`的第一行又是加载`a.js`,这时由于`a.js`已经开始执行了,所以不会重复执行,而是继续往下执行`b.js`,所以第一行输出的是`b.js`,接着,`b.js`要打印变量`foo`,这时`a.js`还没执行完,取不到`foo`的值,导致打印出来是`undefined`.`b.js`执行完,开始执行`a.js`,这时就一切正常了

`ES6`加载的变量,都是动态引用其所在的模块.只要引用存在,代码就能执行.

### 跨模块常量

```
//constants.js
export const A = 1;
export const B = 2;
export const C = 3;

//test.js
import * as constants from './constants';
console.log(constants.A);

//test1.js
import {A,B} from './constants';
console.log(A);
```



## 编程风格

函数的参数如果是对象的成员,优先使用解构赋值

```
// bad
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;
}

// good
function getFullName(obj) {
  const { firstName, lastName } = obj;
}

// best
function getFullName({ firstName, lastName }) {
}
```



如果函数返回多个值,优先使用对象的解构赋值,而不是数组的解构赋值.这样便于以后添加返回值,以及更改返回值的顺序

```
// bad
function processInput(input) {
  return [left, right, top, bottom];
}

// good
function processInput(input) {
  return { left, right, top, bottom };
}

const { left, right } = processInput(input);
```



单行定义的对象,最后一个成员不以逗号结尾.多行定义的对象,最后一个成员以逗号结尾

```
// bad
const a = { k1: v1, k2: v2, };
const b = {
  k1: v1,
  k2: v2   
};

// good
const a = { k1: v1, k2: v2 //no , };   
const b = {
  k1: v1,
  k2: v2,  //,
};
```



对象尽量静态化,一旦定义,就不得随意添加新的属性.如果添加属性不可避免,要使用`Object.assign`方法

```
// bad
const a = {};
a.x = 3;

// if reshape unavoidable
const a = {};
Object.assign(a, { x: 3 });

// good
const a = { x: null };
a.x = 3;
```



```
var ref = 'some value';

// bad
const atom = {
  ref: ref,

  value: 1,

  addValue: function (value) {
    return atom.value + value;
  },
};

// good
const atom = {
  ref,

  value: 1,

  addValue(value) {
    return atom.value + value;
  },
};
```



使用扩展运算符（`...`）拷贝数组

```
// bad
const len = items.length;
const itemsCopy = [];
let i;

for (i = 0; i < len; i++) {
  itemsCopy[i] = items[i];
}

// good
const itemsCopy = [...items];
```



使用`Array.from`方法,将类似数组的对象转为数组

```
const foo = document.querySelectorAll('.foo');
const nodes = Array.from(foo);
```



箭头函数

```
//best
(() => {
  console.log('Welcome to the Internet.');
})();

// bad
[1, 2, 3].map(function (x) {
  return x * x;
});

// good
[1, 2, 3].map((x) => {
  return x * x;
});

// best
[1, 2, 3].map(x => x * x);
```



箭头函数取代`Function.prototype.bind`,不应再用`self/_this/that`绑定 `this`

```
// bad
const self = this;
const boundMethod = function(...params) {
  return method.apply(self, params);
}

// acceptable
const boundMethod = method.bind(this);

// best
const boundMethod = (...params) => method.apply(this, params);
```



> 提示: 简单的、单行的、不会复用的函数,建议采用箭头函数.如果函数体较为复杂,行数较多,还是应该采用传统的函数写法

所有配置项都应该集中在一个对象,放在最后一个参数,布尔值不可以直接作为参数

```
// bad
function divide(a, b, option = false ) {
}

// good
function divide(a, b, { option = false } = {}) {
}
```



不要在函数体内使用`arguments`变量,使用rest运算符（`...`）代替.因为`rest`运算符显式表明你想要获取参数,而且`arguments`是一个类似数组的对象,而`rest`运算符可以提供一个真正的数组

```
// bad
function concatenateAll() {
  const args = Array.prototype.slice.call(arguments);
  return args.join('');
}

// good
function concatenateAll(...args) {
  return args.join('');
}
```



使用默认值语法设置函数参数的默认值

```
// bad
function handleThings(opts) {
  opts = opts || {};
}

// good
function handleThings(opts = {}) {
  // ...
}
```



注意区分`Object`和`Map`,只有模拟现实世界的实体对象时,才使用`Object`.如果只是需要`key: value`的数据结构,使用`Map`结构.因为`Map`有内建的遍历机制

```
let map = new Map(arr);

for (let key of map.keys()) {
  console.log(key);
}

for (let value of map.values()) {
  console.log(value);
}

for (let item of map.entries()) {
  console.log(item[0], item[1]);
}
```



Class

```
// bad
function Queue(contents = []) {
  this._queue = [...contents];
}
Queue.prototype.pop = function() {
  const value = this._queue[0];
  this._queue.splice(0, 1);
  return value;
}

// good
class Queue {
  constructor(contents = []) {
    this._queue = [...contents];
  }
  pop() {
    const value = this._queue[0];
    this._queue.splice(0, 1);
    return value;
  }
}

// bad
const inherits = require('inherits');
function PeekableQueue(contents) {
  Queue.apply(this, contents);
}
inherits(PeekableQueue, Queue);
PeekableQueue.prototype.peek = function() {
  return this._queue[0];
}

// good
class PeekableQueue extends Queue {
  peek() {
    return this._queue[0];
  }
}
```



使用`import`取代`require`

```
// bad
const moduleA = require('moduleA');
const func1 = moduleA.func1;
const func2 = moduleA.func2;

// good
import { func1, func2 } from 'moduleA';
```



使用`export`取代`module.exports`

```
// commonJS的写法
var React = require('react');

var Breadcrumbs = React.createClass({
  render() {
    return <nav />;
  }
});

module.exports = Breadcrumbs;

// ES6的写法
import React from 'react';

const Breadcrumbs = React.createClass({
  render() {
    return <nav />;
  }
});

export default Breadcrumbs
```



如果模块只有一个输出值,就使用`export default`,如果模块有多个输出值,就不使用`export default`,不要`export default`与普通的`export`同时使用

如果模块默认输出一个函数,函数名的首字母应该小写

```
function makeStyleGuide() {
}

export default makeStyleGuide;

const StyleGuide = {
  es6: {
  }
};

export default StyleGuide;
```



```
ESLint`是一个语法规则和代码风格的检查工具,可以用来保证写出语法正确、风格统一的代码.首先,安装`ESLint
$ npm i -g eslint
```



然后,安装`Airbnb`语法规则

```
$ npm i -g eslint-config-airbnb
```



最后,在项目的根目录下新建一个`.eslintrc`文件,配置`ESLint`

```
{
  "extends": "eslint-config-airbnb"
}
```



检查`index.js`

```
var unusued = 'I have no purpose!';

function greet() {
    var message = 'Hello, World!';
    alert(message);
}

greet();
```



```
$ eslint index.js
index.js
  1:5  error  unusued is defined but never used                 no-unused-vars
  4:5  error  Expected indentation of 2 characters but found 4  indent
  5:5  error  Expected indentation of 2 characters but found 4  indent

✖ 3 problems (3 errors, 0 warnings)
```



上面代码说明,原文件有三个错误,一个是定义了变量,却没有使用,另外两个是行首缩进为4个空格,而不是规定的2个空格

## 参考

本文出处请查看阮一峰大师的[ECMAScript 6入门](http://es6.ruanyifeng.com/)!