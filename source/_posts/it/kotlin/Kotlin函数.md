# Kotlin 函数

### 函数声明

```kotlin
fun double(x: Int): Int{
    return 2 * x
}

```

描述：以fun为**关键字**，后接**函数名**，函数名后使用括号包裹**函数参数**，最后使用“:”+ 数据类型说明**返回类型**。

这里的返回类型采用后缀方式表示，意味着有时会被省略，通常是在能通过上下文推断的时候。

### 函数用法

顶层函数直接调用

```kotlin
val result = double(2)
```

成员函数要先拿到对象实例才能调用

```Kotlin
Sample().foo()
```

#### 函数参数

采用Pascal表示法定义，即 *name: type* ，参数采用逗号分隔，每个参数都须有显式类型：

```kotlin
fun powerOf(number: Int, exponent: Int){
  ......
}
```

#### 默认参数	

参数可以设置默认值，当省略时使用默认值。

<u>解决的问题</u>：

减少方法重载

```Kotlin
//1
fun read(b: Array<Byte>, off: Int = 0, len: Int = b.size){
  ......
}

//2
open class A {
  open fun foo(i: Int = 10){......}
}
class B: A(){
  override fun foo(i: Int){......}
}

//3
fun foo(bar: Int = 0, baz: Int){......}
foo(baz = 1) // bar使用默认值0

//4
fun foo(bar: Int = 0, baz: Int = 1, qux:()->Unit){......}
foo(1){ println("hello") } // baz使用默认值1
foo(){ println("hello") } // bar使用默认值0，baz使用默认值1
```

1. 默认值通过在类型后面的 = 及给出的值来定义；
2. 覆盖方法总是使用与基类型方法相同的默认参数值，当覆盖一个带有默认参数值的方法时，必须从签名中省略默认参数值；
3. 若一个默认参数在一个无默认值的参数之前，那么该默认值只能通过**命名参数**调用来使用；
4. 若最后一个lambda表达式参数从括号外传给函数调用，那么允许默认参数不传值。

且慢，从括号外传递函数作为函数参数？

减少嵌套。

#### 命名参数

调用函数时使用命名的函数参数。

<u>解决的问题</u>：

1. 【可读性】当一个函数有大量参数时，看代码时很难直接看出值当意义所在；
2. 【明确值的传递】当默认参数在前面定义时，后面的值只能通过命名参数传递，否则会混乱，不清楚是赋给默认参数的还是无默认参数的值。

#### 返回Unit的函数

如果一个函数不返回任何有用的值，则其返回类型是Unit，不需要显式定义和返回。

```Kotlin
fun printlnHello(name: String?):Unit{
   if (name == null) println("Hello ${name}") else println("Hi there!")
   return Unit
}
```

相当于Java中的void。

#### 单表达式函数

<u>表达式与语句的区别</u>：

1. 表达式（Expression），能被计算出结果的短语，包括带运算符的表达式、直接量表达式、对象和数组初始化表达式、函数定义表达式、属性访问表达式、函数调用表达式、创建对象表达式。
2. 语句（Statement）：表达式用于产生值，而语句用于make something happen。有些语句是由具有副作用（side-effect）的表达式构成，比如赋值和函数调用表达式、声明新的变量和定义函数时。

单表达式即是一个表达式。

```Kotlin
fun double(x: Int): Int = x * 2 // 带运算符的表达式
fun double(x: Int) = x * 2 //当返回类型可推断时省略返回类型
```

#### 显式返回类型

具有块代码体的函数必须始终显式指定返回类型，除非返回类型是Unit。Kotlin不会推断具有块代码体的函数的返回类型。

#### 可变数量的参数（Varargs）

这跟java的可变形参类似，但kotlin使用`varargs`修饰，而java使用`...`

```Kotlin
fun <T> asList(vararg ts: T): List<T>{
  val result = ArrayList<T>()
  for(t in ts)
  	result.add(t)
  return result
}

val list = asList(1,2,3)

//3
val a = arrayOf(0,9,8)
val ll = asList(1,2,*a,3)
```

注意：

1. 多个形参时，只有一个参数可以使用vararg；
2. 若vararg参数不是最后一个时，可以通过命名参数传值；
3. 若有一数组，希望将其内容传入该函数时，使用**伸展操作符（*）**拉平传入。

#### 中缀表示法

解决的问题：

1. 定义新的运算规则

条件：

1. 成员函数或者扩展函数；
2. 只有一个参数；
3. 使用`infix`修饰fun。

```Kotlin
infix fun Int.x(other: Int): Int = this * other

val result = 1 x 2
```

### 函数作用域

1. 顶层函数：直接值文件顶层声明的函数；
2. 局部函数：声明在函数里面的函数；
3. 成员函数：在类或对象内部定义的函数；
4. 扩展函数：扩展一个类的新功能而无需继承该类或使用装饰者这样的任何设计模式。

### 泛型函数

具备泛型参数的函数

```Kotlin
fun <T> singletonList(item: T): List<T>{
  //......
}
```

### 尾递归函数

```kotlin
tailrec fun findFixPoint(x: Double = 1.0): Double
        = if(x == Math.cos(x)) x else findFixPoint(Math.cos(x))
```

将循环算法改为递归函数形式来写，而无堆栈溢出风险，编译器会对该递归进行优化。

使用限制：函数必须将自身调用作为它执行的最后一个操作。目前只在JVM后端支持。

### Lambda表达式

#### 函数引用

```Kotlin
fun isOdd(x: Int) = x % 2 !=0
fun isOdd(s: String) = s == "brilling" || s == "slithy" || s == "tove"

val numbers = listOf(1,2,3)
println(numbers.filter(::isOdd)) //可以重载
```

`::isOdd` 是函数类型`(Int) -> Boolean` 的一个值，`::`用于将函数作为值进行传递。

使用成员函数或扩展函数进行引用：

`String.() -> CharArray`

这样就限定了传入的函数必须为String 的扩展函数。

#### 高阶函数

将函数用作参数或返回值的函数。

Lock 函数示例：

```kotlin
fun <T> lock(lock: Lock, body: ()->T):T{
  lock.lock()
  try{
    return body()
  }finally{
    lock.unlock()
  }
}

// 调用方式1
fun toBeSynchronized() = sharedresource.operation()
val result = lock(lock, ::toBeSynchronized)

// 调用方式2
val result = lock(lock, {sharedResource.operation()})

// 调用方式3
val result = lock(lock){
  sharedResource.operation()
}
```

Map函数示例

```kotlin 
fun <T,R> List<T>.map)(transform: (T) -> R): List<R>{
  val result = arrayListOf<R>()
  for(item in this)
  	result.add(transform(item))
  return result
}

// 调用1
val doubled = ints.map{ value -> value * 2 }

// 调用2
val doubled = ints.map{ it * 2 }
```

#### Lambda表达式

规则：

1. 总是被大括号包围着；
2. 参数在`->`之前声明（参数类型可以省略）；
3. 函数体在`->`后面；
4. 如果函数的最后一个参数是一个函数，并且传递的是一个lambda表达式，则可以在圆括号之外指定它，以减少嵌套；
5. 如果lambda是调用的唯一参数，则调用中的圆括号可以完全省略；
6. 如果函数的字面值只有一个参数，那么可以它的声明可以省略（连同`->`），其名称为it；
7. 如果lambda表达式的参数未使用，那么可以使用下划线取代其名称（1.1 起）；

```kotlin
map.forEach{ _, value -> prinln("$value!") }
```

### 内联函数

解决的问题：高阶函数带来的运行时效率损失，因为每一个函数都是一个对象，并且会捕获一个闭包，即那些在函数题内会访问到的变量。

使用`inline`修饰符修饰函数，使得编译器不为每个函数创建一个对象，而是进行内联，以lock函数为例，就是生成如下代码：

```Kotlin
lock(l){ foo() }

l.lock()
try{
  foo()
}finally{
  l.unlock()
}
```

当然也可以禁用内联，使用`noinline`修饰符

```Kotlin
inline fun foo(inlined: ()-> Unit, noinline notInlined: ()-> Unit){
  // .....
}
```

#### 非局部返回（位于lambda表达式中，但退出包含它的函数）

在Kotlin中我们可以只使用一个正常的、非限定的`return`来退出一个命名或匿名的函数。这意味着要退出一个lambda表达式，我们必须使用一个标签🏷️，并且在lambda表达式内部🈲️止使用裸`return`，因为lambda表达式不能使包含它的函数返回：

```kotlin
fun foo(){
  ordinaryFunction{
    return // Error，不能使foo在此处返回
  }
}

fun foo(){
  inlineFunction{
    return // Ok, 内联的可以做到
  }
}
```

