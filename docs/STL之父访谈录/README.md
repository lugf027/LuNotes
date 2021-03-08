# STL之父访谈录



### [ADT](https://en.wikipedia.org/wiki/Abstract_data_type)

#### 介绍

抽象数据类型( ADT，Abstract Data Type)是指一个数学模型以及定义在此数学模型上的一组操作。它通常是对数据的某种抽象，定义了数据的取值范围及其结构形式，以及对数据操作的集合。

#### 特征

抽象数据类型的特征主要体现在以下几个方面： 

- [数据抽象](https://baike.baidu.com/item/数据抽象/3194259)。用ADT描述程序处理的实体时，强调的是其本质的特征、其所能完成的功能以及它和外部用户的接口（即外界使用它的方法）。

- [数据封装](https://baike.baidu.com/item/数据封装/4760226)。将实体的外部特性和其内部实现细节分离，并且对外部用户隐藏其内部实现细节，它包含两层含义：

- - ①将数据和其行为结合在一起，形成一个不可分割的独立单位；
  - ②信息隐藏，即尽可能隐藏数据内部细节，只留有限的对外接口形成一个边界，与外部发生联系。封装的原则使得软件错误能够局部化，大大降低排错的难度，便于软件的维护。

- [继承性](https://baike.baidu.com/item/继承性/9589682)。[数据封装](https://baike.baidu.com/item/数据封装/4760226)使得一个类型可以拥有一般类型的数据和行为，即对一般类型的继承。若特殊类型从多个一般类型中继承相关的数据和行为，则为[多继承](https://baike.baidu.com/item/多继承/7705685)。

- [多态性](https://baike.baidu.com/item/多态性/4725624)。[多态性](https://baike.baidu.com/item/多态性/4725624)是指在一般类型中定义的数据或行为被特殊类型继承后，具有不同的[数据类型](https://baike.baidu.com/item/数据类型/10997964)或呈现出不同的行为。例如，“苹果”是“水果”的子类，它可以有“水果”的一般“吃”法，但其本身还可以有别的多种“吃法”。

#### 常见类型

在编程语言（或库）和教科书中，常见的几个抽象数据类型如下：

* [complex](https://baike.baidu.com/item/复数)

- [Container](https://en.wikipedia.org/wiki/Container_(abstract_data_type))
- [List](https://en.wikipedia.org/wiki/List_(abstract_data_type))
- [Set](https://en.wikipedia.org/wiki/Set_(abstract_data_type))
- [Multiset](https://en.wikipedia.org/wiki/Set_(abstract_data_type)#Multiset)
- [Map](https://en.wikipedia.org/wiki/Associative_array)
- [Multimap](https://en.wikipedia.org/wiki/Multimap)
- [Graph](https://en.wikipedia.org/wiki/Graph_(abstract_data_type))
- [Tree](https://en.wikipedia.org/wiki/Tree_(data_structure))
- [Stack](https://en.wikipedia.org/wiki/Stack_(abstract_data_type))
- [String](https://baike.baidu.com/item/字符串)
- [Queue](https://en.wikipedia.org/wiki/Queue_(abstract_data_type))
- [Priority queue](https://en.wikipedia.org/wiki/Priority_queue)
- [Double-ended queue](https://en.wikipedia.org/wiki/Double-ended_queue)
- [Double-ended priority queue](https://en.wikipedia.org/wiki/Double-ended_priority_queue)

#### 常用操作

通常为ADT指定的某些操作（可能使用其他名称）是

- compare（*s*，*t*），测试两个实例的状态在某种意义上是否相等；
- hash（*s*），根据实例的状态计算一些标准[哈希函数](https://en.wikipedia.org/wiki/Hash_function)；
- print（*s*）或show（*s*），可产生实例状态的人类可读表示。

在命令式ADT定义中，通常还会发现

- create（），产生ADT的新实例；
- initialize（*classInstance*），即准备一个新创建的实例*小号*用于进一步的操作，或重置它一些“初始状态”;
- copy（*s*，*t*），将实例*s*置于与*t*相同的状态；
- clone（*t*），执行*s* ← create（），copy（*s*，*t*），并返回*s*；
- free（*小号*）或destroy（*小号*），即回收的存储器中并且由使用的其它资源*小号*。
  * free由于ADT是不“使用内存”的理论实体，因此该操作通常不相关或没有意义。但是，当需要分析使用ADT的算法使用的存储时，可能有必要。在这种情况下，需要使用其他公理来指定每个ADT实例使用多少内存（作为其状态的函数），并通过将该数量返回给池free。

#### 接口和实现的分离

实现于程序时，抽象[数据类型](https://baike.baidu.com/item/数据类型/10997964)只显现出其接口，并将实现加以隐藏。用户只需关心它的接口，而不是如何实现。未来更可以改变实现的方式。（其支持[信息隐藏](https://baike.baidu.com/item/信息隐藏)原理，或保护程序免受变化的冲击。）

抽象[数据类型](https://baike.baidu.com/item/数据类型/10997964)的强处在于对用户隐藏了实现细节，仅公开其接口。这表示抽象数据类型可以用各种方法来实现，只要遵循其接口，就不会影响到用户。



### C/C++ 内存模式

#### **内存模式**

　　一个程序执行时，先复制到内存，然后CPU逐句读取指令执行。

　　每个存储单元存放一个字节（8bit）数据，每个有一个唯一的地址，地址是顺序编码的。比如：一台计算机256MB内存，则有256*1024*1024个内存单元，如果用7位十六进制数表示地址值，则范围是：0x0000000~0xFFFFFFF。

　　**内存分配：**

- （1）**代码区：**存放程序代码；
- （2）**全局数据区 或 静态变量区：全局变量 + 静态数据。**存放长期数据的区域，从程序开始执行到结束，都会在内存中占有固定的字节；
- （3）**栈区：局部变量。**存放程序函数中的局部变量，就是，用到某个函数时，该函数定义的变量就存在栈区，退出时相应的变量会自动释放；还有特点：先进后出，即先进栈的变量后退出；
- （4）**堆区：动态数据，指针。**在程序执行过程中申请的内存空间，也要在程序结束时释放，否则内存泄漏；

#### **变量存储位置、作用域**

**（1）全局变量**

　　存储位置：全局数据区（程序结束后由系统释放）；

　　作用域：全局作用域，只需在一个源文件中定义，作用所有；

　　周期：一直存在；

　　引用方式：其他文件使用时，用extern 关键字声明。（注意：不能两个文件中定义相同的名字，重定义了）；

**（2）静态变量**

　　存储位置：全局数据区（程序结束后由系统释放）；

　　作用域：文件作用域，在被定义的文件中；

　　周期：一直存在；

　　引用方式：static， const。（**文件不相互包含**，不同文件可定义完全相同的两个静态变量，是两个完全不同的变量）

**（3）局部变量**

　　存储位置：栈区。（编译器自动分配释放，存放函数参数值，局部变量值）；

　　作用域：局部；

　　周期：程序运行出则销毁；

　　引用方式：

**（4）静态局部变量**

　　存储位置：全局数据区；

　　作用域：局部作用域；

　　周期：一直存在；

　　引用方式：static。（只被初始化一次）

**（5）动态数据，指针**

　　存储位置：堆区；

　　作用域：局部作用域；

　　周期：在程序执行中申请的内存空间，在程序结束中释放；

#### **内存中的堆和栈** 

**（1）申请方式、回收方式**

- 栈：局部变量和参数，系统自动分配、回收；
- 堆：程序员分配释放，new/malloc，由os回收，分配方式类似于链表，**如忘记释放，则内存泄漏；**

**（2）缓存方式**

- 栈：一级缓存，由高地址向低方向增长（以地址增长方向为正）；（存的局部变量，空间固定，**栈内存是：先进后出**）
- 堆：二级缓存，向高地址方向增长，（不连续内存空间）；

**（3）空间限制**

- 栈：空间较小，是固定值；
- 堆：不连续内存，空间大且灵活；

### C++内存同步模式(memory order)



#### **内存模型中的同步模式(memory model synchronization modes)**



#### **顺序一致模式(sequentially consistent)**



#### **宽松模式(relaxed)**



#### **获得/释放模式(acquire/release)**



#### **消费/释放模式(consume/release)**



