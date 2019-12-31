title: learning-go-lang
date: 2012-12-09 20:39:50
tags:
	- go
	
categories:
	- programming
	- go
	
---


## 安装


由于墙，<http://golang.org> 国内无法访问，其它在线教程也一样 
<http://tutorial.golang.org> 
<http://go-tour-zh.appspot.com/>

可以在本机运行，
先安装Go 编译器   <http://code.google.com/p/go/downloads/list>

然后安装教程
``` [go]
go get code.google.com/p/go-tour/gotour  
```

或者中文的
``` [go]
go get bitbucket.org/mikespook/go-tour-zh/gotour  
```

最后执行安装产生的 gotour 执行文件，即可在<http://localhost:3999> 打开教程。
一些练习的答案

##46  练习：斐波纳契闭包
``` [go]
import "fmt"  
  
// fibonacci 函数会返回一个返回 int 的函数。  
func fibonacci() func() int {  
    var a int = 1  
    var b int = 1  
      
    return func() int {  
        c := a+b  
        a = b  
        b = c  
        return c  
    }  
}  
  
func main() {  
    f := fibonacci()  
    for i := 0; i < 10; i++ {  
        fmt.Println(f())  
    }  
}
```


##69  练习：等价二叉树
``` [go]
package main  
  
import (  
    "bitbucket.org/mikespook/go-tour-zh/tree"  
    "fmt"  
)  
   
type Tree struct { 
    Left  *Tree 
    Value int 
    Right *Tree 
}  
  
// Walk 步进 tree t 将所有的值从 tree 发送到 channel ch。  
func Walk(t *tree.Tree, ch chan int) {  
    if t != nil {  
        Walk(t.Left, ch)  
        ch <- t.Value  
        Walk(t.Right, ch)  
    }  
}  
  
// Same 检测树 t1 和 t2 是否含有相同的值。  
func Same(t1, t2 *tree.Tree) bool {  
    ch1 := make(chan int, 10)  
    go Walk(t1, ch1)  
  
    ch2 := make(chan int, 10)  
    go Walk(t2, ch2)  
  
    for i := 0; i < 10; i++ {  
        if <-ch1 != <-ch2 {  
            return false  
        }  
    }  
    return true  
}  
  
func main() {  
    fmt.Println(Same(tree.New(1), tree.New(1)))  
}  
```


##57  练习：错误处理

``` [go]
package main  
  
import (  
    "fmt"  
)  
  
  
type ErrNegativeSqrt float64  
  
func (e ErrNegativeSqrt) Error() string {  
    return fmt.Sprintf("cannot Sqrt negative number: %f",  e)  
}  
  
func Sqrt(f float64) (float64, error) {  
  
    if f < 0 {  
        return 0, ErrNegativeSqrt(f)  
    }  
      
    x := f  
    for i := 0; i < 10; i++ {  
        x = (x + f/x) / 2  
    }  
    return x, nil  
  
  
  
}  
  
func main() {  
    fmt.Println(Sqrt(2))  
    fmt.Println(Sqrt(-2))  
}  
```

##44  练习：Map

``` [go]
package main  
  
import (  
    //"fmt"  
    "strings"  
    "bitbucket.org/mikespook/go-tour-zh/wc"  
)  
  
func WordCount(s string) map[string]int {  
    result := map[string]int {}  
    f := strings.Fields(s)  
    for _, v := range f {  
        result[v] += 1    
    }  
    return result  
}  
  
func main() {  
    wc.Test(WordCount)  
}  
```