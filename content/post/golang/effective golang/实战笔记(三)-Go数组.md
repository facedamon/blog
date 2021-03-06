---
title: "[实战笔记(三) | Go数组]"
date: 2019-10-14T01:42:56+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["golang"]
categories: ["golang/实战系列"]
author: "flysnow"
---

>转载自 https://www.flysnow.org

## 1. 内部实现
要想更清晰的了解数组，我们得了解它的内部实现。数组是长度固定的数据类型，必须存储一段相同类型的元素，而且这些元素是连续的。我们这里强调固定长度，可以说这是和切片最明显的区别。
数组存储的类型可以是内置类型，比如整型或者字符串，也可以是自定义的数据结构。因为是连续的，所以索引比较好计算，所以我们可以很快的索引数组中的任何数据。
这里的索引，一直都是0，1，2这样的，因为其元素类型相同，我们也可以使用反射，获取类型占用大小进行移位，获取相应的元素，这个反射的时候，我们再讲。
## 2. 声明和初始化
数组的声明和初始化和其它类型差不多。声明的原则是：
1. 指明存储数据类型。
2. 存储元素的数量，也就是数组的长度。
		var array [5]int
以上我们声明了一个数组array，但是我们还没有对他进行初始化，这时候数组array里面的值，是对应元素类型的零值，也就是说，现在这个数组是5个0，这和我们java不一样，java里是null。
数组一旦声明，其元素类型和大小都不能变了，如果我们再次进行初始化怎么做呢，可以采用如下办法：
		var array [5]int
		array = [5]int{1,2,3,4,5}
这两步比较繁琐，go提供了`:=`操作符，可以让我们在创建数组的时候直接初始化
		array := [5]int{1,2,3,4,5}
这种简短变量声明方式不仅适用与数组，还适用与任何数据类型，这也是go常用的方式。
有时候我们更懒，连数组的长度都不想指定，不过没关系，使用`...`代替就好了，go会自动推导出数组的长度。
		array :=[...]int{1,2,3,4,5}
假如我们只想给索引为1和3的数组初始化相应的值，其它都为0怎么做呢，直接的办法有：
		
		array :=[5]int{0,1,0,4,0}
还有一种更好的办法，上面讲默认初始化为零值，那么我们就利用这个特性，只初始化索引1和3的值：
		array :=[]5int{1:1,3:4}
## 3. 使用数组
数组的访问非常简单，通过索引即可，操作符为[],因为内存是连续的，所以索引访问的效率非常高。
		array:=[5]int{1:1,3:4}
		fmt.Printf("%d", array[1])
修改数组中的一个元素也很简单：
		array:=[5]int{1:1,3:4}
		fmt.Printf("%d\n", array[1])
		array[1] = 3
		fmt.Printf("%d\n", array[1])
如果我们要循环打印数组中的所有值，一个传统的就是使用for循环：
		func main(){
		    array:=[5]int{1:1,3:4}
		    for i := 0; i < 5; i++ {
		        fmt.Printf("索引：%d,值：%d\n",i, array[i])
		    }
		}
不过大部分时候，我们都是使用`for range`循环
		func main(){
		    array:=[5]int{1:1,3:4}
		    for i, v : range array {
		        fmt.Printf("索引：%d,值：%d\n", i, v)
		    }
		}
同类型的数组是可以互相赋值的，不同类型的不行，会编译错误。那么什么事同样类型的数组呢？go语言固定`比如长度一样，并且每个元素的类型也一样的数组`，才是同类型的数组。
		
		array:=[5]int{1:1,3:4}
		var array1 [5]int = array
		var array2 [4]int = array// error
## 5. 指针数组
指针数组和数组本身差不多，只不过元素类型是指针。
		array := [5]*int{1:new(int),3:new(int)}
这样就创建了一个指针数组，并且为索引1和3都创建了内存空间，其它索引都是指针的零值nil，这是修改指针变量的值也很简单：
		array := [5]*int{1:new(int),3:new(int)}
		*array[1] = 1
以上需要注意的是，只可以给索引1和3赋值，因为只有它们分配了内存，才可以赋值，如果我们给索引0赋值运行的时候，会提示无效内存或者是一个nil指针引用。
		panic: runtime error: invalid memoru address or nil pointer dereference
要解决这个问题，我们要先给索引0分配内存，然后再进行赋值修改。
		array := [5]*int{1:new(int),3:new(int)}
		array[0] = new(int)
		*array[0] = 2
		fmt.Println(*array[0])
## 6. 函数间传递数组
在函数间传递变量时，总是以值的方式，如果变量是个数组，那么就会整个复制，并传递给函数，如果该数组很大，比如长度100多万，那么这对内存是一个很大的开销。
		func main(){
		    array := [5]int{1:1,3:4}
		    modify(array)
		    fmt.Println(array)
		}
		func modify(a [5]int){
		    a[1] = 3
		    fmt.Println(a)
		}
通过上面的例子，可以看到，数组是复制的，原来的数组没有修改。我们这里是5个长度的数组还好，一百多万怎么办，有一种传递数组的指针，这样，复制的大小只有一个数组类型的指针大小。
		func main(){
		    array := [5]int{1:2,3:4}
		    modify(&array)
		    fmt.Println(array)
		}
		
		func modify(a *[5]int){
		    a[1] = 3
		    fmt.Println(*a)
		}
这是传递数组的指针的例子，会发现数组被修改了。所以这种情况虽然节省了复制的内存，但是要谨慎使用，因为一不小心，就会修改原数组。导致不必要的问题。
**这里注意，数组的指针和指针数组是两个概念，数组的指针是`*[5]int`，指针数组是`[5]*int`，注意*的位置**
