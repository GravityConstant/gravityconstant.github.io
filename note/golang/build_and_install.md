for more about information, access website [极客学院Go](http://wiki.jikexueyuan.com/project/go-command-tutorial/0.1.html)，[GitHub address, including package](https://github.com/hyper0x)

- NOTE:

  - go env 查看系统变量
  - go clean -cache 清理临时运行的文件
  - go help package
  - go help gopath
  - go help importpath
  - go help buildmode
  
```
> go help packages

1. An import path that is a rooted path or that begins with
a . or .. element is interpreted as a file system path and
denotes the package in that directory.

2. Otherwise, the import path P denotes the package found in
the directory DIR/src/P for some DIR listed in the GOPATH
environment variable (For more details see: 'go help gopath').

3. net/... at the end of the pattern can match an empty string
   ./vendor/... and ./mycode/vendor/... can success

4. Directory and file names that begin with "." or "_" are ignored
by the go tool, as are directories named "testdata".
```

```
> go help importpath


```

##### 包的引入

- import with .

When using . as an alias, one can use the items from the package without qualifying with their package names, as for example in: test1 = ReturnStr()
```
import . ".pack1"
```

- import with _

The package pack1 is imported for its side-effects only, that is: its init functions are executed and global variables initialized.
```
import _ "./pack1/pack1"
```

#### 编译包

GOPATH = e:\workspace;e:\project;e:\gotest

1. 包的生成

```
GOPATH\src> go install test\pack1

GOPATH
  |
   bin
   pkg
      |
      GOOS_GOARCH
         |
         test
            |
            test.a
   src
      |
      test
         |
         test.g
```

2. 生成可执行文件

```
GOPATH\src> go build test
output: test.exe[src目录下]

GOPATH\src> go build test\main.go
output: main.exe[src目录下]

GOPATH\src\test> go build main.go
output: main.exe[test目录下]
```

###### NOTE:

- src/test 重命名为 src/testa
- test.g文件

```
package test
...
```
- 执行go install testa
- main.g文件

```
package main

import "test/testa"     // "test/testa" as test
// import "test/test"   // ok also

func main() {
    test.Func()         // testa.Fun() error occur
}
```

---

<html>
<table>
  <tr><td width=14%>标记名称</td><td>标记描述</td></tr>
  <tr><td>-a</td><td>强行对所有涉及到的代码包（包含标准库中的代码包）进行重新构建，即使它们已经是最新的了。</td></tr>
  <tr><td>-n</td><td>打印编译期间所用到的其它命令，但是并不真正执行它们。</td></tr>
  <tr><td>-p n</td><td>指定编译过程中执行各任务的并行数量（确切地说应该是并发数量）。在默认情况下，该数量等于CPU的逻辑核数。但是在darwin/arm平台（即iPhone和iPad所用的平台）下，该数量默认是1。</td></tr>
  <tr><td>-race</td><td>开启竞态条件的检测。不过此标记目前仅在linux/amd64、freebsd/amd64、darwin/amd64和windows/amd64平台下受到支持。</td></tr>
  <tr><td>-v</td><td>打印出那些被编译的代码包的名字。</td></tr>
  <tr><td>-work</td><td>打印出编译时生成的临时工作目录的路径，并在编译结束时保留它。在默认情况下，编译结束时会删除该目录。</td></tr>
  <tr><td>-x</td><td>打印编译期间所用到的其它命令。注意它与-n标记的区别。</td></tr>
</table>
</html>


#### go build

- 整个包编译

```
src
  test
    pack1
      pack1.go
      packA.go
    toola
      filename.go
      
// 不会再pkg目录下生成.a的包
test> go build  // test目录下生成test.exe
src>  go build test // src目录下生成test.exe
```

- 子目录编译

```
src
  twg_exercise
    ch10
      structs_fields
        struct_pack
          struckPack.go
        main.go
```
main.go

```
import "./structs_fields/struct_pack"

import "twg_exercise/ch10/structs_fields/struct_pack"
```
编译

```
// main.go相对引入，ok, ch10下生成main.exe
ch10> go build -a -v -p 1 ./structs_fields/main.go
// error src下找不到文件\src\structs_fields
ch10> go build structs_fields/main.go
// error local import './structs_fields/struct_pack' in non-local package
ch10> go build ./structs_fields

// main.go 绝对引入,ok, ch10下生成structs_fields.exe
ch10> go build -a ./structs_fields
```

#### go install

```
$HOME/golang/goc2p:
    bin/
    pkg/
    src/
        cnet/
        logging/
        helper/
            ds/
        pkgtool/
        
> go install -a -v -work
> go install -a -v -work ../cnet/ctcp
```
- NOTE:   
这是由于Go认为本地代码包路径的表示只能以“./”或“../”开始，再或者直接为“.”或“..”，而代码包的代码导入路径又不允许以“/”开始。所以，这种用绝对路径表示代码包位置的方式也就不能被支持了。

---

对于李工的nway_pbx_web一定要在nway/nway_pbx_web下编译，否则出错