---
title: golang的反射机制与实践（下）
tags: 反射
categories: golang
abbrlink: 42411
date: 2019-06-13 07:07:33
---
![](https://upload-images.jianshu.io/upload_images/15072499-8cbc0a3af2305f9d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上篇说了下反射该怎么用，现在我们来看一看使用反射的实际情况，深入理解一下

这篇因为是实践篇，所以有大量的代码示例来进行演示，**因为只是演示反射的使用，所以对一些复杂的错误机制没做处理**

反射本身并不难，看懂了上一章反射到底是干嘛用的，什么时候用，这一章其实非常好懂

**说到底就是将`reflect`包提供给我们的方法，进行一些组合使用罢了**，说土一点就是调用下API

没看上篇的可以先看看[golang的反射与实践（上）]()

## 反射的实践操作

好了，咱们开始进行实践

先把我们的准备工作做好，先定义一个`Struct`，再给这个结构体加上一些方法
```go
// Employee 员工
type Employee struct {
	Name string `json:"emp_name"`
	Age  int    `json:"emp_age"`
	Sex  int
}

// GetSum 返回两数之和
func (e *Employee) GetSum(n1, n2 int) int {
	return n1 + n2
}

// Set 接受值，给结构体e赋值
func (e *Employee) Set(name string, age, sex int) {
	e.Name = name
	e.Age = age
	e.Sex = sex
}

// Print 打印结构体*Employee 
func (e *Employee) Print() {
	log.Print("======Start======")
	log.Print(e)
	log.Print("======End======")
}
```

随便给这个结构体写了几个方法，我们主要是看，我们如何使用反射在运行时对变量进行一个操作

## 使用反射来遍历结构体的字段值，并获取结构体的tag标签

先来看个常规用法

```go
// GetStruct 获取结构体的字段及tag
func GetStruct(i interface{}) {
	rType := reflect.TypeOf(i)
	rVal := reflect.ValueOf(i)

	kd := rVal.Kind()

	// 如果是传进来的是指针类型
	// 则获取指针值
	if kd == reflect.Ptr {
		rType = rType.Elem()
		rVal = rVal.Elem()
		kd = rVal.Kind()
	}

	if kd != reflect.Struct {
		log.Panicf("Kind is %v not struct ", kd)
	}
	// 获取结构体的字段数
	sNum := rVal.NumField()
	log.Printf("Struct has %v fields ", sNum)
	// 遍历结构体的所有字段
	for i := 0; i < sNum; i++ {
		log.Printf("Field %d value is %v", i, rVal.Field(i))
		// 获取Struct的tag，使用Type类型获取
		tag := rType.Field(i).Tag.Get("json")
		if tag == "" {
			log.Printf("Field %d hasn't tag  %v ", i, tag)
			continue
		}
		log.Printf("Field %d tag is %v ", i, tag)
	}
}
```
我们定义一个方法`GetStruct(i interface{})`，因为入参是`interface{}`类型，所以这个方法可以接收并处理所有的数据类型。这就是反射的牛逼之处了

遗憾的是，反射的性能比较低。后面咱们对性能进行分析时再拿出来聊聊

测试用例如下

```go
func TestGetStruct(t *testing.T) {
	emp := &Employee{}
	emp.Set("闹闹", 99, 0)
	GetStruct(emp)
}
```

执行结果如下图所示

![](http://ww1.sinaimg.cn/large/007OROpSgy1g3v341z04cj30c905ujr6.jpg)

这个函数接受的参数是`interface`，也就是说，通过这个函数，不管入参传递了什么样的结构体，我们可以知道这个结构体有什么标签，有几个方法

获取`tag`标签的用处就是对我们的结构体进行序列化时使用，将结构体的字段名变成我们需要的别名

可以参考下`encoding/json`包的使用方式

## 获取并调用结构体的方法


```go
// CallMethod 调用结构体方法
// i : 传入的struct
// methodByName : 调用结构体的方法名
func CallMethod(i interface{}, methodByName string) {
	rVal := reflect.ValueOf(i)
	rType := reflect.TypeOf(i)
	log.Printf("Type is %v Kind is %v", rType, rType.Kind())

	// 获取结构体有多少个方法
	numOfMethod := rVal.NumMethod()
	log.Printf("Struct has %d method", numOfMethod)
	// 声明Value数组
	var params []reflect.Value
	// 声明一个Value类型，用于接收方法
	var method reflect.Value

	if methodByName == "GetSum" {
		// 调用方法时的参数
		params = append(params, reflect.ValueOf(10))
		params = append(params, reflect.ValueOf(88))

	}
	if methodByName == "Set" {
		// 调用方法时的参数
		params = append(params, reflect.ValueOf("闹闹吃鱼"))
		params = append(params, reflect.ValueOf(18))
		params = append(params, reflect.ValueOf(0))
	}
	// 获取方法
	method = rVal.MethodByName(methodByName)
	if !method.IsValid() {
		// 如果结构体不存在此方法，输出Panic
		log.Panic("Method is invalid")
	}
	result := method.Call(params)
	if len(result) > 0 {
		// 如果函数存在返回值，则打印第一条
		log.Println("Call result is ", result[0])
	}
}
```

这里值得注意一点的就是，我们通过反射的`Call`去调用函数，传入的参数的类型是`reflect.Value`类型，并不是我们定义函数时的`int`类型

所以在调用函数时传入的参数需要进行一个类型转换

给你们附上测试用例，你们可以自己调试跑跑，会发现，不管你传的结构体的字段是什么，我都进行统一处理了
```go
func TestCallMethod(t *testing.T) {
	emp := &Employee{}
	emp.Set("闹闹", 99, 0)
	emp.Print()
	CallMethod(emp, "Set")
	emp.Print()
}
```

## 修改字段值
```go
// ModifyField 修改字段值
func ModifyField(i interface{}, filedName string) {
	rVal := reflect.ValueOf(i)
	filed := rVal.Elem().FieldByName(filedName)
	if !filed.IsValid() {
		log.Panic("filedName is invalid")
	}
	filed.SetString("闹闹")
}
```

运行时修改结构体的字段，主要就是做到一个通用性，比如上述的例子，不管是什么结构体

依然附上测试用例
```go
func TestModifyField(t *testing.T) {
	emp := &Employee{}
	ModifyField(emp, "Name")
}
```

不管传入的结构体是什么，只要包含了`filedName`（我们指定的字段名），我们就可以对其进行值的更改

假如我们有100个结构体，需要对`name`字段进行修改，通过反射的机制，我们代码的耦合度将大大的降低

## 定义适配器，用作统一处理接口
```go
// Bridge 适配器
// 可以实现调用任意函数
func Bridge(call interface{}, args ...interface{}) {
	var (
		function reflect.Value
		inValue  []reflect.Value
	)
	n := len(args)
	// 将参数转换为Value类型
	inValue = make([]reflect.Value, n)
	for i := 0; i < n; i++ {
		inValue[i] = reflect.ValueOf(args[i])
	}
	// 获得函数的Value类型
	function = reflect.ValueOf(call)
	// 传参，调用函数
	function.Call(inValue)
}
```

写了个测试用例，函数是我们在调用`Bridge`前就已经定义好了

```go
func TestBridge(t *testing.T) {
	call1 := func(v1, v2 int) {
		log.Println(v1, v2)
	}
	call2 := func(v1, v2 int, str string) {
		log.Println(v1, v2, str)
	}

	Bridge(call1, 1, 2)
	Bridge(call2, 2, 3, "callTest")
}

```

两个函数是不同的函数，但是都可以通过`Bridge`进行执行

适配器有什么用呢？如果不知道的童鞋，可以去看看设计模式「适配器模式」

因为本篇幅只是说如何在实战中应用反射，所以这里就不讲解设计模式了

## 使用反射创建，并操作结构体
```go
// CreateStruct 使用反射创建结构体
// 并给结构体赋值
func CreateStruct(i interface{}) *Employee {
	var (
		structType  reflect.Type
		structValue reflect.Value
	)
	// 获取传入结构体指向的Type类型
	structType = reflect.TypeOf(i).Elem()
	// 创建一个结构体
	// structValue持有一个指向类型为Type的新申请的指针
	structValue = reflect.New(structType)
	// 转换成我们要创建的结构体
	modle := structValue.Interface().(*Employee)
	// 取得structValue指向的值
	structValue = structValue.Elem()
	// 给结构体赋值
	structValue.FieldByName("Name").SetString("闹闹吃鱼")
	structValue.FieldByName("Age").SetInt(100)
	return modle
}
```

使用方式就看看测试用例

```go
func TestCreateStruct(t *testing.T) {
	emp := &Employee{
		Name: "NaoNao",
		Age:  18,
		Sex:  1,
	}
	emp.Print()// &{NaoNao 18 1}
	newEmp := CreateStruct(emp)
	newEmp.Print()// &{闹闹吃鱼 100 0}
}
```


可能你会问，`CreateStruct`的入参不是`interface{}`吗？可为什么我传一个任意的结构体，却要给返回一个指定的结构体呢？就不能我传什么结构体进去，就返回什么结构体出来吗？

理想总是丰满的，现实却是非常骨感，虽然我们反射的方法实现，都是将入参写为`interface{}`，但**使用反射并不是意味着我们一定就写了一个万能的程序**

还记得上一篇提到的，`变量`与`reflect.Value`之间该如何转换吗？

咱们再复习一下：`变量<------>interface{}<------>reflect.Value`

我们**不管是把`Value`转为结构体，还是转为基本类型，我们都需要在编译前确定转换后的类型**

换句话说，只要我们在运行时牵扯到类型的转换，我们都需要各种`if`来判断是否能转换成我们需要的类型

本文以大量的代码实现来阐述反射该怎么用，说实话，挺无聊的

写这篇文章的目的就是让你拿电脑上去编译跑跑，或者什么时候想到要用反射了，可以拿出来瞅瞅，看看什么地方需要用到反射，反射又可以干什么

![image](https://upload-images.jianshu.io/upload_images/15072499-63e24bbd8340aa7a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 微信扫码关注公众号「闹闹吃鱼」，还可领取Go语言学习大礼包，入门到进阶不再了无头绪