---
title: golang的反射机制与实践（上）
abbrlink: 22954
date: 2019-06-09 03:16:43
tags: 反射
categories: golang
---
![](http://ww1.sinaimg.cn/large/007OROpSgy1g3xzl0lw59j30hn0dgdl9.jpg)


反射机制是一个很重要的内容，当我们写框架的时候，要想要松耦合，高复用，那么就有很多地方都需要用到反射，可谓是中高级程序员必须掌握的知识点

很多后台语言都有反射机制，但它们的使用原理大多都是一样的

各语言不同的地方，大致就是代码实现方式不一致罢了

**其根本，都是从变量得到反射对象，再由反射对象去操作原变量**

好了，步入正题

## 什么是反射

我就用一句话来概括吧

**使用反射，可以让我们在程序运行时对任意类型的对象进行操作**

注意操作这两个字，操作是指：可以获取对象的信息、改变对象的值、调用对象的方法、甚至是创建一个对象

说到这你可能有点困惑，我们在编写代码的时候不就已经把该实例化的象进行了实例化，该调用的方法都调用了嘛？为什么写程序的时候不调用方法，偏要在运行时去进行这些操作？

其实问题就在这里，如果我们在写程序的时候，一切的对象与方法都能够确定了，那还要反射做什么？

正是因为我们在写程序的时候，要想写一些“万能程序”，用于降低代码的耦合度，所以我们才需要反射，用于处理一些未知的对象

想想，当我们写一个方法，不管别人往我们这个方法内传入什么样的参数，最后我们的函数都能给别人所需要的内容。是不是感觉很牛逼？

## 反射的使用原理

我这里主要说使用反射的原理，并不是刨析反射的底层原理，有兴趣想要探索原理的读者大人，可以去看看go的reflect包源码

先给你们上个图，看懂这个关系图，后面的文字基本也就可以不看了

![](https://user-gold-cdn.xitu.io/2019/6/12/16b48b7578b23a34?w=614&h=415&f=jpeg&s=97836)


没看懂没关系，稍微解释就能明白~~

我们定义的一个变量，不管是基本类型`int`，还是一个结构体`Employee`，我们都可以通过`reflect.TypeOf()`获取他的反射类型`Type`，也可以通过`reflect.ValueOf()`去获取他的反射值`Value`

**我们学习反射，其实就是学习如何使用原变量，去取得`reflect.Type`或者`reflect.Value`这种反射对象；再使用这个反射对象`Type`以及`Value`，反过来对原变量进行操作**

弄明白了这个道理，那一切都将变得简单

剩下的，我们只是需要去学习`reflect`包中提供的方法。当我们需要要怎么操作变量，就使用其提供的对应方法即可

## 反射的注意事项与细节

#### `Type`与`Kind`的区别是什么？

`Type`是类型，`Kind`是类别，听起来有点绕，**他们之间的关系为`Type`是`Kind`的子集**

如果变量是基本类型，那么`Type`与`Kind`得到的结果是一致的，比如变量为`int`类型，`Type`与`Kind`的值相等，都为`int`

但当变量为结构体时，`Type`与`Kind`的值就不一样了

我们来看个实际案例
```go
func main() {
	var emp Employee
	emp = Employee{
		Name: "naonao",
		Age:  99,
	}
	rVal := reflect.ValueOf(emp)
	log.Printf("Kind is %v ,Type is %v",
		rVal.Kind(),
		rVal.Type())
	// Kind is struct ,Type is main.Employee
}
```

可以看到，`Kind`的值是`struct`,而`Type`的值是`包名.Employee`

#### 反射如何在变量与`reflect.Value`之间切换？

变量可以转换成`interface{}`之后，再转换成`reflect.Value`类型，既然空接口可以转换成`Value`类型，那么自然也可以反过来转换成变量

用个表达式来表示，就如下所示

> 变量<----->interface{}<----->reflect.Value

利用空接口来进行中转，这样`变量`与`Value`之间就可以实现互相转换了

下面我们再说如何用代码实现转换

#### 如何使用反射获取变量本身的值？

这里我们要注意一下，`reflect.ValueOf()`得到的值是`reflect.Value`类型，并不是变量本身的值

```go
var num = 1
rVal := reflect.ValueOf(num)
log.Printf("num is %v", num + rVal)
```

这段代码会报错`invalid operation: num + rVal (mismatched types int and reflect.Value)`

很明显，`rVal`是属于`reflect.Value`类型，不能与`int`类型相加

那怎样才能获得它本身的值呢？

**如果是基本类型**，比如`var num int`,那么使用`reflect`包里提供的转换方法即可`reflect.ValueOf(num).Int()`

或者是`float`，那就调用`reflect.ValueOf(num).float()`，如果是其它的基本类型，需要的时候去文档里面找找即可

但**如果是我们自己定义的结构体**，因为`reflect`包无法确定我们自己定义了什么结构体，所以本身并不会带有结构体转换的方法，那么我们只能**通过类型断言来进行转换**

**也就是上面说的，利用空接口进行中转，再利用断言进行类型转换**，可以看如下代码示例

```go
// Employee 员工
type Employee struct {
	Name string
	Age  int
}

func main() {
	emp := &Employee{
		Name: "naonao",
		Age:  99,
	}
	reflectPrint(emp)
}

func reflectPrint(v interface{}) {
	rVal := reflect.ValueOf(v)   // 获取reflect.Value
	iV := rVal.Interface()       // 利用空接口进行中转
	empVal, ok := iV.(*Employee) // 利用断言转换
	if ok {
		// 如果成功转换则打印结构体
		log.Print(empVal)
	}
}

```
这里我只是进行了一个简单的判断，如果想要进行完整的判断，还是需要借助`swith`语句，下篇会提到。也可以参照`reflect`包的单元测试文件

#### 通过反射来修改变量

先来看看代码如何实现

```go
func main() {
	var num = 1
	modifyValue(&num)// 传递地址
	log.Printf("num is %v", num)// num is 20
}

func modifyValue(i interface{}) {
	rVal := reflect.ValueOf(i)
	rVal.Elem().SetInt(20)
}
```

细心的你肯定发现了一点异常，函数接收的参数不再是值了，而是接受了一个指针地址

改变值的时候，先调用了`Elem()`方法，再进行了一个`SetInt()`的操作

为什么直接传值不行呢？**因为`reflect`包中提供的所有修改变量值的方法，都是对指针进行的操作**

那为什么还要先使用`Elem()`呢？因为`Elem()`的作用，就是取得指针地址所对应的值，取到值了，我们才能对值进行修改

总不可能连值都没拿到手，就想着去改值吧？

#### 如何理解reflect.Value.Elem()

关于`Elem()`的使用可以简单的理解为
```go
num := 1
prt *int := &num // 获取num的指针地址
num2 := *ptr // 从指针处取值
```

因为我们传递了一个地址，所以我们要先拿到这个地址的指针，再通过指针去取得所对应的值

`reflect`包底层实现就是基于这个原理，不过它的底层代码加了较多的判断，用来保证稳定性  

## 写在最后

这篇先说些基础概念，下篇我们再从实践出发，看看在什么地方需要使用反射，又该如何使用`reflect`包提供的方法去实现

![](http://ww1.sinaimg.cn/large/007OROpSgy1g5pbxlmfqbj306008f0t1.jpg)

## 微信扫码关注公众号「闹闹吃鱼」，每周都有好分享