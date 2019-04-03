[原文地址](https://sites.google.com/site/thewaytogo2012/)  
[golang包地址](http://golang.org/pkg/)



### Chapter1
```
package main

import (
    "fmt"
)

const c = "C"

var v int = 5

type T struct{}

func init() { // initialization of package
}

func main() {
    var a int
    Func1()
    // ...
    fmt.Println(a)
}

func (t T) Method1() {
    //...
}

func Func1() { // exported function Func1
    //...
}
```

<html>
<!--在这里插入内容-->
<h4>The <u>order of execution</u> (program startup ) of a Go application is as follows:  </h4>

<p>(1) all packages in package main are imported in the order as indicated,
in every package:
<p>(2) if it imports packages, (1) is called for this package (recursively)
but a certain package is imported only once  
<p>(3) then for every package (in reverse order) all constants and variables are evaluated, and the  
init() if it contains this function.  
<p>(4) at last in package main the same happens, and then main() starts executing.
</html>

<html>
<h4>Initialization of a package</h4>
<p>Program execution begins by importing the packages, initializing the main package and then invoking the function main().</p>
<p>(1) A package with no imports is initialized by assigning initial values to all its package-level variables and then calling any package-level init() function defined in its source.
<p>(2) init() functions cannot be called
</html>




### Chapter4

1. 类型转换
```
type IntArray []int
data := []int{1, 5, 9, 4, 2, 8}
a := IntArray(data)
```
2. 运算符优先顺序
```
Precedence      Operator(s)
7               ^ !
6               * / % << >> & &^
5               + - | ^
4               == != < <= >= >
3               <-
2               &&
1               ||
```
3. 数组的初始化
```
a := []string{
	33: "a",
	1:  "b",    // 最后一个,不可缺少
}
```
4. 数组的索引

```
type Day int
type Week int

func main() {
	s := [3]int{1, 2, 3}
	// 只要[]中不检查Type，只要Kind为int就行
	fmt.Println(s[Day(1)], s[Week(2)])
}
```

### Chapter5 控制结构

- 语句块赋值的变量，不能拿到外面使用
```
for i:=0; i<12; i++ {
    fmt.Printf("%d ", i)
}
fmt.Printf("\n%d", i) // undefined: i
```
- 格式化数据
```
// %#v: go的原始数据格式输出
fmt.Printf("XMLName: %#v\n", v.XMLName)
// %q: 带双引号输出
fmt.Printf("Name: %q\n", v.Name)
fmt.Printf("Phone: %q\n", v.Phone)
fmt.Printf("Email: %v\n", v.Email)
fmt.Printf("Groups: %v\n", v.Groups)
fmt.Printf("Address: %v\n", v.Address)
```

### Chapter6

1. Passing a variable number of parameters

```
package main

import (
	"fmt"
)

func main() {
	x := Min(1, 3, 2, 0)
	fmt.Printf("The minimum is: %d\n", x)
	arr := []int{7, 9, 3, 5, 1}
	x = Min(arr...)
	fmt.Printf("The minimum in the array arr is: %d", x)
}
func Min(a ...int) int {
	if len(a) == 0 {
		return 0
	}
	min := a[0]
	for _, v := range a {
		if v < min {
			min = v
		}
	}
	return min
}
```

2. defer executes before the } or return or error occur   
   arguments are evaluated at the line of the defer-statement
   parameters inverse order (like a stack or LIFO)  

```
package main

import "fmt"

var p = fmt.Printf

func main() {
	f()
}
func f() {
	for i := 0; i < 5; i++ {
		defer fmt.Printf("%d ", i) // output: 4 3 2 1
	}
}
```
2. trace with defer

```
package main

import (
	"io"
	"log"
)

func func1(s string) (n int, err error) {
	defer func() {
		log.Printf("func1(%q) = %d, %v", s, n, err)
	}()
	return 7, io.EOF
}
func main() {
	func1("Go")
}
```
3. This can be convenient for modifying the error return value of a function

```
func f() (ret int) {
	defer func() {
		ret++
	}()
	return 1
}
func main() {
	fmt.Println(f())
}
```

### Chapter7

1. array define

```
//value type
var arr1 [5]int
for i:=0; i < len(arr1); i++ {
    arr1[i] = i * 2
}
// value type
a := [...]int{1, 2, 3, 4, 5}
a := [...]string{“a”, “b”, “c”, “d”}
a := [5]int{1, 2, 3, 4, 5}

// pointer type
var arr1 = new([5]int)
a := []int{1, 2, 3, 4, 5}

slice1 = []int{1, 2, 3, 4, 5}[:]     // ok
slice1 = [5]int{1, 2, 3, 4, 5}[:]    // error
slice1 = [...]int{1, 2, 3, 4, 5}[:]  // error
```

> reference:  
deal with array:  
http://github.com/feyeleanor/slices  
http://github.com/feyeleanor/chain  
http://github.com/feyeleanor/lists

### Chapter9

1. centos7 reboot.go(亲测能用)

```
package main

import (
	"syscall"
)

const LINUX_REBOOT_MAGIC1 uintptr = 0xfee1dead
const LINUX_REBOOT_MAGIC2 uintptr = 672274793
const LINUX_REBOOT_CMD_RESTART uintptr = 0x1234567

func main() {
	syscall.Syscall(syscall.SYS_REBOOT,
		LINUX_REBOOT_MAGIC1,
		LINUX_REBOOT_MAGIC2,
		LINUX_REBOOT_CMD_RESTART)
}
```

2. 测试变量所占用的字节数

```
package main

import (
	"fmt"
	"unsafe"
)

func main() {
	var i string
	fmt.Println(unsafe.Sizeof(i))
}

string      16
int         8
byte        1

[]byte      24
[]string    24
[]int       24

new([]int)    8         // pointer, 默认nil
new([]int32)  8         // pointer
make([]int32, 1)        24

make(map[string]int)    8       // pointer
make(map[string]int32)  8       // pointer
// pointer, use make, or nil alway, cannot inital value
new(map[string]int)     8       
```

3. []byte("hello")和[]byte{"hello"}区别

```
// []byte是强制类型转换，输出24
var i []byte
i = []byte("hello world!!!!!!!lalalalalalalalallalalalala")
fmt.Println(unsafe.Sizeof(i)) // 24

// []byte是初始化，输出24
var i []byte
i = []byte{'h', 'e', 'l', 'l', 'o', 'a', 'b', 'c', 'd', 'e', 'f', 'h'}
fmt.Println(unsafe.Sizeof(i)) // 24

// []byte好像是指针啊
h := i
h[0] = 'z'
fmt.Println(string(i)) // z...
```

#### Chapter 9

```
godoc sort
godoc cmd/sort
// 创建一个临时的服务器 http://localhost:6060，path我并没有指定成功
godoc -http=6060 -path="."
// go的帮助文档,localhost:6060访问
godoc -http=:6060
// 帮助文档命令
godoc strings Fields
// or
godoc cmd/strings Fields
```

#### go packages
> reference
- http://go-lang.cat-v.org/dev-utils (Developer-oriented)
- http://go-lang.cat-v.org/go-code (Programs and applications)
- http://go-lang.cat-v.org/library-bindings (Library bindings)
- http://go-lang.cat-v.org/pure-go-libs (Pure Go libraries)

#### Chapter10 Structs and Methods

1. 方法所引用的结构体参数，是看方法上的结构体是否为传指针，而与初始化的
结构体是否为指针类型无关
2. 结构体方法中的结构体不论是否传指针，初始化的结构体不论是否为指针类型都
可以调用
3. 但是使用接口调用结构体方法的话，结构体方法中的结构体都可以传递指针，
但是传递指针类型的话，只能传递只能，而不能传递结构体

```
type Upper interface {
	UpPerson()
}
type Person struct {
	firstName string
	lastName  string
}

func (p *Person) UpPerson() {
	p.firstName = strings.ToUpper(p.firstName)
	p.lastName = strings.ToUpper(p.lastName)
}
func (p *Person) LowPerson() {
	p.firstName = strings.ToLower(p.firstName)
	p.lastName = strings.ToLower(p.lastName)
}
func (p Person) lowPerson() {
	p.firstName = strings.ToLower(p.firstName)
	p.lastName = strings.ToLower(p.lastName)
}

func main() {
    // 说明1
    var pers1 *Person = &Person{"Chris", "Woodward"}
	pers1.UpPerson()
	fmt.Println(pers1) // &{CHRIS WOODWARD}
	pers1.LowPerson()
	fmt.Println(pers1) // &{chris woodward}
	pers1.lowPerson()
	fmt.Println(pers1) // &{CHRIS WOODWARD}
	// 说明2，结合上文可说明
	pers2 := Person{"Lucy", "Lily"}
	pers2.UpPerson()
	fmt.Println(pers2) // {LUCY LILY}
	pers2.LowPerson()
	fmt.Println(pers2) // {lucy lily}
	pers2.lowPerson()
	fmt.Println(pers2) // {lucy lily}
	// 说明3，结合1，2
	ups := []Upper{pers1, pers2} // err:pers2需要使用指针
	ups := []Upper{pers1, &pers2} // ok
	for n, _ := range ups {
		ups[n].UpPerson()
		fmt.Println("detail:", ups[n])
	}
}
```

#### Chapter11 Interfaces and reflection

- Using method sets with interface  
  ==Summarized:== when you call a method on an interface, it must either have an identical receiver type or it must be directly discernible from the concrete type: P
  1. Pointer methods can be called with pointers.
  2. Value methods can be called with values.
  3. Value-receiver methods can be called with pointer values because they can be dereferenced first.
  4. Pointer-receiver methods ==cannot== be called with values, however, because the value stored inside an interface has no address.

- 接口类型断言和switch...type

```
type Interface interface{}
func main() {
	a := 1
	i := Interface(a)
	b := i.(int) // 断言不进行类型检测
	if t, ok := i.(int); ok {
		fmt.Println(&t, &a)
	}
	switch t := i.(type) {
	case int:
		fmt.Println(&t, &a)
	}
}
```

#### Chapter13 Error-handling and Testing
- 错误处理和恢复

```
package main

import (
	"fmt"
)

func badcall() {
	panic("bad end")
}

func test() {
	defer func() {
		if e := recover(); e != nil {
			fmt.Printf("Panicking %s\r\n", e)
		}
	}()
	badcall()
	fmt.Printf("After bad call\r\n")
}

func main() {
	fmt.Printf("Calling test\r\n")
	test()
	fmt.Printf("Test completed\r\n")
}
```
- 测试和基准检查程序
  1. 测试程序必须在要测试的包里面
  2. 文件名必须是*_test.go的形式
  3. 测试程序不会和正常的go程序一起编译，只有go test才编译所有的文件
  4. 形式，Abcde中的A可以小写，看testing包中写的是小写以rountine的方式运行
  ```
  func TestAbcde(t *testing.T)
  ```
  5. 测试T结构体管理测试状态和支持测试格式化
  ```
  # 标记测试失败，并继续执行
  func (t *T) Fail()
  # 标记测试失败，停止执行；该文件所有的其他测试停止，继续执行其他文件测试
  func (t *T) FailNow()
  # 记录日志
  func (t *T) Log(args …interface{})
  # 先记录日志，然后影响同FailNow()
  func (t *T) Fatal(args …interface{})
  ```
  6. 进行测试之前需要初始化操作(例如打开连接)，测试结束后，需要做清理工作(例如关闭连接)等等，使用使用TestMain()，[用例来源](https://blog.csdn.net/lanyang123456/article/details/79776410)
     - add.go
     ```
     package hello
     func Add(a, b int) int {
         return a + b
     }
     ```
     - add_test.go
     ```
     package hello
     import (
        "fmt"
        "testing"
     )
     func TestAdd(t *testing.T) {
         if Add(1, 2) != 3 {
             t.Errorf("Add(1, 2) failed. Got %d, expected 3.", r)
         }
     }
     func TestMain(m *testing.M) {
         fmt.Println("begin")
         m.Run()
         fmt.Println("end")
     }
     ```
     - 测试
     ```
     $ go test -v
     begin 
     === RUN TestAdd 
     — PASS: TestAdd (0.00s) 
     PASS 
     end 
     ok hello 0.432s
     ```
  7. Benchmark测试
     ```
     func BenchmarkReverse(b *testing.B) {
     }
     
     $ go test -bench=.*
     ```

#### Chapter14 Goroutines and Channels

- 其实引用传递也是值传递，只不过是将数据的地址赋值给一个新的指针

```
// polartocartesian.go
package main

import (
	"bufio"
	"fmt"
	"math"
	"os"
	"runtime"
	"strconv"
	"strings"
)

type polar struct {
	radius float64
	Θ      float64
}

type cartesian struct {
	x float64
	y float64
}

const result = "Polar: radius=%.02f angle=%.02f degrees -- Cartesian: x=%.02f y=%.02f\n"

var prompt = "Enter a radius and an angle (in degrees), e.g., 12.5 90, " + "or %s to quit."

func init() {
	if runtime.GOOS == "windows" {
		prompt = fmt.Sprintf(prompt, "Ctrl+Z, Enter")
	} else { // Unix-like
		prompt = fmt.Sprintf(prompt, "Ctrl+D")
	}
}

func main() {
	questions := make(chan polar)
	defer close(questions)
	answers := createSolver(questions)
	defer close(answers)
	interact(questions, answers)
}

func createSolver(questions chan polar) chan cartesian {
	answers := make(chan cartesian)
	go func() {
		for {
			polarCoord := <-questions
			Θ := polarCoord.Θ * math.Pi / 180.0 // degrees to radians
			x := polarCoord.radius * math.Cos(Θ)
			y := polarCoord.radius * math.Sin(Θ)
			answers <- cartesian{x, y}
		}
	}()
	return answers
}

func interact(questions chan polar, answers chan cartesian) {
	reader := bufio.NewReader(os.Stdin)
	fmt.Println(prompt)
	for {
		fmt.Printf("Radius and angle: ")
		line, err := reader.ReadString('\n')
		if err != nil {
			break
		}
		line = line[:len(line)-1] // chop of newline character
		if numbers := strings.Fields(line); len(numbers) == 2 {
			polars, err := floatsForStrings(numbers)
			if err != nil {
				fmt.Fprintln(os.Stderr, "invalid number")
				continue
			}
			questions <- polar{polars[0], polars[1]}
			coord := <-answers
			fmt.Printf(result, polars[0], polars[1], coord.x, coord.y)
		} else {
			fmt.Fprintln(os.Stderr, "invalid input")
		}
	}
	fmt.Println()
}

func floatsForStrings(numbers []string) ([]float64, error) {
	var floats []float64
	for _, number := range numbers {
		if x, err := strconv.ParseFloat(number, 64); err != nil {
			return nil, err
		} else {
			floats = append(floats, x)
		}
	}
	return floats, nil
}
/* Output:
Enter a radius and an angle (in degrees), e.g., 12.5 90, or Ctrl+Z, Enter to qui
t.
Radius and angle: 12.5 90
Polar: radius=12.50 angle=90.00 degrees -- Cartesian: x=0.00 y=12.50
Radius and angle: ^Z
*/
```
通道变量可以指定读写方向

```
package main

import (
	"bufio"
	"fmt"
	"math"
	"os"
	"runtime"
	"strconv"
	"strings"
)

type polar struct {
	radius float64
	Θ      float64
}

type cartesion struct {
	x float64
	y float64
}

const result = "Polar: radius=%.02f angle=%.02f degrees -- " +
	"Cartesion: x=%.02f y=%.02f\n"

var prompt = "Enter a radius and an angle (in degrees), e.g., 12.5 90, " +
	"or %s to quit."

func init() {
	if runtime.GOOS == "windows" {
		prompt = fmt.Sprintf(prompt, "Ctrl+Z, Enter")
	} else {
		prompt = fmt.Sprintf(prompt, "Ctrl+D")
	}
}

func main() {
    // questions := make(chan<- polar) error
	questions := make(chan polar)
	defer close(questions)
	answers := createSlover(questions)
	defer close(answers)
	interact(questions, answers)
}

func createSlover(questions <-chan polar) chan cartesion {
	answers := make(chan cartesion)
	go func() {
		for {
			polarCoord := <-questions
			Θ := polarCoord.Θ * math.Pi / 180.0
			x := polarCoord.radius * math.Cos(Θ)
			y := polarCoord.radius * math.Sin(Θ)
			answers <- cartesion{x, y}
		}
	}()
	return answers
}

func interact(questions chan<- polar, answers <-chan cartesion) {
	reader := bufio.NewReader(os.Stdin)
	fmt.Println(prompt)
	for {
		fmt.Printf("Radius and angle: ")
		line, err := reader.ReadString('\n')
		if err != nil {
			break
		}
		line = line[:len(line)-1]
		if numbers := strings.Fields(line); len(numbers) == 2 {
			polars, err := floatsForStrings(numbers)
			if err != nil {
				fmt.Println(os.Stderr, "invalid number")
				continue
			}
			questions <- polar{polars[0], polars[1]}
			coord := <-answers
			fmt.Printf(result, polars[0], polars[1], coord.x, coord.y)
		} else {
			fmt.Fprintln(os.Stderr, "invalid input")
		}
	}
}

func floatsForStrings(numbers []string) ([]float64, error) {
	var floats []float64
	for _, number := range numbers {
		if x, err := strconv.ParseFloat(number, 64); err != nil {
			return nil, err
		} else {
			floats = append(floats, x)
		}
	}
	return floats, nil
}

```
- 默认的，通讯时同步的，并且是不缓存：发送不会完成直到有一个接受者接收了数据。试想一个不缓存的通道好像在通道没有数据。
  1. 发送者在通道中阻塞
  2. 接受者也在通道中阻塞