# 基础概念:
* main包下 必须创建main函数为程序入口
* 调用同包函数不需要文件
* 参数传递分两种 
	+ 值传递 （基本数据类型 数组 结构体 string）
	+ 引用传递 （指针 slice切片 map 管道chan interface等）

# 数据类型:
### 基本数据类型:
* 整型：

	| 类型   	 | 占用存储   | 表数范围 		   | 备注  |
	| :---   	 |   :----:  |     :---:	   |:----:|
	| int      | 32位系统4字节 64位系统8字节     |-|-|
	| int8     | 1字节      | -128～127	     |-|
	| int16    | 2字节      | -2^15~2^15-1  |-|
	| int32    | 4字节      | -2^31~2^31-1  |-|
	| int64    | 8字节      | -2^63~2^63-1  |-|
	| uint     | 32位系统4字节 64位系统8字节     |-|-|
	| uint8    | 1字节      | 0~255         |-|
	| uint16   | 2字节      | 0~2^16-1      |-|
	| uint32   | 4字节      | 0~2^32-1      |-|
	| uint64   | 8字节      | 0~2^64-1      |-|
	| rune     |  4字节     | -2^31~2^31-1  |等价于int32 表示Unicode码 |
	| byte     | 1字节      | 0～255	      |-|


* 浮点

	| 类型   | 占用存储   | 表数范围 | 备注     |
	| :----| :-----:| :----:| :----:|
	| float32| 4字节 | - | 单精度|
	| float64| 8字节 | - | 双精度| 


    	  
* 字符类 
	- Go中没有专有类型，一般使用byte 来保存单个字母字符
	- 保存的字符在ASCII表中，可以直接使用byte， 如果对应码值大于255 ，应该使用int类型保存
	- 如果按照字符方式输出，需要格式化 `fmt.Printf("%c",c)`
	- 汉字3字节 英文1字节


* 布尔 bool
	- 占1个字节

	
* 字符串 string
	- string（go官方将string归属为基本数据类型)一个字符占3个字节，字符串按下标方式获取到的为每个字节的十进制形式
	- 为不可变类型 重新赋值会创建新的内存空间
	- 有两种表现形式
		- 双引号 会识别转义字符
		- 反引号 以字符串原本形式输出 不会转义

-
 
基本数据类型的转换: 
	
```go
//表达式 T(v) ，将v转换为类型T
var i int32 = 100
//将 int32类型 转换为 float32类型
var i float32 = float32(i)
```
浮点类型转换为二进制时:

* 整数部分直接转换
* 小数部分\*2，如果结果小于1，则继续\*2，如果结果大于1，则结果-1后的小数部分继续*2，直到结果刚好等于1

基本数据类型和string之间的转换:

- fmt.Springf()
- strconvb包
- 字符串转布尔类型时 "1","t","T","true","True" 转换后都为true 
- "0","f","F","false","False" 转换后都为false

-
    
### 派生/复杂数据类型:
* 指针 Pointer

	+ 引用传递
	+ \*[Type] 声明指针变量 type代表数据指向的数据类型
	+ \&[name] 得到当前变量的指针
	+ \*[name] 得到指针指向的值

	```go
	//*[Type] 声明指针变量 type代表数据指向的数据类型
	//&[name] 得到当前变量的指针
	//*[name] 得到指针指向的值
	var ptr *int //声明一个int类型的指针
	*ptr = 10 // 赋值	
	```
	
* 数组 
	
	+ 数组的第一个元素地址就是数组的地址
	+ 数组内元素地址间隔是由元素类型所占字节大小决定
	+ 数组默认进行值传递，传递过程会进行值拷贝，需要在其他函数中变更数组元素值需要传递指针（引用传递）

	```go
	//声明一个长度为20的int数组
	var int_arrays [20]int
	int_arrays[0] = 1
	int_arrays[1] = 3
	
	```

* 结构体 struct
	
	+ 相当于Java中的对象，可定义属性，绑定函数
	+ 属于值类型 传递进行值拷贝


	```go
	type Cat struct {
	    Name  string
	    Age   int
	    Color string
	}
	func main() {
	    var cat Cat
	    cat.Name = "cat name"
	    cat.Age = 10
	    cat.Color = "red"
	}
	```
 	绑定方法:

	```golang\
	type Cat struct {
	    	Name  string `json:"name"`
	    	Age   int    `json:"age"`
	    	Color string `json:"color"`
	}
	func (cat Cat) eat() {
		//值传递 不会改变原对象属性
		cat.Name = "robot"
	}
	func (c *Cat) eat_1() {
		// 引用传递 会改变原对象值
		c.Name = "robot"
	}
	```
	
	声明结构体:
	
	```go
	func main(){
		var cat Cat
		
		cat1 := Cat{}
		
		cat2 := Cat{"c", 2, "black"}
		
		//结构体指针
		var cat3 *Cat = new(Cat)
		
		//标准写法
		(*cat3).Name = "d"
		
		//可简写
		cat3.Age = 12
		
		//简写方式同上
		var cat4 *Cat = &Cat{}
		cat4.Name = "e"
		var cat5 *Cat = &Cat{"f", 20, "blue"
	}
	```
	
	+ 结构体类型实现String()方法后，fmt.print()方法会默认调用类型的String()方法进行输出 （类似java中重写toString()方法) 前提是使用指针绑定
		
	+ 结构体工厂模式 返回一个student的指针 相当于java中的构造方法 属性为私有时也需要编写get方法

	```go	
	type student struct {
	    Name  string
	    Score float64
	}
	func Student(name string, score float64) *student {
	    return &student{
	        Name:  name,
	        Score: score}
	}
	```



* 管道 Channel
* 函数 func
	
	+ 在Go中也算作一种数据类型
	
	``` go
	func name(){
		//.....具体逻辑
	}
	
	func main() {
		//也可以将匿名函数赋值给变量
		f := func(i int) {
			fmt.Println(i)
		}
		fmt.Printf("f: %v\n", f)
	}
	```
	
* 切片 slice
	+ 可以理解为是一个可变长度的数组，初始容量为3，每次自增容量长度为之前的2倍，在容量超过1024之后，每次增加之前长度的1/4
	+ 切片 是数组的引用 所以为引用类型 在传递时遵循引用传递机制
	+ 相当于Java中的List\<T>
    
    
 
  ```
	//创建切片
	var num []int 
	var  data = []int{1,3,5}
	data := []int{1,3,5}
	var datas = make([]table)//1代表 长度，
	```
   
* 接口 interface
	
	+ 在Go中体现多态
	+ 接口的数据类型是指针（引用类型）
	+ interface中不能包含变量 ，不需要显示实现
	+ 有个对象包含接口中的所有方法就算实现 
	+ 两个接口都拥有相同的方法时，包含所有方法的对象相当于同时实现两个接口
	+ 自定义类型都可以实现接口
	+ 接口继承 语法与结构体继承一致 接口代码块中声明匿名接口类型
	+ 实现接口时 如果该继承了其他接口 同样需要实现父接口中的所有方法才可
	+ 空接口 interface{} 没有任何方法 相当于所有类型都实现了该接口

	
	```Go
	type AInterface interface {
		say()	
	}
	type BInterface interface {
		hello()
	}
	type Monster struct {
	}
	func (m Monster) say() {
		fmt.Println("say()")
	}
	func (m Monster) hello() {
		fmt.Println("hello()")
	}
	func main() {
		var aInterface AInterface = Monster{}
		aInterface.say()
		var bInterface BInterface = Monster{}
		bInterface.hello()
	}
	```
	
* map

	+ key-value数据结构
	+ 声明时不会分配内存 需要用make开辟内存空间
	+ 无序存储
	+ var 变量名 map[keyType]valueType
	+ 不能以slice,map,func 作为key的类型
	
	创建和赋值：
	
	```Go
	func main() {
		//1
		var kv map[string]string
		kv = make(map[string]string, 10)
		kv["A"] = "a"
		kv["B"] = "b"
		kv["C"] = "c"
		//2
		kv_1 := make(map[string]string, 5)
		kv_1["a"] = "A"
		//3 
		kv_2 := map[string]string{
			"A": "a",
			"B": "b",
		}
	}
	```
	
	调用：
	

# 保留关键字:
- break
- default
- func
- interface
- select
- case
* defer
	+ defer修饰的语句会被压入独立的栈区 暂时不执行 在执行完代码块后再以弹栈执行（代码块以函数为边界 压入时做值拷贝）
- go
- map
- struct
- chan
- else
- goto
- package
- switch
- const
- fallthrough
* if
	+ 条件判断 
- range
- type
- continue
- for
- import
- return
- var
- new 

```
iota 常量定义计数器，从0开始，用以生成连续常量，只作用于因式分解区域


new关键字返回的为指针类型
make 返回为对象



字符类型
	一个字符3个字节，字符串按下标方式获取到的为每个字节的十进制形式
切片类型
	可以理解为是一个可变长度的数组，初始容量为3，每次自增容量长度为之前的2倍，在容量超过1024之后，每次增加之前长度的1/4
	切片结构：
		属性有 array 指针，len 长度，cap 容量
	创建切片：
	var num []int 
	var  data = []int{1,3,5}
	data := []int{1,3,5}
	var datas = make([]int,1,3)  // 1 代表 长度，4 代表初始化容量， 实际含义为 创建一个切片，切片内数据初始化数据为1（第一个元素有值，int类型默认为0） 容量为3，其中第二第三元素为nil

datas := append(data,3) 含义为在data切片中添加一个新的元素，值为3， 如果append()触发了自动扩容，会创建一块新的内存地址来存放切片数据并返回。


简略声明常量，如需从1开始，写法为 v1 = iota +1

	
```
	

### 闭包：

* 函数使用函数外的参数完成调用 参数与函数称为闭包
	

	
### 异常处理：

* 使用revover()函数接受 err
* 使用panic()抛出异常
	
	```	
	func errorOne() {
	    defer func() {
	        err := recover()
	        if err != nil {
	            fmt.Print(err) //正常输出 不阻断
	            panic(err)     //抛出异常 阻断进程
	        }
	    }()
	    num := 0
	    res := 10 / num
	    fmt.Print(res)
	}
	```

		
		
		
	
### 面向对象：

* 继承： 
	+ golang中没有extends 通过在结构体中嵌套父类匿名结构体实现继承特性
	+ 结构体可以使用嵌套匿名结构体中所有字段和方法 无论公共私有属性/方法
	+ 多层继承中同名方法遵循就近原则
	+ 多继承时调用属性/方法时须明确指出父结构体
		
	```	go
	type person struct {
	    name string
	    age  int
	}
	type student struct {
	    person
	    class string
	}
	```
		
### 类型断言

* 接口是一般类型，需要转为具体类型时，需要使用类型断言

	```go
	package main
	
	import "fmt"
	
	type Point struct {
		x int
		y int
	}
	
	func main() {
		var null_interface interface{}
		var point Point = Point{1, 3}
		null_interface = point
		//类型断言 判断null_interface 是否为指向Point类型的指针
		// 并且尝试转换 转换失败抛出异常
		b ,flag := null_interface.(Point)
		if !flag {
			fmt.Print("转换失败")
		}
		fmt.Printf("b type= %T, value= %v", b, b)
		//b type= main.Point, value= {1 3}
	}
	```
* Go源码中应用类型断言场景，如IO包中copyBuffer方法使用到类型断言
	
	```go
	// copyBuffer is the actual implementation of Copy and CopyBuffer.
	// if buf is nil, one is allocated.
	func copyBuffer(dst Writer, src Reader, buf []byte) (written int64, err error) {
		// If the reader has a WriteTo method, use it to do the copy.
		// Avoids an allocation and a copy.
		if wt, ok := src.(WriterTo); ok {
			return wt.WriteTo(dst)
		}
		// Similarly, if the writer has a ReadFrom method, use it to do the copy.
		if rt, ok := dst.(ReaderFrom); ok {
			return rt.ReadFrom(src)
		}
		...
	}
	----------------------------------------------------------------
	// WriterTo is the interface that wraps the WriteTo method.
	// WriteTo writes data to w until there's no more data to write or
	// when an error occurs. The return value n is the number of bytes
	// written. Any error encountered during the write is also returned.
	// The Copy function uses WriterTo if available.
	type WriterTo interface {
		WriteTo(w Writer) (n int64, err error)
	}
	```
	
	+ 上文中尝试将Reader转型为WriterTo体现多态以调用`WriteTo(w Writer) (n int64, err error)`方法

	

### I/O

#### os包中 File 封装了文件相关动作

* 获取文件使用`os.Open(name string)(file *File,err error)` 会返回一个File指针

	+ 文件读取

		```go
		func main() {
			file, err := os.Open("/Users/arning/go/src/oop/main.go")
			if err != nil {
				fmt.Println(err)
			}
			defer file.Close()
			//获取缓冲读取对象 默认缓冲区大小defaultBufSize = 4096
			reader := bufio.NewReader(file)
			for {
				//读取到换行符刷新缓冲
				str, err := reader.ReadString('\n')
				if err != nil {
					//表示读取到文件末尾
					if err == io.EOF {
						break
					}
					fmt.Println(err)
					break
				}
				fmt.Print(str)
			}
			//直接打开文件，返回byte切片和err
			v, _ := ioutil.ReadFile(file.Name())
			fmt.Printf(string(v))
		}
		```
	
	+ 文件写入
	
		```go
		func main() {
			//参数1 文件路径
			//参数2 打开模式 当前以写入，创建，追加模式打开文件
			//参数3 unix系统中的权限
			file, err := os.OpenFile("./learnFile.txt", os.O_WRONLY|os.O_CREATE|os.O_APPEND, 0755)
			if err != nil {
				fmt.Printf("err: %v\n", err)
				return
			}
			defer file.Close()
			writer := bufio.NewWriter(file)
			for i := 0; i < 3; i++ {
				writer.WriteString("new file write\n")
				writer.Flush()
			}
		}
		```
	
	+ `os.OpenFile(name string, flag int, perm FileMode)(*File, error)`中flag的枚举值:
	
		- O\_RDONLY int = syscall.O_RDONLY // open the file read-only.
		- O\_WRONLY int = syscall.O_WRONLY // open the file write-only.
		- O\_RDWR   int = syscall.O_RDWR   // open the file read-write.
		- O\_APPEND int = syscall.O_APPEND // append data to the file when writing.
		- O\_CREATE int = syscall.O_CREAT  // create a new file if none exists.
		- O\_EXCL   int = syscall.O\_EXCL   // used with O_CREATE, file must not exist.
		- O\_SYNC   int = syscall.O_SYNC   // open for synchronous I/O.
		- O\_TRUNC  int = syscall.O_TRUNC  // truncate regular writable file when opened.
	
* 可以使用`os.IsNotExist(err error)`函数来判断异常是否为文件不存在异常
* 文件拷贝`io.Copy(dst Writer, src Reader) (written int64, err error)`

		
### goroutine
	
* 进程和线程
	- 进程就是程序在系统中的一次执行过程 一个进程就是一个PID
	- 线程是	执行实例 程序的最小单元 
	- 一个进程可以创建/销毁多个线程
	- 一个程序至少一个进程，一个进程至少一个线程
* 并发和并行
	- 多线程在单核运行 并发
	- 多线程在多核运行 并行
	- Go中 会将并发 转换为并行











