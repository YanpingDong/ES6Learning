# JS零碎知识

## 无返回或无明确返回

　　如果函数无返回值，那么可以调用没有参数的 return 运算符，随时退出函数。那么返回的值到底是什么呢？
```
function sayHi(sMessage) {
  if (sMessage == "bye") {
    return;
  }

  alert(sMessage);
}
```
　　这段代码中，如果 sMessage 等于 "bye"，就永远不显示警告框。

**NOTE：如果函数无明确的返回值，或调用了没有参数的 return 语句，那么它真正返回的值是 undefined。**

## 函数参数

　　可以向函数传递超过或者少于定义的个数，也可以通过arguments特殊对象来访问传入函数中的变量。

示例代码：[argumentsDemo](SimpleDemo\functionArgumentsDemo.html)

> 与其他程序设计语言不同，ECMAScript 不会验证传递给函数的参数个数是否等于函数定义的参数个数。开发者定义的函数都可以接受任意个数的参数（根据 Netscape 的文档，最多可接受 255 个），而不会引发任何错误。任何遗漏的参数都会以 undefined 传递给函数，多余的函数将忽略。

也可以利用arguments来模拟函数重载，示例: [模拟函数重载](overLoadSimulationDemo.html)

## 函数名只是变量

　　因为函数名是变量，所以才可以很容易的把函数作为参数传递给另一个函数。

　　记得下面这个函数吗？

```
function sayHi(sName, sMessage) {
  alert("Hello " + sName + sMessage);
}
```

　　还可以这样定义它：

```
var sayHi = new Function("sName", "sMessage", "alert(\"Hello \" + sName + sMessage);");
```

　　虽然由于字符串的关系，这种形式写起来有些困难，但有助于理解函数只不过是一种引用类型，它们的行为与用 Function 类明确创建的函数行为是相同的。**即在用functionp定义sayHi这个函数的时候就相当于已经声名了一个名为sayHi的变量且指向该函数**，请看下面这个例子：

```
function doAdd(iNum) {
  alert(iNum + 20);
}

function doAdd(iNum) {
  alert(iNum + 10);
}

doAdd(10);	//输出 "20"
```

　　如你所知，第二个函数重载了第一个函数，使 doAdd(10) 输出了 "20"，而不是 "30"。
　　如果以下面的形式重写该代码块，这个概念就清楚了：

```
var doAdd = new Function("iNum", "alert(iNum + 20)");
var doAdd = new Function("iNum", "alert(iNum + 10)");
doAdd(10);
```

　　请观察这段代码，很显然，doAdd 的值被改成了指向不同对象的指针。**函数名只是指向函数对象的引用值**，行为就像其他对象一样。甚至可以使两个变量指向同一个函数：

```
var doAdd = new Function("iNum", "alert(iNum + 10)");
var alsodoAdd = doAdd;
doAdd(10);	//输出 "20"
alsodoAdd(10);	//输出 "20"
```

　　下面示例展示了将一个函数传递给另一个函数

```
function callAnotherFunc(fnFunction, vArgument) {
  fnFunction(vArgument);
}

var doAdd = new Function("iNum", "alert(iNum + 10)");

callAnotherFunc(doAdd, 10);	//输出 "20"
```

## 对象

　　ECMA-262 把对象（object）定义为“属性的无序集合，每个属性存放一个原始值、对象或函数”。严格来说，这意味着对象是无特定顺序的值的数组。
　　尽管 ECMAScript 如此定义对象，但它更通用的定义是基于代码的名词（人、地点或事物）的表示。

## 面向对象语言的要求

　　一种面向对象语言需要向开发者提供四种基本能力：

1. 封装 - 把相关的信息（无论数据或方法）存储在对象中的能力
2. 聚集 - 把一个对象存储在另一个对象内的能力
3. 继承 - 由另一个类（或多个类）得来类的属性和方法的能力
4. 多态 - 编写能以多种方法运行的函数或方法的能力
　　ECMAScript 支持这些要求，因此可被是看做面向对象的。

## 对象废除

　　ECMAScript 拥有无用存储单元收集程序（garbage collection routine），意味着不必专门销毁对象来释放内存。当再没有对对象的引用时，称该对象被废除（dereference）了。运行无用存储单元收集程序时，所有废除的对象都被销毁。每当函数执行完它的代码，无用存储单元收集程序都会运行，释放所有的局部变量，还有在一些其他不可预知的情况下，无用存储单元收集程序也会运行。

　　把对象的所有引用都设置为 null，可以强制性地废除对象。例如：

```
var oObject = new Object;
// do something with the object here
oObject = null;
```

　　当变量 oObject 设置为 null 后，对第一个创建的对象的引用就不存在了。这意味着下次运行无用存储单元收集程序时，该对象将被销毁。

　　每用完一个对象后，就将其废除，来释放内存，这是个好习惯。这样还确保不再使用已经不能访问的对象，从而防止程序设计错误的出现。此外，旧的浏览器（如 IE/MAC）没有全面的无用存储单元收集程序，所以在卸载页面时，对象可能不能被正确销毁。废除对象和它的所有特性是确保内存使用正确的最好方法。

**注意：废除对象的所有引用时要当心。如果一个对象有两个或更多引用，则要正确废除该对象，必须将其所有引用都设置为 null。**

## 早绑定和晚绑定

　　早绑定early binding：在编译的时候就已经却确定了将来程序运行基类或者派生类的哪个方法.在编译代码的时候根据引用类型就决定了运行该引用类型中定义的方法。即基类方法，这种方式运行效率高。

　　晚绑定late binding:只有在运行的时候才能决定运行基类或者派生类中的方法。运行的时候将根据该实际类型,而不是引用类型来调用相应的方法.即取决于我们new了什么对象。

　　ECMAScript 中的所有变量都采用晚绑定方法。

　　早绑定的优点是: 编译效率  代码提示(代码智能感知)  编译时类型检查。

　　晚绑定的优点是: 不用申明类型  对象类型可以随时更改。

## 内置对象

　　ECMA-262 把内置对象（built-in object）定义为“由 ECMAScript 实现提供的、独立于宿主环境的所有对象，在 ECMAScript 程序开始执行时出现”。这意味着开发者不必明确实例化内置对象，它已被实例化了。ECMA-262 只定义了两个内置对象，即 Global 和 Math （它们也是本地对象，根据定义，每个内置对象都是本地对象）。

## 宿主对象

　　所有非本地对象都是宿主对象（host object），即由 ECMAScript 实现的宿主环境提供的对象。所有 BOM 和 DOM 对象都是宿主对象。

## ECMAScript 只有公用作用域

　　对 ECMAScript 讨论上面这些作用域几乎毫无意义，因为 ECMAScript 中只存在一种作用域 - 公用作用域。ECMAScript 中的所有对象的所有属性和方法都是公用的。因此，定义自己的类和对象时，必须格外小心。记住，所有属性和方法默认都是公用的！

　　**建议性的解决方法：**

　　许多开发者都在网上提出了有效的属性作用域模式，解决了 ECMAScript 的这种问题。

　　由于缺少私有作用域，开发者确定了一个规约，说明哪些属性和方法应该被看做私有的。这种规约规定在属性前后加下划线：

`obj._color_ = "blue";`

　　这段代码中，属性 color 是私有的。*注意，下划线并不改变属性是公用属性的事实，它只是告诉其他开发者，应该把该属性看作私有的。*

　　有些开发者还喜欢用单下划线说明私有成员，例如：`obj._color`。

## ECMAScript 没有静态作用域

　　严格来说，ECMAScript 并没有静态作用域。不过，它可以给构造函数提供属性和方法。还记得吗，构造函数只是函数。函数是对象，对象可以有属性和方法。例如：

```
function sayHello() {
  alert("hello");
}

sayHello.alternate = function() {
  alert("hi");
}

sayHello();		//输出 "hello"
sayHello.alternate();	//输出 "hi"
```

## 关键字 this

**this 的功能**

　　在 ECMAScript 中，要掌握的最重要的概念之一是关键字 this 的用法，它用在对象的方法中。关键字 this 总是指向调用该方法的对象，例如：
```
var oCar = new Object;
oCar.color = "red";
oCar.showColor = function() {
  alert(this.color);
};

oCar.showColor();		//输出 "red"
```
　　在上面的代码中，关键字 this 用在对象的 showColor() 方法中。在此环境中，this 等于 oCar。下面的代码与上面的代码的功能相同：

```
var oCar = new Object;
oCar.color = "red";
oCar.showColor = function() {
  alert(oCar.color);
};

oCar.showColor();		//输出 "red"
```

　　即this指代的什么是根据 **运行时此函数在什么对象上被调用（或运行时此函数在什么上下文环境中视调用）**， 如果在全局作用范围内使用this，则指代当前页面对象window； 如果在函数中使用this则是调用该函数的对象。

**使用 this 的原因**

　　为什么使用 this 呢？因为在实例化对象时，总是不能确定开发者会使用什么样的变量名。使用 this，即可在任何多个地方重用同一个函数。即不同的对象调用同样的方法可以用this传入不同的状态。请思考下面的例子：

```
function showColor() {
  alert(this.color);
};

var oCar1 = new Object;
oCar1.color = "red";
oCar1.showColor = showColor;

var oCar2 = new Object;
oCar2.color = "blue";
oCar2.showColor = showColor;

oCar1.showColor();		//输出 "red"
oCar2.showColor();		//输出 "blue"
```

　　在上面的代码中，首先用 this 定义函数 showColor()，然后创建两个对象（oCar1 和 oCar2），一个对象的 color 属性被设置为 "red"，另一个对象的 color 属性被设置为 "blue"。两个对象都被赋予了属性 showColor，指向原始的 showColor () 函数（注意这里不存在命名问题，因为一个是全局函数，而另一个是对象的属性）。调用每个对象的 showColor()，oCar1 输出是 "red"，而 oCar2 的输出是 "blue"。这是因为调用 oCar1.showColor() 时，函数中的 this 关键字等于 oCar1。调用 oCar2.showColor() 时，函数中的 this 关键字等于 oCar2。

　　注意，引用对象的属性时，必须使用 this 关键字。例如，如果采用下面的代码，showColor() 方法不能运行：

```
function showColor() {
  alert(color);
};
```

　　**如果不用对象或 this 关键字引用变量，ECMAScript 就会把它看作局部变量或全局变量。然后该函数将查找名为 color 的局部或全局变量，但是不会找到。结果如何呢？该函数将在警告中显示 "null"。**