---
title: 面向对象的JavaScript
date: 2019-07-07 16:05:00
categories:
- JavaScript设计模式与开发实践
tags:
- 动态类型语言和鸭子类型
- 多态
- 封装
- 原型模式和基于原型继承的JavaScript
---

# 面向对象的JavaScript

## 动态类型语言和鸭子类型
>
静态类型语言：在编译时已确定变量的类型
动态类型语言：在程序运行时，待变量被赋予某个值之后才具有某种类型

JavaScript是一种典型的动态类型语言

动态类型的语言给变量类型的宽容给实际编码带来了很大的灵活性，由于**无需进行类型检测**，我们可以*尝试调用任何对象的任意方法*，而无需考虑他原本是否被设计为拥有该方法。

### 鸭子类型
通俗说法：如果它走起路来向鸭子，叫起来也是鸭子，那么它就是鸭子。

鸭子类型指导我们：只关心对象的行为，而不关注对象本身，也就是关注HAS-A，而不是IS-A。

```JavaScript
var duck = {
  duckSinging() {
    console.log("嘎嘎嘎");
  }
}
var chicken = {
  duckSinging() {
    consle.log("嘎嘎嘎");
  }
}
var choir = []; // 合唱团
var joinChoir = animal => {
  if( animal && typeof animal.duckSinging === "function") {
    choir.push(animal);
    console.log("恭喜加入合唱团");
    console.log("合唱团已有成员数量："+choir.length)
  }
}
joinChoir(duck); // 恭喜加入合唱团
joinChoir(chicken); // 恭喜加入合唱团
```
利用鸭子类型的思想，我们不必借助超类型的帮助，就能轻松地在动态类型语言中实现一个原则：“**面向接口编程**，而不是面向实现编程”。**面向接口编程**是设计模式中最重要的思想。

## 多态

多态：polymorphism, poly(复数)+morph(形态)+ism，从字面上可以理解为复数形态。

>
多态的实际含义：同一个操作作用于不同的对象上面，可以产生不同的解释和不同的执行结果。换句话说，给不同的对象发送同一个消息的时候，这些对象会根据这个消息分别给出不同的反馈。

多态背后的思想试讲“做什么”和“谁去做以及怎么做“分离开来，也就是将”不变的事物“与”可能改变的事物“分离开来。把不变的部分隔离出来，把可变的部分封装起来，这给与我们扩展程序的能力，程序看起来是可生长的，也是符合开放-封闭原则的。

### 对象的多态性
```JavaScript
// 不变的部分隔离出来，那就是虽有的动物都会叫
var makeSound = animal => animal.sound();
// 把可变的部分封装起来
var Duck = function(){}
Duck.prototype.sound = () => console.log("嘎嘎嘎");
var Chicken = function(){}
Chicken.prototype.sound = () => console.log("咯咯咯");
makeSound( new Duck() ); // 嘎嘎嘎
makeSound( new Chicken() ) // 咯咯咯
// 如果增加狗，只要追加代码，不用改动原来makeSound函数
var Dog = function(){}
Dog.prototype.sound = () => {consle.log("汪汪汪")};
makeSound( new Dog() ); // 汪汪汪
```

### 类型检查和多态
静态语言在编译时或进行类型匹配检测，所以不能给变量赋不同类型的值。即接受Duck类型的对象时不可以接受Chicken类型的对象。
静态类型的面对对象语言通常被设计为可以****向上转型**，Duck对象和Chicken对象的类型都被隐藏在超类型Animal身后，Duck对象和Chicken对象就能被交换使用。

### 使用继承得到多态效果
继承通产包括实现继承和接口继承，本节讨论实现继承

### JavaScript的多态
多态的思想实际上是把“做什么”和“谁去做”分离开，实现这一点，归根接地要先消除类型之间的耦合关系。而JavaScript的变量类型在运行期间可变，意味着JavaScript对象的动态性是与生俱来的。

### 多态在面向对象程序设计中的作用

Martin Fowler在《重构：改善既有代码的设计》里写到：
>
多态的最根本好处在于，你不必再向对象询问“你是什么类型”而后根据得到的答案调用对象的某个行为---你只需要调用该行为就是了，其他的一切多态机制都会为你安排妥当。

**多态最根本的作用就是把过程化的条件分支语句转化为对象的多态性。**


Martin Fowler 的话可以用下面这个例子很好地诠释：

在电影的拍摄现场，当导演喊出“action”时，主角开始背台词，照明师负责打灯光，后面的群众演员假装中枪倒地，道具师往镜头里撒上雪花。在得到同一个消息时，每个对象都知道自己应该做什么。如果不利用对象的多态性，而是用面向过程的方式来编写这一段代码，那么相当于在电影开始拍摄之后，导演每次都要走到每个人的面前，确认它们的职业分工（类型），然后告诉他们要做什么。如果映射到程中，那么程序中将充斥着条件分支语句。

利用对象的多态性，导演在发布消息时，就不必考虑各个对象接收到消息后应该这么做。对象应该这么做并不是临时决定的，而是已经实现约定和排练完毕的，每个对象该做什么，已经成为了该对象的一个方法，被安排在对象的内部，每个队形负责他们自己的行为。所有这些对象可以根据同一个消息，有条不紊的分别进行各自的工作。

将行为分布在各个对象中，并让这些对象各自负责自己的行为，这正是面向对象设计的优点。

### 设计模式与多态
绝大部分设计模式的实现都离不开多态性的思想。

在JavaScript中函数本身也是对象，函数用来封装行为并且能够被四处传递，很多设计模式在JavaScript中可以用高阶函数来代替实现的原因。

## 封装
封装的目的试讲信息隐藏，一般而言，我们讨论的是封装数据和封装实现，更广义的封装还包括封装类型和封装变化。

### 封装数据
在许多语言的对象系统中，封装数据是由语法解析来实现的，提供了private、public、protected等关键字来提供不同的访问权限。

JavaScript并没有对这些关键字的支持，只能依赖变量的作用于来实现封装特性，而且只能模拟出public和private两种封装性。
除了ES6提供的let，一般我们通过函数来创建作用域：
```JavaScript
var myObject = (function(){
  var _name = "yue"; // private变量
  return {
    getName: function() { // public方法
      return _name;
    }
  }
})();
console.log(myObject.getName()); // yue
console.log(myObject._name); // undefined
```
在ES6中，还可以通过`Symbol`创建私有属性。

### 封装实现

有时候我们喜欢把封装等同于封装数据，但这是一种比较狭义的定义。封装的目的是将信息隐藏，封装应该被视为“任何形式的封装”，封装不仅仅是隐藏数据，还包括隐藏实现细节、设计细节和隐藏对象的类型等。

封装实现使得对象内部的变化对其他对象而言是透明的，也就是不可见的。对象对他自己的行为负责，其他对象或者用户都不关心他的内部实现。封装是的对象之间的耦合变松散，对象之间只通过暴露的API接口来通信。当我们修改一个对象时，可以随意修改他的内部实现，只要对外的接口没有变化，就不会影响程序的其他功能。

### 封装类型
JavaScript中，并没有对抽象类和接口的支持，在封装类型方面，JavaScript没有能力也没有必要做的更多。

### 封装变化

《设计模式》一书曾提到如下文字：
>
“考虑你的设计中哪些地方可能变化，这种方式与关注会导致重新设计的原因相反。它不是考虑什么时候会迫使你的设计改变，而是考虑你怎样才能够在不重新设计的情况下进行改变。这里的关键在于封装发生变化的概念，这是许多设计模式的主题。”

即“找到变化并封装之”。

## 原型模式和基于原型继承的JavaScript对象系统
在以类为中心的面向对象编程语言汇总，类和对象的关系可以想象成铸模和逐渐的关系，对象总是从类中创建而来。
在原型编程的思想中，类不是必须的，对象未必需要从类中创建而来，一个对象时通过克隆另外一个对象所得到的。

**第一个设计模式---原型模式**

### 使用克隆的原型模式
原型模式是用于创建对象的一种模式，我们不再关心对象的具体实现，而是找到一个对象，通过克隆来创建一个一模一样的对象。

如果我们需要一个跟某个对象一模一样的对象，就可以使用原型模式。

ES5提供了Object.create方法，用来克隆对象。

```JavaScript
Object.create = Object.create || obj => {
  var F = function(){};
  F.prototype = obj;
  return new F();
}
```

### 克隆是创建对象的手段

原型模式的真正目的并非在于得到一个一模一样的对象，而是提供了一种便捷的方式去创建摸个类型的对象，克隆只是创建这个对象的过程和手段。

### 体验Io语言

### 原型编程泛型的一些规则
Io和JavaScript一样，基于原型链的委托机制就是原型继承的本质。原型编程的一个重要特性，即当对象无法响应某个请求是，会把该请求委托给它自己的原型。

原型编程泛型至少包括以下基本原则：
* 所有的数据都是对象
* 要得到一个对象，不是通过实例化类，而是找到一个对象作为原型并克隆它
* 对象会记住它的原型
* 如果对象无法响应某个请求，它会把这个请求委托给它自己的原型

### JavaScript中的原型继承

#### 1.所有的数据都是对象
按照JavaScript设计者的本意，除了undefined，一切都应使对象，为了实现这一目标，number、boolean、string这几种基本类型也可以通过“包装类”的方式变成数据类型数据来处理。

JavaScript中一定会有一个根对象存在，这些对象追根溯源都来源于这个根对象。

JavaScript中的根对象是`Object.prototype`对象。

```JavaScript
var obj1 = new Object();
var obj2 = {};
console.log( Object.getPrototypeOf(obj1) === Object.prototype) // true
console.log( Object.getPrototypeOf(obj2) === Object.prototype) // true
```

#### 2.要得到一个对象，不是通过实例化类，而是找到一个对象作为原型并克隆它

在JavaScript语言中，我们并不需要关心克隆的细节，因为这是引擎内部负责实现的。我们只需显式的调用`var obj1 = new Object()` 或者 `var obj2 = {}`。

用`new`运算符从构造器中得到一个对象
```JavaScript
function Person(name) {
  this.name = name;
};

Person.prototype.getName = function() {
  return this.name;
};
var a = new Person("yue");
console.log(a.name); // yue
console.log(a.getName()); // yue
console.log(Object.getPrototypeOf(a) === Person.prototype) // true
```
在这里Preson并不是类，而是函数构造器，JavaScript的函数既可以作为普通函数被调用，也可以作为构造器被调用。当使用`new`运算符来调用函数是，此时函数就是一个构造器。实际上也是先克隆`Object.prototype`对象，在进行其他额外操作。

我们可以通过下面这段代码来理解`new`运算过程

```JavaScript
function Person(name) {
  this.name = name;
};

Person.prototype.getName = function() {
  return this.name;
};

var objectFactory = function() {
  // 从Object.prototype上克隆一个空对象
  var obj = new Object(),
  // 取得外部传入的构造器，此例是Person
     Constructor = [].shift.call(arguments);
  obj._proto_ = Constructor.prototype; //指向正确的原型
  var ret = Constructor.apply(obj, arguments); // 借用外部传入的构造器给obj设置属性
  return typeof ret === "object" ? ret : obj; // 确保构造器总是返回一个对象
};

var a = objectFactory(Person, "yue");

console.log(a.name); // yue
// 按照书应该输出yue,但在chrome会报错，a.getName is not a function ,因为a.prototype === Obj.prototype
console.log(a.getName());
console.log(Object.getPrototypeOf(a) === Person.prototype); // 应该输出true，实际输出false
```

#### 3.对象会记住它的原型

>
就JavaScript的真正实现来说，其实并不能说对象有原型，而只能说对象的构造器有原型。因此，对于“对象吧请求委托给他自己的原型”这句话，更好的说法是“对象把请求委托给他的构造器的原型”。

JavaScript给对象提供了一个名为`_proto_`的隐藏属性，会指向它的构造器的原型对象，即{Constructor}.prototype。

#### 4.如果对象无法响应某个请求，它会把这个请求委托个它的构造器的原型

### 原型继承的未来

Peter Norvig 说过，设计模式是对语言不足的补充，如果要使用设计模式，不如去找一门更好的语言。
