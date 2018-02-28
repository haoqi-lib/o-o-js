来看看 JS 对象的基本语法。

## 对象基础知识

一个对象就是一系列相关数据和函数的集合。对象中的数据叫**属性**（ Property ），对象中的函数叫**方法** （ method ）。下面通过例子来看一下它们都是什么样子。

跟 JS 中其他东西一样，创建一个对象也是首先需要初始化一个变量。

```js
var person = {}
```

这样就创建了一个对象，但是是一个空对象。添加一些内容进来

```js
var person = {
  name: ['Bob', 'Smith'],
  age: 32,
  gender: 'male',
  interests: ['music', 'skiing'],
  bio: function() {
    alert(
      this.name[0] +
        ' ' +
        this.name[1] +
        ' is ' +
        this.age +
        ' years old. He likes ' +
        this.interests[0] +
        ' and ' +
        this.interests[1] +
        '.'
    )
  },
  greeting: function() {
    alert("Hi! I'm " + this.name[0] + '.')
  }
}
```

可以用下面的方式对上面的内容进行取值或者执行。

```js
person.name[0]
person.age
person.interests[1]
person.bio()
person.greeting()
```

可以看到对象中现在就有了一些属性和方法了。

也可以这样描述，一个对象中会包含多个成员，每个成员都有一个名字，类似于上面看到的 name 或者 age ，每个名字对应一个值，类似 `['Bob', 'Smith']` 和 32 。我们可以把一个名字加上它对应的值叫做一个**键值对**。对象中，每个键值对之间用逗号隔开。具体形式如下：

```js
var objectName = {
  member1Name: member1Value,
  member2Name: member2Value,
  member3Name: member3Value
}
```

每个值都几乎可以是任意类型。比如上面看到的，有 string ，有 number 还有数组和函数。前四个东西是数据，也就是我们说的属性，后两个是函数，也就是方法。

上面这样的对象就是一个**对象字面值**（ object literal ），因为在创建它的时候就是一点点明确的写成了它的内容。另外我们也可以从 class 实例化得到对象，这个我们后面会看到。

用字面值创建对象是当我们想要把一系列结构化的关联数据进行传递的常见方式。例如把一些数据通过发请求的形式传递到服务器。这时候，发送一个对象，比发送多个值要高效的多，而且对象这种形式可以很方便的用名字来取值，这一点上来说比数组更易于操作。

## 点表示法

上面我们进行对象取值的方式叫做**点表示法** ( Dot Notation ) 。对象名 person 作为一个命名空间开使用--想要对任何东西进行取值都必须先输入它。然后再输入点，然后是要取值的那个成员的名字，或者是要执行的函数的名字等。

例如

```js
person.age
person.interests[1]
person.bio()
```

## 括号表示法

取对象属性的另外一种方法就括号表示法。

```
person.age
```

就等价于

```
person['age']
```

这个形式跟从数组中取值看上去很像。只不过括号中不是索引值，而是成员名。

## 设置对象成员

除了能读取对象成员，我们也能来设置新的对象成员。例如

```js
person.age = 45
person['eyes'] = 'hazel'
person.farewell = function() {
  alert('Bye everybody!')
}
```

可以到终端中测试一下

```js
person.age
person['eyes']
person.farewell()
```

括号表示法有一个非常有用的方式，那就是用来设置成员名。所以可以有下面的形式

```js
person[1 + 1] = 'xxx'
var myDataName = 'height'
person[myDataName] = '1.75m'
```

也就是说方括号内是可以写变量的，求值结果会作为最终的属性名。

### this 的作用

上面代码中，有这么一条

```js
greeting: function() {
  alert('Hi! I\'m ' + this.name.first + '.');
}
```

那这里的 this 是什么呢？ this 是一个 JS 语言的关键字，替代的是当前对象，这里也就是指 `person` 。

### 对象其实无处不在

比如之前使用了

```js
myString.split(',')
```

这里也有点，那么之前的 `myString` 也是一个对象喽？没错，它是 String 类（ class ）的一个实例对象，所以里面有自带的一些方法和属性。

## 参考

* https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Basics
