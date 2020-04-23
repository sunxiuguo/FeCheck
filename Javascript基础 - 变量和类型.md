# Javascript 基础 - 变量和类型

### JavaScript 规定了几种语言类型

```javascript
按存储类型分类：
基础类型 -> number, string, boolean, undefined, null, symbol
引用类型 -> object（Object, Array, Date, Function, RegExp）
```

### 常用的判断数据类型的方式

```javascript
1. typeof
typeof 1234 === 'number'
typeof '' === 'string'
typeof true === 'boolean'
typeof {} === 'object'
typeof Symbol() === 'symbol'
typeof undefined === undefined
typeof null === 'object'
typeof function() {} === 'function'

通常用于判断基础类型, 或者判断一个变量是否存在
比如在Node环境, typeof window === undefined

2. instanceof
语法:  object(实例对象) instanceof constructor(构造函数)，检测constructor.prototype是否存在于object的原型链上。
function Car() {}
const car = new Car();
car instance Car === true;
car instance Object === true;

注意非Object实例的对象
const obj = Object.create(null);
obj instanceof Object === false;

但是需要注意的是, 不通过构造函数生成的基础类型的值, 是无法通过instanceof判断的
'xxxx' instanceof String === false; // 值为false
new String('xxxx') instanceof String === true; // 值为true

虽然判断数组没有问题，但是instanceof判断对象也是有问题的
[1,2] instanceof Array === true;
[1,2] instanceof Object === true;
new Date() instanceof Object === true; // 可以看出数组或者Date等类型，instanceof的值都是Object, 是因为Object.prototype存在于各个实例的原型链上
Array.prototype.__proto__ === Object.prototype
Object.prototype.__proto__ === null // Object.prototype是原型链的顶端

而且如果prototype被改变, instaceof的值就会发生变化
function Car(){}
const car = new Car();
car instanceof Car === true;
car._proto_ = {}; // car.__proto__ === Car.prototype;
car instanceof Car === false;

3. constructor // 具体可以看Javascript基础 - 原型和继承篇
function cstor(variable) {
    if (variable === null || variable === undefined) {
        return 'Null or Undefined';
    }

    var cst = variable.constructor;

    switch (cst) {
        case Number:
            return 'Number'
        case String:
            return 'String'
        case Boolean:
            return 'Boolean'
        case Array:
            return 'Array'
        case Object:
            return 'Object'
    }
}

4. Object.prototype.toString.call(xxx)
利用Object原型链上的toString方法, 会把变量转化为类型值, [object, xxxx]
而其他的toString方法没有这个特性，比如Array.toString, 会把数组转化为字符串, [1,2] => '1,2'

Object.prototype.toString.call([]) === '[object Array]'
Object.prototype.toString.call(/^aaa$/g) === '[object RegExp]'
Object.prototype.toString.call(new Date) === '[object Date]'

5. Array.isArray
```

### 值类型和引用类型在存储上有什么区别？

值类型 key 和 value 是存储在栈内存中的

```javascript
var name = "jozo";
var city = "guangzhou";
var age = 22;
```

![](https://images2015.cnblogs.com/blog/1103385/201702/1103385-20170212104752057-1066946645.png)

引用类型在堆内存中存储 value，在栈内存中存储 key 和指向堆内存的地址

```javascript
var person1 = { name: "jozo" };
var person2 = { name: "xiaom" };
var person3 = { name: "xiaoq" };
```

![](https://images2015.cnblogs.com/blog/1103385/201702/1103385-20170212104829307-1264699054.png)

- [ ] 分别都是存储在哪？？有什么区别？

- [ ] JavaScript 对象的底层数据结构是什么

- [ ] Symbol 类型在实际开发中的应用、可手动实现一个简单的 Symbol

- [ ] JavaScript 中的变量在内存中的具体存储形式

- [ ] 基本类型对应的内置对象，以及他们之间的装箱拆箱操作

- [ ] 理解值类型和引用类型

- [ ] null 和 undefined 的区别

- [ ] 可能发生隐式类型转换的场景以及转换原则，应如何避免或巧妙应用

- [ ] 出现小数精度丢失的原因，JavaScript 可以存储的最大数字、最大安全数字，JavaScript 处理大数字的方法、避免精度丢失的方法
