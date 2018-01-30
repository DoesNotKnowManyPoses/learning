# learning 一些学习笔记。

__proto__ 并不是语言本身的特性，这是各大厂商具体实现时添加的私有属性，虽然目前很多现代浏览器的 JS 引擎中都提供了这个私有属性，但依旧不建议在生产中使用该属性，避免对环境产生依赖。生产环境中，我们可以使用 Object.getPrototypeOf 方法来获取实例对象的原型，然后再来为原型添加方法/属性。

与函数一样，类也可以使用表达式的形式定义。

const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};
上面代码使用表达式定义了一个类。需要注意的是，这个类的名字是MyClass而不是Me，Me只在 Class 的内部代码可用，指代当前类。

let inst = new MyClass();
inst.getClassName() // Me
Me.name // ReferenceError: Me is not defined

采用 Class 表达式，可以写出立即执行的 Class。

let person = new class {
  constructor(name) {
    this.name = name;
  }

  sayName() {
    console.log(this.name);
  }
}('张三');

person.sayName(); // "张三"

类不存在变量提升（hoist），这一点与 ES5 完全不同。

new Foo(); // ReferenceError
class Foo {}

{
  let Foo = class {};
  class Bar extends Foo {
  }
}
上面的代码不会报错，因为Bar继承Foo的时候，Foo已经有定义了。但是，如果存在class的提升，上面代码就会报错，因为class会被提升到代码头部，
而let命令是不提升的，所以导致Bar继承Foo的时候，Foo还没有定义。

还有一种方法是利用Symbol值的唯一性，将私有方法的名字命名为一个Symbol值。

const bar = Symbol('bar');
const snaf = Symbol('snaf');

export default class myClass{

  // 公有方法
  foo(baz) {
    this[bar](baz);
  }

  // 私有方法
  [bar](baz) {
    return this[snaf] = baz;
  }

  // ...
};
上面代码中，bar和snaf都是Symbol值，导致第三方无法获取到它们，因此达到了私有方法和私有属性的效果。

一个比较简单的解决方法是，在构造方法中绑定this，这样就不会找不到print方法了。

class Logger {
  constructor() {
    this.printName = this.printName.bind(this);
  }

  // ...
}
另一种解决方法是使用箭头函数。

class Logger {
  constructor() {
    this.printName = (name = 'there') => {
      this.print(`Hello ${name}`);
    };
  }

  // ...
}

由于本质上，ES6 的类只是 ES5 的构造函数的一层包装，所以函数的许多特性都被Class继承，包括name属性。

class Point {}
Point.name // "Point"
name属性总是返回紧跟在class关键字后面的类名。

由于本质上，ES6 的类只是 ES5 的构造函数的一层包装，所以函数的许多特性都被Class继承，包括name属性。

class Point {}
Point.name // "Point"
name属性总是返回紧跟在class关键字后面的类名。







