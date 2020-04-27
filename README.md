# JavaScript继承的多种方式和优缺点


## 原型链继承

```javascript
function Parent() {
  this.name = 'super-wall';
}

Parent.prototype.sayName = function() {
  console.log(this.name);
}

function Child() {}

Child.prototype = new Parent();

var child1 = new Child();

child1.sayName();
```

### 缺点
- 引用类型的属性被所有实例共享

  ```javascript
  function Parent() {
    this.names = ['a', 'b', 'c'];
  }

  function Child() {}

  Child.prototype = new Parent();

  var child1 = new Child();
  var child2 = new Child();

  child2.names.reverse();
  console.log(child1.names); // ['c', 'b', 'a']
  ```
- 创建 Child 实例，无法向 Parent 传递参数


## 借用构造函数(经典继承)

```javascript
function Parent(name) {
  this.name = name;
  this.sayName = function() {
    console.log(this.name);
  }
}

function Child(name) {
  Parent.call(this, name)
}

var child1 = new Child('super-wall');

child1.sayName();
```

### 缺点
- 方法都在构造函数中定义，每次创建实例都会创建一遍方法。

## 组合继承（原型链+借用构造函数）

```javascript
function Parent(name) {
  this.name = name;
}

Parent.prototype.sayName = function() {
  console.log(this.name);
}

function Child(name) {
  Parent.call(this, name)
}

Child.prototype = new Parent();

var child1 = new Child('super-wall');

child1.sayName();
```

### 缺点
- 组合继承最大的缺点是会调用两次父构造函数。Child.prototype 上也会存在一个name属性

## 寄生组合式继承

```javascript
function Parent(name) {
  this.name = name;
}

Parent.prototype.sayName = function() {
  console.log(this.name);
}

function Child(name) {
  Parent.call(this, name)
}

Child.prototype = Object.create(Parent.prototype);

Child.prototype.constructor = Child;

var child1 = new Child('super-wall');

child1.sayName();
```

## Object.create 的 Polyfill

> 用F函数作为中间桥梁。 实现[bind函数](https://github.com/super-wall/implement-bind-call-and-apply#bind)时，其中模拟new操作时，就是利用`Object.create`原理。

```javascript
if (typeof Object.create !== 'function') {
  Object.create = function(proto) {
    // 容错处理省略...
    function F() {}

    F.prototype = proto;

    return new F();
  }
}
```