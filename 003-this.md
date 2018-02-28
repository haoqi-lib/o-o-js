this 的动态切换，固然为 JavaScript 创造了巨大的灵活性，但也使得编程变得困难和模糊。有时，需要把 this 固定下来，避免出现意想不到的情况。JavaScript 提供了 call、apply、bind 这三个方法，来切换/固定 this 的指向。

## this 是什么？

不管是什么场合，this 都有一个共同点：它总是返回一个对象。

由于对象的属性可以赋给另一个对象，所以属性所在的当前对象是可变的，即 this 的指向是可变的。

```js
var A = {
  name: '张三',
  describe: function() {
    return '姓名：' + this.name
  }
}

var B = {
  name: '李四'
}

B.describe = A.describe
B.describe()
// "姓名：李四"
```

上面代码中，A.describe 属性被赋给 B，于是 B.describe 就表示 describe 方法所在的当前对象是 B，所以 this.name 就指向 B.name。

稍稍重构这个例子，this 的动态指向就能看得更清楚。

```js
function f() {
  return '姓名：' + this.name
}

var A = {
  name: '张三',
  describe: f
}

var B = {
  name: '李四',
  describe: f
}

A.describe() // "姓名：张三"
B.describe() // "姓名：李四"
```

上面代码中，函数 f 内部使用了 this 关键字，随着 f 所在的对象不同，this 的指向也不同。

只要函数被赋给另一个变量，this 的指向就会变。

```js
var A = {
  name: '张三',
  describe: function() {
    return '姓名：' + this.name
  }
}

var name = '李四'
var f = A.describe
f() // "姓名：李四"
```

## Function.prototype.call()

函数实例的 call 方法，可以指定函数内部 this 的指向（即函数执行时所在的作用域），然后在所指定的作用域中，调用该函数。

```js
var obj = {}

var f = function() {
  return this
}

f() === window // true
f.call(obj) === obj // true
```

上面代码中，全局环境运行函数 f 时，this 指向全局环境（浏览器为 window 对象）；call 方法可以改变 this 的指向，指定 this 指向对象 obj，然后在对象 obj 的作用域中运行函数 f。

call 方法的参数，应该是一个对象。如果参数为空、null 和 undefined，则默认传入全局对象。

```js
var n = 123
var obj = { n: 456 }

function a() {
  console.log(this.n)
}

a.call() // 123
a.call(null) // 123
a.call(undefined) // 123
a.call(window) // 123
a.call(obj) // 456
```

上面代码中，a 函数中的 this 关键字，如果指向全局对象，返回结果为 123。如果使用 call 方法将 this 关键字指向 obj 对象，返回结果为 456。可以看到，如果 call 方法没有参数，或者参数为 null 或 undefined，则等同于指向全局对象。

如果 call 方法的参数是一个原始值，那么这个原始值会自动转成对应的包装对象，然后传入 call 方法。

```js
var f = function() {
  return this
}

f.call(5)
// Number {[[PrimitiveValue]]: 5}
```

上面代码中，call 的参数为 5，不是对象，会被自动转成包装对象（Number 的实例），绑定 f 内部的 this。

call 方法还可以接受多个参数。

```js
func.call(thisValue, arg1, arg2, ...)
```

call 的第一个参数就是 this 所要指向的那个对象，后面的参数则是函数调用时所需的参数。

```js
function add(a, b) {
  return a + b
}

add.call(this, 1, 2) // 3
```

上面代码中，call 方法指定函数 add 内部的 this 绑定当前环境（对象），并且参数为 1 和 2，因此函数 add 运行后得到 3。

## Function.prototype.apply()

apply 方法的作用与 call 方法类似，也是改变 this 指向，然后再调用该函数。唯一的区别就是，它接收一个数组作为函数执行时的参数，使用格式如下。

```js
func.apply(thisValue, [arg1, arg2, ...])
```

apply 方法的第一个参数也是 this 所要指向的那个对象，如果设为 null 或 undefined，则等同于指定全局对象。第二个参数则是一个数组，该数组的所有成员依次作为参数，传入原函数。原函数的参数，在 call 方法中必须一个个添加，但是在 apply 方法中，必须以数组形式添加。

```js
function f(x, y) {
  console.log(x + y)
}

f.call(null, 1, 1) // 2
f.apply(null, [1, 1]) // 2
```

上面代码中，f 函数本来接受两个参数，使用 apply 方法以后，就变成可以接受一个数组作为参数。

利用这一点，可以做一些有趣的应用。

## Function.prototype.bind()

bind 方法用于将函数体内的 this 绑定到某个对象，然后返回一个新函数。

```js
var d = new Date()
d.getTime() // 1481869925657

var print = d.getTime
print() // Uncaught TypeError: this is not a Date object.
```

上面代码中，我们将 d.getTime 方法赋给变量 print，然后调用 print 就报错了。这是因为 getTime 方法内部的 this，绑定 Date 对象的实例，赋给变量 print 以后，内部的 this 已经不指向 Date 对象的实例了。

bind 方法可以解决这个问题。

```js
var print = d.getTime.bind(d)
print() // 1481869925657
```

上面代码中，bind 方法将 getTime 方法内部的 this 绑定到 d 对象，这时就可以安全地将这个方法赋值给其他变量了。

bind 方法的参数就是所要绑定 this 的对象，下面是一个更清晰的例子。

```js
var counter = {
  count: 0,
  inc: function() {
    this.count++
  }
}

var func = counter.inc.bind(counter)
func()
counter.count // 1
```

上面代码中，counter.inc 方法被赋值给变量 func。这时必须用 bind 方法将 inc 内部的 this，绑定到 counter，否则就会出错。

this 绑定到其他对象也是可以的。

```js
var counter = {
  count: 0,
  inc: function() {
    this.count++
  }
}

var obj = {
  count: 100
}
var func = counter.inc.bind(obj)
func()
obj.count // 101
```

上面代码中，bind 方法将 inc 方法内部的 this，绑定到 obj 对象。结果调用 func 函数以后，递增的就是 obj 内部的 count 属性。

bind 还可以接受更多的参数，将这些参数绑定原函数的参数。

```js
var add = function(x, y) {
  return x * this.m + y * this.n
}

var obj = {
  m: 2,
  n: 2
}

var newAdd = add.bind(obj, 5)
newAdd(5) // 20
```

上面代码中，bind 方法除了绑定 this 对象，还将 add 函数的第一个参数 x 绑定成 5，然后返回一个新函数 newAdd，这个函数只要再接受一个参数 y 就能运行了。

## 参考

本节是我对下面文章的摘抄。

* http://javascript.ruanyifeng.com/oop/this.html
