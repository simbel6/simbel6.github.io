---
title: JavaScript 中的继承
categories:
  - 前端
  - JavaScript
tags:
  - JavaScript
  - 继承
abbrlink: '77044630'
---

## JavaScript 中的继承

### 1. 原型链继承

原型链的问题：

* 原型中包含的引用值会在所有实例间共享
* 子类型在实例化时不能给父类构造函数传参

```js
function SuperType() {
  this.property = true;
  this.colors = ["red", "blue"];
}

SuperType.prototype.getSuperValue = function () {
  return this.property;
};

function SubType() {
  this.subproperty = false;
}

// 继承 SuperType
SubType.prototype = new SuperType();

// 恢复constructor属性
// 这种方式会创建一个[[Enumerable]] 为 true 的属性，而原生 constructor 属性默认是不可枚举的
// SubType.prototype.constructor = SubType

// 使用 Object.defineProperty()方法来定义 constructor 属性
Object.defineProperty(SubType.prototype, "constructor", {
  enumerable: false,
  value: SubType,
});

// 新方法
SubType.prototype.getSubValue = function () {
  return this.subproperty;
};

// 覆盖已有方法
SubType.prototype.getSuperValue = function () {
  return false;
};

let instance1 = new SubType();
instance1.colors.push("black");
console.log("instance1.colors", instance1.colors);

let instance2 = new SubType();
console.log("instance2.colors", instance2.colors);

console.log(SubType.prototype.constructor == SubType);
```

### 2. 盗用构造函数 / 对象伪装 / 经典继承

优点：

* 可以在子类构造函数中向父类构造函数传参； 
* 每个实力都可以有自己的属性

缺点：

* 必须在构造函数中定义方法，因此函数不能重用
* 子类也不能访问父类原型上定义的方法

```js
function SuperType(name) {
  this.colors = ["red", "blue"];
  this.name = name;
  // this.getColors = function () {
  //   return this.colors
  // }
}

SuperType.prototype.getColors = function () {
  return this.colors
}

function SubType(sub_name) {
  SuperType.call(this, sub_name);
  this.age = 29;
}

let instance1 = new SubType("James");
instance1.colors.push("yellow");
console.log(instance1.colors)
console.log(instance1.name);
console.log(instance1.age);

let instance2 = new SubType("Kobe");
console.log(instance2.colors);
// console.log(instance2.getColors())  // 访问父类原型上的方法会报错
```

### 3. 组合继承 / 伪经典继承

组合继承综合了原型链和盗用构造函数，将两者的优点集中起来。
基本思路是使用原型链继承原型上的属性和方法，而通过构造函数继承实例属性，
这样既可以把方法定义在原型上以实现重用，又可以让每个实例都有自己的属性

缺点： 父类构造函数会执行两次

```js
function SuperType(name) {
  this.colors = ["red", "blue"];
  this.name = name;
}

SuperType.prototype.getName = function () {
  return this.name;
};

function SubType(name, age) {
  // 继承属性
  SuperType.call(this, name);
  this.age = age;
}

// 继承方法
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType

SubType.prototype.getAge = function () {
  return this.age;
};

let instance1 = new SubType("James", 31);
instance1.colors.push("yellow");
console.log(instance1.colors);
console.log(instance1.getName());
console.log(instance1.getAge());

let instance2 = new SubType("Kobe", 30);
console.log(instance2.colors);
console.log(instance2.getAge());
console.log(instance2.getName());

console.log(SubType.prototype.constructor == SubType)
```

### 4. 原型式继承

原型式继承适合不需要单独创建构造函数，但仍然需要在对象间共享信息的场合。
属性中包含的引用值会始终在对象间共享

ES5 增加了 `Object.create()` 方法将原型式继承规范化了

```js
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}

let person = {
  name: "Nick",
  friends: ["Jane", "Aimee", "Tina"],
};

let anotherPerson = object(person);
anotherPerson.name = "Jay";
anotherPerson.friends.push("Jeff");

let yetAnother = object(person);
yetAnother.name = "Andy";
yetAnother.friends.push("Lee");

let thirdPerson = Object.create(person, {
    name: {
        value: 'Oli'
    }
})
thirdPerson.friends.push('Nash')

console.log(thirdPerson.name)
console.log(person.friends);
```

### 5. 寄生式继承

寄生式继承背后的思路类似于寄生构造函数和工厂模式：

创建一个实现继承的函数，以某种方式增强对象，然后返回这个对象

```js
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}

// 参数original 是新对象的基准对象
function createAnthoer(original) {
  let clone = object(original);
  clone.sayHi = function () {
    console.log("Hi");
  };
  return clone;
}

let person = {
  name: "Jack",
  friends: ["Robin", "Andy"],
};

let anthoerPerson = createAnthoer(person);
anthoerPerson.sayHi();
```

### 6. 寄生式组合继承

所谓寄生组合式继承，即通过借用构造函数来继承属性，通过原型链的混成形式来继承方法

基本思路： 不必为了指定子类型的原型而调用超类型的构造函数，我们需要的仅是超类型原型的一个副本，

本质上就是使用寄生式继承来继承超类型的原型，然后再将结果指定给子类型的原型

```js
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}

function inheritPrototype(subType, superType) {
  let prototype = object(superType.prototype); // 创建对象
  prototype.constructor = subType; // 增强对象
  subType.prototype = prototype; // 赋值对象
}

function SuperType(name) {
  this.name = name;
  this.colors = ["red", "blue"];
}

SuperType.prototype.sayName = function () {
  console.log(this.name);
};

function SubType(name, age) {
  SuperType.call(this, name);
  this.age = age;
}

inheritPrototype(SubType, SuperType);

SubType.prototype.sayAge = function () {
  console.log(this.age);
};

let sub = new SubType("Kobe", 35);
sub.sayName();
sub.sayAge();
console.log(sub.colors);
console.log(sub instanceof SuperType);
console.log(SubType.prototype.isPrototypeOf(sub));
console.log(SuperType.prototype.isPrototypeOf(sub));
console.log(Object.prototype.isPrototypeOf(sub));
```