# Javascript 基础 - 变量和类型

## 一、JavaScript 规定了几种语言类型

按存储类型分类：
基础类型 -> number, string, boolean, undefined, null, symbol
引用类型 -> object（Object, Array, Date, Function, RegExp）

## 二、常用的判断数据类型的方式

1. typeof

```javascript
typeof 1234 === "number";
typeof "" === "string";
typeof true === "boolean";
typeof {} === "object";
typeof Symbol() === "symbol";
typeof undefined === undefined;
typeof null === "object";
typeof function () {} === "function";
```

通常用于判断基础类型, 或者判断一个变量是否存在
比如在 Node 环境, typeof window === undefined

2. instanceof
   语法: object(实例对象) instanceof constructor(构造函数)，检测 constructor.prototype 是否存在于 object 的原型链上。

```javascript
function Car() {}
const car = new Car();
car instance Car === true;
car instance Object === true;
```

注意非 Object 实例的对象

```javascript
const obj = Object.create(null);
obj instanceof Object === false;
```

但是需要注意的是, 不通过构造函数生成的基础类型的值, 是无法通过 instanceof 判断的

```javascript
"xxxx" instanceof String === false; // 值为false
new String("xxxx") instanceof String === true; // 值为true
```

虽然判断数组没有问题，但是 instanceof 判断对象也是有问题的

```javascript
[1, 2] instanceof Array === true;
[1, 2] instanceof Object === true;
new Date() instanceof Object === true; // 可以看出数组或者Date等类型，instanceof的值都是Object, 是因为Object.prototype存在于各个实例的原型链上
Array.prototype.__proto__ === Object.prototype;
Object.prototype.__proto__ === null; // Object.prototype是原型链的顶端
```

而且如果 prototype 被改变, instaceof 的值就会发生变化

```javascript
function Car() {}
const car = new Car();
car instanceof Car === true;
car._proto_ = {}; // car.__proto__ === Car.prototype;
car instanceof Car === false;
```

3. constructor // 具体可以看 Javascript 基础 - 原型和继承篇

```javascript
function cstor(variable) {
  if (variable === null || variable === undefined) {
    return "Null or Undefined";
  }

  var cst = variable.constructor;

  switch (cst) {
    case Number:
      return "Number";
    case String:
      return "String";
    case Boolean:
      return "Boolean";
    case Array:
      return "Array";
    case Object:
      return "Object";
  }
}
```

4. Object.prototype.toString.call(xxx)
   利用 Object 原型链上的 toString 方法, 会把变量转化为类型值, [object, xxxx]
   而其他的 toString 方法没有这个特性，比如 Array.toString, 会把数组转化为字符串, [1,2] => '1,2'

```javascript
Object.prototype.toString.call([]) === "[object Array]";
Object.prototype.toString.call(/^aaa$/g) === "[object RegExp]";
Object.prototype.toString.call(new Date()) === "[object Date]";
```

5. Array.isArray

## 三、JavaScript 中的值类型和引用类型

### 值类型

值类型 key 和 value 是存储在栈内存中的

```javascript
var name = "jozo";
var city = "guangzhou";
var age = 22;
```

![](https://github.com/sunxiuguo/FeCheck/blob/master/assets/stack.png)

值类型的赋值如下

```javascript
var a = 10;
var b = a;

a++;
console.log(a); // 11
console.log(b); // 10
```

![](https://github.com/sunxiuguo/FeCheck/blob/master/assets/normal-set-value.png)

### 引用类型

引用类型在堆内存中存储 value，在栈内存中存储 key 和指向堆内存的地址
类型比较时都是根据在栈内存储的值的，也就是说引用类型的比较，是根据两个对象在堆内存中的地址比较的。

```javascript
var person1 = { name: "jozo" };
var person2 = { name: "xiaom" };
var person3 = { name: "xiaoq" };
```

![](https://github.com/sunxiuguo/FeCheck/blob/master/assets/heap.png)

引用类型的赋值如下

```javascript
var a = {}; // a保存了一个空对象的实例
var b = a; // a和b都指向了这个空对象

a.name = "jozo";
console.log(a.name); // 'jozo'
console.log(b.name); // 'jozo'

b.age = 22;
console.log(b.age); // 22
console.log(a.age); // 22

console.log(a == b); // true
```

![](https://github.com/sunxiuguo/FeCheck/blob/master/assets/object-set-value.png)

### JavaScript 中的堆内存和栈内存

    https://juejin.im/post/5d116a9df265da1bb47d717b

- [ ] JavaScript 对象的底层数据结构是什么 TODO

      https://juejin.im/entry/58eb2b94ac502e006c452632#comment

### Symbol 类型在实际开发中的应用、可手动实现一个简单的 Symbol

1. 作为对象键名, 模拟私有属性

symbol 的 key 是不能通过 for...in 和 Object.keys()来枚举的，它未被包含在对象自身的属性名集合(property names)之中。所以，利用该特性，我们可以把一些不需要对外操作和访问的属性使用 Symbol 来定义。

```js
const key = Symbol();

let obj = {
  [key]: "test_symbol",
  age: 18,
  name: "sxg",
};

console.log(obj); // { age: 18, name: 'sxg', [Symbol()]: 'test_symbol' }

Object.getOwnPropertyNames(obj); // ['age', 'name']

for (let key in obj) {
  console.log(key);
}
// age
// name

JSON.stringify(obj); // {age:18,name: 'sxg'}

// 使用Object的API是可以访问到的
Object.getOwnPropertySymbols(obj); // [Symbol(name)]
```

2. 使用 Symbol 来代替常量

   有时我们会定义一个常量，给它赋予一个唯一值。

   比如 const TYPE_SXG = 'SXG_TYPE_TEST';

   常量少的话还好，如果有太多需要声明，那么命名上可能会非常头疼

   使用 Symbol 我们可以直接

   const TYPE_SXG = Symbol();
   const AGE_SXG = Symbol();

3. 同样的，也可以用来定义类的私有属性/方法

```js
const PASSWORD = Symbol();

class Login {
  constructor(username, password) {
    this.username = username;
    this[PASSWORD] = password;
  }

  checkPassword(pwd) {
    return this[PASSWORD] === pwd;
  }
}
```

4. 全局创建/获取 Symbol, 用于在多个 window 之间使用

```js
const sb1 = Symbol.for("1111");
const sb2 = Symbol.for("1111");
```

- [ ] 模拟实现 Symbol

  https://juejin.im/post/5b1f4c21f265da6e0f70bb19

- [ ] 基本类型对应的内置对象，以及他们之间的装箱拆箱操作

  https://juejin.im/post/5c5011805188252552518470

- [ ] 栈内存和堆内存的垃圾回收

  https://juejin.im/post/5d0706a6f265da1bc23f77a9

- [ ] null 和 undefined 的区别

- [ ] 可能发生隐式类型转换的场景以及转换原则，应如何避免或巧妙应用

- [ ] 出现小数精度丢失的原因，JavaScript 可以存储的最大数字、最大安全数字，JavaScript 处理大数字的方法、避免精度丢失的方法
