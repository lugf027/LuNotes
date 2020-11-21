# Effective C++ v3



### 01 view C++ as a federation of language

C++主要的次语言：

* C:

  C++ 以 C 为基础。区块、语句、预处理、内置数据类型、数组、指针都来自 C。

* Object-Oriented C++:

  C with Classes，包括类、封装、继承、多态、虚函数动态绑定等。

* Template C++:

  C++的泛型编程部分。

* STL:

  standard template library。

故此，C++ 高效编程守则视状况而变化，取决于用 C++ 的哪一部分。比如对于内置基本类型， pass-by-value 更高效。对于 Object-Oriented C++ 的自定义类和 Template C++, pass-by-reference-to-const 往往更好。而 STL 的话 pass-by-value 又更好。



### 02 Prefer consts, enums, and inlines to #defines

建议：

* 对于单纯常量，最好用 const 对象或者 enums 替代 #define
* 对于形似函数的 macros, 最好用 inline 函数替代 #define

注：

* static 类成员不能在类的构造函数初始化，只能在类外面定义（初始化）

* 静态常量可在内部赋值也可在类外，如在内部赋值，外部也要有定义（/声明）

* 如编译器不接受类内部定义，类的内部又需要用到时，int 可在 enum 内定义

  ```C++
  class Widgts
  {
  private:
      enum {IntNum = 5 };
      int IntArr[IntNum];
  }
  ```

* Enums 和 #defines 不会导致内存分配，均无法取地址

* #define 无法创建针对 class 的专属常量，亦即不具有封装性；而内联函数可以具有封装性

* 采用内联函数替换宏函数，必要时和模版结合



### 03 Use Const whenever possible



### 04 Make sure that object are initialized before they're used

