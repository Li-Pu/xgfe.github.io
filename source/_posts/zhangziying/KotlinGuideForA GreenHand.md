title: Kotlin初探(Java视角)
date: 2020.6.17 16:01:00
updated: 2020.6.17 16:01:00
categories:
- zhangziying03
tags:
- Android
- Kotlin
- Mobile
---
## 简介
Kotlin凭什么被称为 "better Java"？本文从Java使用者的角度入门Kotlin，看看Kotlin好处都有啥。
<!--more-->
## Kotlin是什么
### Kotlin和Java的关系：
Kotlin兼容Java，可以直接调用Java代码，因此一切Java的库也适用于Kotlin。Kotlin可以编译成JVM字节码也可以编译成JavaScript方便在没有 JVM 的设备上运行。Java能跑的地方Kotlin也能跑，Kotlin可以看做Java的扩展/优化。
在语法糖的加持下Kotlin相比Java可以写出更简洁的代码。Kotlin在无缝兼容Java的基础上还拥有一些其他特性，比如协程、空类型检查等特性。
### Kotlin的社会地位：
Kotlin 在2017年被Google宣布为Android官方开发语言。
## 基本语法
## 变量
Kotlin中一切都是对象。可以采用统一的方式进行声明。
### 声明
Kotlin中可以使用var或val对变量进行定义，其中var定义可变变量，val定义不可变变量。
```Kotlin
//Kotlin中不需要分号了哦
var <标识符> : <类型> = <初始化值>
val <标识符> : <类型> = <初始化值>
```
### null检测
Java中虽然有@Nullable @NotNull等注解,但是加上这些注解后仅仅会有IDE警告，提示处理null情况。Kotlin编译器则可以强制处理null情况，最大限度避免null问题。Kotlin在声明变量的时候，可以指定变量是否为空，调用可为空对象时，需要进行空判断。
在Kotlin的null检查机制中，使用!!以及?进行null检测。其中加上!!后则变量不可为空，否则会NPE(NullPointerException)，而?则可以为空。
在声明变量的时候，在类型后面加上相应的符号：
```Kotlin
var h: String? = "helloworld" 
```
在使用变量的时候对于可空的变量可以直接抛出异常或者进行相应的处理或者不作处理。
```Kotlin
var h: String? = "1" 
//!!抛出空指针异常
val ages = age!!.toInt()
//不做处理返回 null
val ages1 = age?.toInt()
//age为空返回-1 等价于 ages2 = age==null? -1: age;
val ages2 = age?.toInt() ?: -1
```
### 比较与类型判断
在 Kotlin 中，三个等号 === 表示比较对象地址，两个 == 表示比较两个值大小。
```Kotlin
var c:Int = 999
val a:Int? = c
val b:Int? = c
a===b //false 需要注意两点 1、使用？会进行自动装箱，是Int对象。2、若c在-127到128之间则结果为true 因为会使用IntegerCache里缓存的数据而不是装箱成新对象。
a==b //true
```
类型判断也十分简便，使用 is 进行判断：
```Kotlin
if(a is Int) { //todo }
```
## 循环相关
### 区间遍历
对于一个区间的遍历，Kotlin提供了Java相比更为简约的写法：
```Kotlin
//区间可以通过..进行连接，如A..B表示[A,B] 还可以通过 until 进行连接,比如 A until B 表示[A,B)
for(i in 区间) 
//还可以通过downTo从大往小取 
for(i in 5 downTo 1)
//也可以设定步长，比如每两个数取一次
for(i in 5 downTo 1 step 2)
```
### 更简洁的switch：when
在Kotlin中可以使用when来替代Java中的switch：
```Kotlin
when (x) {
    1,3 -> print("1 OR 3")
    2 -> print("3")
    in 4..10 -> print("in [4,10]")//可以判断区间
    is Int -> print("is Int") // 还可以判断类型
    else -> { // 除非覆盖所有情况，否则必须写
        print("err")
    }
}
```
## 类与对象
### 构造函数
Kotlin对象支持多个构造函数，其中分为主构造函数和次构造函数。主构造函数的参数放在类的头，具体代码放在类内的 init{} 代码段中。次构造函数直接在类内使用constructor进行声明：
```Kotlin
//若主构造函数没有任何注解或者可见性修饰符(public、protected、private、internal)，则 constructor 可以省略
class Demo construcotr(name: String) {   
        //主构造函数
   init {
             // TODO  
      }    
    //次构造函数
   construcotr(name: String, key: String){
   //TODO
   }
}
```
其他内容与Java相似度较大，不再展开。
## 协程
首先，协程并不是Kotlin特有的，它已经有十几年的历史了，go Javascript python等语言都支持协程。简单的来说，协程就是轻量级的线程。协程完全由程序控制，因此协程的切换不设计用户态和内核态的转变消耗相对于线程切换更低。一个进程中可以有多个线程，而一个线程中也可以有多个协程。对于同一个线程里面的协程，它们之间的执行是串行的。因此协程序适合于io密集型的程序。
### 基本使用
**launch**
一个最简单协程的创建用launch加上大括号括起来的代码即可，返回Job,可以用来对协程进行取消等操作：  
```Kotlin
fun main() {
    var job = launch{
        //todo
    //使用delay可以挂起协程
    delay(1000L)
    }
    job.cancel()
    job.join()
}
```
**runBlocking**
runBlocking启动的协程任务会阻断当前线程，直到该协程执行结束：
```Kotlin
fun main() {
    runBlocking {     
        delay(2000L)  
    } 
}
```
也可以用runBlocking来包装一个函数：
```Kotlin
fun test() = runBlocking<T> { 
    //todo
}
```
**async/await**
async方法的返回值类型是Deferred, 是Job的子类, Deferred里有个await方法, 调用它可得到协程的返回值。
```Kotlin
fun test() = runBlocking<T> { 
    var deferred = async{ //todo}
    //输出结果
    print(deferred.await())
}
```
### 其他概念&参数
上下文：协程运行的上下文包括Job以及调度器等
协程调度器：决定协程运行的线程情况，比如指定线程或者不设限
***GlobalScope&CoroutineScope***
GlobalScope继承自CoroutineScope。当一个协程被其它协程在 CoroutineScope中启动的时候则会继承父协程的上下文。当使用 GlobalScope来启动一个协程时，则新协程的作业没有父作业。 因此它与这个启动的作用域无关且独立运作。
可以在创建协程的时候使用，比如：
```Kotlin
var job = GlobalScope.launch{
        //todo
    //使用delay可以挂起协程
    delay(1000L)
}
```
也可以直接创建一个作用域，然后在里面创建协程：
```Kotlin
GlobalScope{
        launch{
            //todo
    }
}
```
***调度器***
```Kotlin
launch(Dispatchers.Default) { // 将会获取默认调度器
   //todo
}
launch(newSingleThreadContext("MyOwnThread")) { // 将使它获得一个新的线程
   //todo
}
```
