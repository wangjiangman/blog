---
layout:     post
title:      "ES6特性"
subtitle:   ""
date:       2018-01-04
author:     "Fiona"
header-img: "img/linux-bg.jpg"
tags:
    - 翻译
    - web
    - javascript
---

> 本文翻译自：https://github.com/lukehoban/es6features

# ECMAScript 6 <sup>[git.io/es6features](http://git.io/es6features)</sup>

## 入门介绍
ECMAScript 6, 也被称为ECMAScript 2015, 是ECMAScript在2015年的最新标准。 ES6是JavaScript语言的一次重大更新, 而上一次重要更新是2009年制定的ES5。这些特性在主要的Javascript引擎上的实现[正在进行](http://kangax.github.io/es5-compat-table/es6/).

查看[ES6 standard](http://www.ecma-international.org/ecma-262/6.0/)获取ECMAScript 6的全部详细文档。

ES6包含以下新特性:
- [arrows](#arrows)
- [classes](#classes)
- [enhanced object literals](#enhanced-object-literals)
- [template strings](#template-strings)
- [destructuring](#destructuring)
- [default + rest + spread](#default--rest--spread)
- [let + const](#let--const)
- [iterators + for..of](#iterators--forof)
- [generators](#generators)
- [unicode](#unicode)
- [modules](#modules)
- [module loaders](#module-loaders)
- [map + set + weakmap + weakset](#map--set--weakmap--weakset)
- [proxies](#proxies)
- [symbols](#symbols)
- [subclassable built-ins](#subclassable-built-ins)
- [promises](#promises)
- [math + number + string + array + object APIs](#math--number--string--array--object-apis)
- [binary and octal literals](#binary-and-octal-literals)
- [reflect api](#reflect-api)
- [tail calls](#tail-calls)

## ECMAScript 6 特性

### arrows
箭头是使用 `=>` 语法进行函数速写，它们在语法上与在C#，Java 8 和CoffeeScript中相关的特性类似。支持语句块体和表达式体，其中表达式体返回表达式的值。与函数不同的是，箭头表达式与包裹的代码共享词法上的`this`。

```JavaScript
// Expression bodies
var odds = evens.map(v => v + 1);
var nums = evens.map((v, i) => v + i);
var pairs = evens.map(v => ({even: v, odd: v + 1}));

// Statement bodies
nums.forEach(v => {
  if (v % 5 === 0)
    fives.push(v);
});

// Lexical this
var bob = {
  _name: "Bob",
  _friends: [],
  printFriends() {
    this._friends.forEach(f =>
      console.log(this._name + " knows " + f));
  }
}
```

更多信息: [MDN Arrow Functions](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

### Classes
ES6 classes是基于原型的面向对象模式的语法糖，拥有一个方便的声明使得类模式更加易用和具有互操作性。Classes支持基于原型的继承、super调用、实例、静态方法和构造函数。

```JavaScript
class SkinnedMesh extends THREE.Mesh {
  constructor(geometry, materials) {
    super(geometry, materials);

    this.idMatrix = SkinnedMesh.defaultMatrix();
    this.bones = [];
    this.boneMatrices = [];
    //...
  }
  update(camera) {
    //...
    super.update();
  }
  get boneCount() {
    return this.bones.length;
  }
  set matrixType(matrixType) {
    this.idMatrix = SkinnedMesh[matrixType]();
  }
  static defaultMatrix() {
    return new THREE.Matrix4();
  }
}
```

更多信息: [MDN Classes](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)

### Enhanced Object Literals
对象字面量被扩展，以支持在构造函数中设置原型， 对类似于`foo: foo`赋值、方法定义、supper调用和计算属性名使用表达式进行所写。同时，使得对象字面量和类的声明紧密联合起来,让基于对象的设计受益于一些相同的便利条件。

```JavaScript
var obj = {
    // __proto__
    __proto__: theProtoObj,
    // Shorthand for ‘handler: handler’
    handler,
    // Methods
    toString() {
     // Super calls
     return "d " + super.toString();
    },
    // Computed (dynamic) property names
    [ 'prop_' + (() => 42)() ]: 42
};
```

更多信息: [MDN Grammar and types: Object literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#Object_literals)

### Template Strings
模板字符串为模板化的字符串提供语法糖，这与Perl、Python等语言的字符串插值特性类似。可以添加一个标记，以允许对字符串结构进行自定义，避免注入攻击或从字符串内容构造更高级别的数据结构。

```JavaScript
// 基本的文字字符串创建
`In JavaScript '\n' is a line-feed.`

// 多行字符串
`In JavaScript this is
 not legal.`

// 字符串插入
var name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`

// 构造一个HTTP请求前缀来解释替换和构造
POST`http://foo.org/bar?a=${a}&b=${b}
     Content-Type: application/json
     X-Credentials: ${credentials}
     { "foo": ${foo},
       "bar": ${bar}}`(myOnReadyStateChangeHandler);
```

更多信息: [MDN Template Strings](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/template_strings)

### Destructuring
解构通过使用模式匹配来实现绑定，支持对数组和对象进行匹配。解构是Fail-soft（为了工作可靠而降低性能）, 类似于标准对象查找`foo["bar"]`, 在未找到时产生`undefined`值.

```JavaScript
// 数组匹配
var [a, , b] = [1,2,3];

// 对象匹配
var { op: a, lhs: { op: b }, rhs: c }
       = getASTNode()

// 对象匹配简写
// 将`op`, `lhs` and `rhs`绑定在范围内
var {op, lhs, rhs} = getASTNode()

// 可以出现在函数参数位置
function g({name: x}) {
  console.log(x);
}
g({name: 5})

// Fail-soft解构
var [a] = [];
a === undefined;

// 带默认值的Fail-soft解构
var [a = 1] = [];
a === 1;
```

更多信息: [MDN Destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

### Default + Rest + Spread
被调用函数计算默认参数值。在函数调用中将数组转换为连贯的参数。将尾随的参数绑定定位数组。Rest替换了对于 `arguments`的需求，更直接的解决常见的问题。

```JavaScript
function f(x, y=12) {
  // y is 12 if not passed (or passed as undefined)
  return x + y;
}
f(3) == 15
```
```JavaScript
function f(x, ...y) {
  // y is an Array
  return x * y.length;
}
f(3, "hello", true) == 6
```
```JavaScript
function f(x, y, z) {
  return x + y + z;
}
// Pass each elem of array as argument
f(...[1,2,3]) == 6
```

更多信息: [Default parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters), [Rest parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters), [Spread Operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)

### Let + Const
let 和 const 是具有块级作用域的绑定用构造，let 是新的 var，只在块级作用域内有效，const 是单赋值，声明的是块级作用域的常量。此两种操作符具有静态限制，可以防止出现“在赋值之前使用”的错误。

```JavaScript
function f() {
  {
    let x;
    {
      // okay, block scoped name
      const x = "sneaky";
      // error, const
      x = "foo";
    }
    // error, already declared in block
    let x = "inner";
  }
}
```

More MDN info: [let statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let), [const statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)

### Iterators + For..Of
迭代器对象允许像 [CLI IEnumerable](https://msdn.microsoft.com/zh-cn/library/system.collections.ienumerable(v=vs.110).aspx) 或者 [Java Iterable](https://docs.oracle.com/javase/7/docs/api/java/lang/Iterable.html) 一样自定义迭代器。将for..in转换为自定义的基于迭代器的形如for..of的迭代，不需要实现一个数组，支持像 [LINQ](https://msdn.microsoft.com/zh-cn/library/bb397926.aspx) 一样的惰性设计模式

```JavaScript
let fibonacci = {
  [Symbol.iterator]() {
    let pre = 0, cur = 1;
    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        return { done: false, value: cur }
      }
    }
  }
}

for (var n of fibonacci) {
  // truncate the sequence at 1000
  if (n > 1000)
    break;
  console.log(n);
}
```

迭代器基于这些duck-typed的接口 (此处使用[TypeScript](http://typescriptlang.org) 的类型语法，仅用于阐述问题):
```TypeScript
interface IteratorResult {
  done: boolean;
  value: any;
}
interface Iterator {
  next(): IteratorResult;
}
interface Iterable {
  [Symbol.iterator](): Iterator
}
```

更多信息: [MDN for...of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)

### Generators
生成器通过使用function*和yield简化迭代器的编写， 形如function*的函数声明返回一个生成器实例，生成器是迭代器的子类型，迭代器包括附加的next和throw，这使得值可以回流到生成器中，所以，yield是一个返回或抛出值的表达式形式。

注意：也可以被用作类似‘await’一样的异步编程中，具体细节查看[ES7的await提案](http://wiki.ecmascript.org/doku.php?id=strawman:async_functions)
```JavaScript
var fibonacci = {
  [Symbol.iterator]: function*() {
    var pre = 0, cur = 1;
    for (;;) {
      var temp = pre;
      pre = cur;
      cur += temp;
      yield cur;
    }
  }
}

for (var n of fibonacci) {
  // truncate the sequence at 1000
  if (n > 1000)
    break;
  console.log(n);
}
```
生成器接口如下(此处使用[TypeScript](http://typescriptlang.org)的类型语法，仅用于阐述问题)：

```TypeScript
interface Generator extends Iterator {
    next(value?: any): IteratorResult;
    throw(exception: any);
}
```

更多信息: [MDN Iteration protocols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)

### Unicode
字符串支持新的Unicode文本形式，也增加了新的正则表达式修饰符u来处理码位，同时，新的API可以在21bit码位级别上处理字符串，增加这些支持后可以使用 Javascript 构建全球化的应用。

```JavaScript
// same as ES5.1
"𠮷".length == 2

// new RegExp behaviour, opt-in ‘u’
"𠮷".match(/./u)[0].length == 2

// new form
"\u{20BB7}"=="𠮷"=="\uD842\uDFB7"

// new String ops
"𠮷".codePointAt(0) == 0x20BB7

// for-of iterates code points
for(var c of "𠮷") {
  console.log(c);
}
```

更多信息: [MDN RegExp.prototype.unicode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/unicode)

### Modules
ES6 在语言层面上支持使用模块来进行组件定义，将流行的JavaScript模块加载器（AMD、CommonJS）中的模式固化到了语言中。运行时行为由宿主定义的默认加载器定义，隐式异步模型 - 直到（全部）请求的模块均可用且经处理后，才会执行（当前模块内的）代码。

```JavaScript
// lib/math.js
export function sum(x, y) {
  return x + y;
}
export var pi = 3.141593;
```
```JavaScript
// app.js
import * as math from "lib/math";
alert("2π = " + math.sum(math.pi, math.pi));
```
```JavaScript
// otherApp.js
import {sum, pi} from "lib/math";
alert("2π = " + sum(pi, pi));
```

额外的新特性，包括export default以及export *：

```JavaScript
// lib/mathplusplus.js
export * from "lib/math";
export var e = 2.71828182846;
export default function(x) {
    return Math.log(x);
}
```
```JavaScript
// app.js
import ln, {pi, e} from "lib/mathplusplus";
alert("2π = " + ln(e)*pi*2);
```

More MDN info: [import statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import), [export statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)

### Module Loaders
模块加载器支持:
- 动态加载
- 状态隔离
- 全局命名空间隔离
- 编译钩子
- 嵌套虚拟化(注: 在模块内调用模块)

默认的模块加载器是可配置的，也可以构建新的加载器，对在隔离和受限上下文中的代码进行求值和加载。

```JavaScript
// 动态加载 - ‘System’ 是默认的加载器
System.import('lib/math').then(function(m) {
  alert("2π = " + m.sum(m.pi, m.pi));
});

// 创建一个执行沙箱- 新的加载器
var loader = new Loader({
  global: fixup(window) // replace ‘console.log’
});
loader.eval("console.log('hello world!');");

// 直接操作模块缓存
System.get('jquery');
System.set('jquery', Module({$: $})); // 警告：此部分的设计尚未最终定稿
```

### Map + Set + WeakMap + WeakSet
用于实现常见算法的高效数据结构，WeakMaps提供不会泄露的对象键(对象作为键名，而且键名指向对象)索引表 注：所谓的不会泄露，指的是对应的对象可能会被自动回收，回收后WeakMaps自动移除对应的键值对，有助于防止内存泄露。

```JavaScript
// Sets
var s = new Set();
s.add("hello").add("goodbye").add("hello");
s.size === 2;
s.has("hello") === true;

// Maps
var m = new Map();
m.set("hello", 42);
m.set(s, 34);
m.get(s) == 34;

// Weak Maps
var wm = new WeakMap();
wm.set(s, { extra: 42 });
wm.size === undefined

// Weak Sets
var ws = new WeakSet();
ws.add({ data: 42 });
// Because the added object has no other references, it will not be held in the set
```

更多信息: [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map), [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set), [WeakMap](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap), [WeakSet](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakSet)

### Proxies
代理可以创造一个具备宿主对象全部可用行为的对象。可用于拦截、对象虚拟化、日志/分析等。

```JavaScript
// Proxying a normal object
var target = {};
var handler = {
  get: function (receiver, name) {
    return `Hello, ${name}!`;
  }
};

var p = new Proxy(target, handler);
p.world === 'Hello, world!';
```

```JavaScript
// Proxying a function object
var target = function () { return 'I am the target'; };
var handler = {
  apply: function (receiver, ...args) {
    return 'I am the proxy';
  }
};

var p = new Proxy(target, handler);
p() === 'I am the proxy';
```

所有运行时级别的元操作都有对应的陷阱（使得这些操作都可以被代理）：

```JavaScript
var handler =
{
  get:...,
  set:...,
  has:...,
  deleteProperty:...,
  apply:...,
  construct:...,
  getOwnPropertyDescriptor:...,
  defineProperty:...,
  getPrototypeOf:...,
  setPrototypeOf:...,
  enumerate:...,
  ownKeys:...,
  preventExtensions:...,
  isExtensible:...
}
```

更多信息: [MDN Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

### Symbols
符号(Symbol) 能够实现针对对象状态的访问控制，允许使用string(与ES5相同)或symbol作为键来访问属性。符号是一个新的原语类型，可选的description参数可以用于调试——但并不是符号身份的一部分。符号是独一无二的(如同gensym（所产生的符号）)，但不是私有的，因为它们可以通过类似Object.getOwnPropertySymbols的反射特性暴露出来。

```JavaScript
var MyClass = (function() {

  // module scoped symbol
  var key = Symbol("key");

  function MyClass(privateData) {
    this[key] = privateData;
  }

  MyClass.prototype = {
    doStuff: function() {
      ... this[key] ...
    }
  };

  return MyClass;
})();

var c = new MyClass("hello")
c["key"] === undefined
```

更多信息: [MDN Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

### Subclassable Built-ins
在 ES6 中，内建对象，如Array、Date以及DOM元素可以被子类化。

针对名为Ctor的函数，其对应的对象的构造现在分为两个阶段（这两个阶段都使用虚分派）：

- 调用Ctor[@@create]为对象分配空间，并插入特殊的行为
- 在新实例上调用构造函数来进行初始化

已知的@@create符号可以通过Symbol.create来使用，内建对象现在显式暴露它们的@@create。

```JavaScript
// Pseudo-code of Array
class Array {
    constructor(...args) { /* ... */ }
    static [Symbol.create]() {
        // Install special [[DefineOwnProperty]]
        // to magically update 'length'
    }
}

// User code of Array subclass
class MyArray extends Array {
    constructor(...args) { super(...args); }
}

// Two-phase 'new':
// 1) Call @@create to allocate object
// 2) Invoke constructor on new instance
var arr = new MyArray();
arr[1] = 12;
arr.length == 2
```

### Math + Number + String + Array + Object APIs
新加入了许多库，包括核心数学库，进行数组转换的协助函数，字符串 helper，以及用来进行拷贝的Object.assign。

```JavaScript
Number.EPSILON
Number.isInteger(Infinity) // false
Number.isNaN("NaN") // false

Math.acosh(3) // 1.762747174039086
Math.hypot(3, 4) // 5
Math.imul(Math.pow(2, 32) - 1, Math.pow(2, 32) - 2) // 2

"abcde".includes("cd") // true
"abc".repeat(3) // "abcabcabc"

Array.from(document.querySelectorAll('*')) // Returns a real Array
Array.of(1, 2, 3) // Similar to new Array(...), but without special one-arg behavior
[0, 0, 0].fill(7, 1) // [0,7,7]
[1, 2, 3].find(x => x == 3) // 3
[1, 2, 3].findIndex(x => x == 2) // 1
[1, 2, 3, 4, 5].copyWithin(3, 0) // [1, 2, 3, 1, 2]
["a", "b", "c"].entries() // iterator [0, "a"], [1,"b"], [2,"c"]
["a", "b", "c"].keys() // iterator 0, 1, 2
["a", "b", "c"].values() // iterator "a", "b", "c"

Object.assign(Point, { origin: new Point(0,0) })
```

更多信息: [Number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number), [Math](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math), [Array.from](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from), [Array.of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/of), [Array.prototype.copyWithin](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/copyWithin), [Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)

### Binary and Octal Literals
加入对二进制(b)和八进制(o)字面量的支持。

```JavaScript
0b111110111 === 503 // true
0o767 === 503 // true
```

### Promises
Promise是用来进行异步编程的库。Promise是对一个“将来可能会变得可用”的值的第一类表示，Promise被使用在现有的许多JavaScript库中。

```JavaScript
function timeout(duration = 0) {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, duration);
    })
}

var p = timeout(1000).then(() => {
    return timeout(2000);
}).then(() => {
    throw new Error("hmm");
}).catch(err => {
    return Promise.all([timeout(100), timeout(200)]);
})
```

更多信息: [MDN Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

### Reflect API
完整的反射API。此API在对象上暴露了运行时级别的元操作，从效果上来说，这是一个反代理API，并允许调用与代理陷阱中相同的元操作。实现代理非常有用。

```JavaScript
// No sample yet
```

更多信息: [MDN Reflect](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect)

### Tail Calls
（ES6）保证尾部调用时栈不会无限增长，这使得递归算法在面对未作限制的输入时，能够安全地执行。

```JavaScript
function factorial(n, acc = 1) {
    'use strict';
    if (n <= 1) return acc;
    return factorial(n - 1, n * acc);
}

// Stack overflow in most implementations today,
// but safe on arbitrary inputs in ES6
factorial(100000)
```
