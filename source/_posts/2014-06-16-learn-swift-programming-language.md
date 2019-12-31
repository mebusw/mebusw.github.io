title: Apple最新Swift编程语言之闭包
tags:
  - ios
  - playground
  - python
  - swift
  - xcode
  - lambda
  - clojure
  - functional programming

categories:
    - programming
    - swift

---

![](http://tse2.mm.bing.net/th?id=OIP.M41605c49a5ba1abb7f5230c1400ad351o0&pid=15.1)

Swift作为苹果为开发者最新发布的编程语言，受到热烈的追捧。与2014世界杯同热。

作为一门动态语言，吸收了大量Python语言的特性和语法（我喜欢Python），当然也有些ruby、js和C#的痕迹，同时保持了对原objective-C库的兼容。

iBook推出了教程，[中文翻译](http://gg.swiftguide.cn/)也完成了。

大家需要下载最新xcode，里面有交互式的playground可以用来学习swift。为了获得最好的体验，在 Xcode 当中使用代码预览功能。代码预览功能可以让你编辑代码并实时看到运行结果。 [打开Playground](https://github.com/Tairraos/the-swift-programming-language-in-chinese/blob/gh-pages/chapter1/GuidedTour.playground.zip?raw=true)

# 你需要知道Swift语言闭包函数 ()->()

## 定义一个函数

你可以用`func`关键字来定义函数。函数可以接收和返回0个、1个或多个参数（tuples列表）。返回类型在`->`符号后面。

``` swift
func jediGreet(name: String, ability: String) -> (farewell: String, mayTheForceBeWithYou: String) {
  return ("Good bye, \(name).", " May the \(ability) be with you.")
}

```

## 调用函数

``` swift
let retValue = jediGreet("old friend", "Force")
println(retValue)
println(retValue.farewell)
println(retValue.mayTheForceBeWithYou)

```

## 函数类型


``` swift
func sum(x: Int, y: Int) -> (result: Int) { return x + y }
```
上述函数的函数类型为 `(Int, Int) -> (Int)`。
函数类型可以用于嵌套函数的参数类型或者返回类型。


## 传递和返回函数

下列函数将另一个函数作为结果返回，可以用于赋值给变量及调用。

``` swift
func jediTrainer () -> ((String, Int) -> String) {
  func train(name: String, times: Int) -> (String) {
    return "\(name) has been trained in the Force \(times) times"
  }
  return train
}
let train = jediTrainer()
train("Obi Wan", 3)

```


## 可变入参函数

可变入参函数带有可变数量的入参（表示为参数类型后的`...`），其内容可作为数组来访问。

``` swift
func jediBladeColor (colors: String...) -> () {
  for color in colors {
    println("\(color)")
  }
}
jediBladeColor("red","green")

```

## 定义一个闭包

闭包被置于花括号`{}`中，且定义为`()->()`类型的函数。其中`->`分隔了入参与返回类型，其后的`in`关键字分隔了闭包头与闭包体。

``` swift
{ (params) -> returnType in
  statements
}

```

一个例子，`map`函数应用于数组。

``` swift
let padawans = ["Knox", "Avitla", "Mennaus"]
padawans.map({
  (padawan: String) -> String in
  "\(padawan) has been trained!"
})

```

## 类型已知的闭包

当闭包的入参类型已知时，可以这样写：

``` swift
func applyMutliplication(value: Int, multFunction: Int -> Int) -> Int {
  return multFunction(value)
}

applyMutliplication(2, {value in
  value * 3
})

```

## 闭包省略入参名

闭包入参可以不用参数名而是位置($0,$1,...)来访问

``` swift
applyMutliplication(2, {$0 * 3})
```

甚至，如果闭包是函数的最后一个入参时，圆括号可以这样省略掉

``` swift
applyMutliplication(2) {$0 * 3}
```



（翻译自 <http://fuckingswiftblocksyntax.com/>）