# CMeke Prime - LLVM

[![LLVM徽标](https://llvm.org/docs/_static/logo.png)](https://llvm.org/docs/index.html)

[TOC]

```
Warning
Disclaimer: This documentation is written by LLVM project contributors not anyone affiliated with the CMake project. This document may contain inaccurate terminology, phrasing, or technical details. It is provided with the best intentions.

警告
免责声明：本文档由LLVM项目贡献者而非 任何与CMake项目关联的人编写。本文档可能包含不正确的术语，措辞或技术细节。仅仅以最好的意图提供罢了。
```



## Introduction

LLVM项目和许多基于LLVM的核心项目都是使用CMake构建的。本文档旨在为开发人员修改LLVM项目或在LLVM之上构建自己的项目提供CMake的简要概述。

CMake官方语言参考可在cmake语言手册页和[cmake语言在线文档中找到](https://cmake.org/cmake/help/v3.4/manual/cmake-language.7.html)。



## 10,100 ft View

CMake是一种以自己的语言读取脚本文件的工具，该脚本文件描述了软件项目的构建方式。当CMake运行脚本时，它会构建软件项目的内部表示形式。一旦脚本被完全处理，如果没有错误，CMake将生成构建文件来实际构建项目。CMake支持为各种命令行生成工具以及流行的IDE生成生成文件。

当用户运行CMake时，它将执行各种检查，类似于autoconf过去的工作方式。在检查和评估构建描述脚本期间，CMake将值缓存到CMakeCache中。这很有用，因为它允许构建系统在增量开发过程中跳过长时间运行的检查。CMake缓存也有一些缺点，但这将在后面讨论。



## Scripting Overview

CMake的脚本语言具有非常简单的语法。每种语言构造都是与模式 \_name\_(\_args\_) 匹配的命令。命令分为三种主要类型：语言定义的（在CMake中用C ++实现的命令），定义的函数，定义的宏。CMake发行版还包含一套CMake模块，其中包含有用功能的定义。

下面的示例是用于构建C ++“ Hello World”程序的完整CMake构建。该示例仅使用CMake语言定义的函数。

```cmake
cmake_minimum_required(VERSION 3.15)
project(HelloWorld)
add_executable(HelloWorld HelloWorld.cpp)
```

CMake语言以foreach循环和if块的形式提供控制流构造。为了使上面的示例更加复杂，您可以在目标平台是Apple时添加if块来定义“ APPLE”：

```cmake
cmake_minimum_required(VERSION 3.15)
project(HelloWorld)
add_executable(HelloWorld HelloWorld.cpp)
if(APPLE)
  target_compile_definitions(HelloWorld PUBLIC APPLE)
endif()
```



## Variables, Types, and Scope

### Dereferencing

在CMake中，变量是“字符串 (stringly ) ”类型的。在整个运行过程中，所有变量均表示为字符串。在对变量进行`${}`解引用时会对其进行包装，并导致名称用字面值替换为值。CMake在其文档中将其称为“变量评估”。取消引用是在被调用的命令接收参数之前执行 的。这意味着 dereferencing a list  会导致多个单独的参数传递给命令。

变量dereferences 可以嵌套并用于对复杂数据建模。例如：

```cmake
set(var_name var1)
set(${var_name} foo) # same as "set(var1 foo)"
set(${${var_name}}_var bar) # same as "set(foo_var bar)"
```

Dereferencing 未设置的变量将导致空扩展（empty expansion）。在CMake中，有条件地设置变量是一种常见的模式，因为知道它将在未设置变量的代码路径中使用，因此有条件地设置变量。整个LLVM CMake构建系统中都有此示例。

变量空扩展的一个示例是：

```cmake
if(APPLE)
  set(extra_sources Apple.cpp)
endif()
add_executable(HelloWorld HelloWorld.cpp ${extra_sources})
```

在此示例中，`extra_sources`仅当您针对Apple平台时才定义变量。对于所有其他目标，`extra_sources`在为add_executable指定参数之前，它将被评估为空。



### List

在CMake中，列表是用分号分隔的字符串，强烈建议您避免在列表中使用分号。那样不顺畅。定义列表的一些示例：

```cmake
# Creates a list with members a, b, c, and d
set(my_list a b c d)
set(my_list "a;b;c;d")

# Creates a string "a b c d"
set(my_string "a b c d")
```



### Lists of Lists

CMake中更复杂的模式之一元素为列表的列表。因为列表不能包含带有分号的元素来构造列表列表，所以您要创建一个引用其他列表的变量名称列表。例如：

```cmake
set(list_of_lists a b c)
set(a 1 2 3)
set(b 4 5 6)
set(c 7 8 9)
```

使用这种布局，您可以使用以下代码遍历打印每个值的列表列表：

```cmake
foreach(list_name IN LISTS list_of_lists)
  foreach(value IN LISTS ${list_name})
    message(${value})
  endforeach()
endforeach()
```

您会注意到内部的foreach循环列表被双重dereferenced。这是因为第一个dereference 变成`list_name`了子列表的名称（在示例中为a，b或c），然后第二个dereferenced 是获取列表的值。

在整个CMake中都使用此模式，最常见的示例是编译器标志选项，CMake指的是使用以下变量扩展：CMAKE _ $ {LANGUAGE} _FLAGS 和CMAKE _ $ {LANGUAGE} _FLAGS _ $ {CMAKE_BUILD_TYPE}。

### Other Types

在命令行上缓存或指定的变量可以具有与它们关联的类型。CMake的UI工具使用变量的类型来显示正确的输入字段。变量的类型通常不会影响evaluation，但是CMake确实对某些变量（例如PATH）进行了特殊处理。您可以在[CMake的set文档中](https://cmake.org/cmake/help/v3.5/command/set.html#set-cache-entry)阅读有关特殊处理的更多信息。



### Scope

CMake本质上具有基于目录的作用域。在CMakeLists文件中设置变量，将为该文件以及所有子目录设置变量。在CMakeLists文件中包含的CMake模块中设置的变量将在包含它们的范围以及所有子目录中进行设置。

在子目录中再次设置已经设置的变量时，它将覆盖该作用域和所有更深子目录中的值。

CMake `set`命令提供了两个与范围相关的选项。`PARENT_SCOPE`将变量设置为父范围，而不是当前范围。`CACHE`选项在`CMakeCache`中设置变量，这导致在所有作用域中都设置了该变量。除非指定了`FORCE`选项，否则`CACHE`选项不会设置`CACHE`中已经存在的变量。

除了基于目录的作用域，CMake函数还具有自己的作用域。这意味着在函数内部设置的变量不会渗入父级作用域。对于宏而言，情况并非如此，因此，LLVM在合理的情况下更喜欢使用函数而不是宏。

```
Note
Unlike C-based languages, CMake’s loop and control flow blocks do not have their own scopes.

注意
与基于C的语言不同，CMake的循环和控制流模块没有自己的作用域。
```

## Control Flow

CMake具有与任何脚本语言相同的基本控制流构造，但是存在一些怪癖，因为与CMake中的所有内容一样，控制流构造是命令。



### If, ElseIf, Else

注意

有关CMake if命令的完整文档，请转到 [此处](https://cmake.org/cmake/help/v3.4/command/if.html)。该资源更加完整。



通常，CMake if块按您期望的方式工作：

```cmake
if(<condition>)
  message("do stuff")
elseif(<condition>)
  message("do other stuff")
else()
  message("do other other stuff")
endif()
```

关于CMake的最重要的事情是来自 C 背景的块是否没有自己的作用域。在条件块内设置的变量在`endif()`。之后继续存在。



### Loops

CMake`foreach`块的最常见形式是：

```cmake
foreach(var ...)
  message("do stuff")
endforeach()
```

`foreach`块的变量参数部分可以包含dereferenced lists，要迭代的值或两者的混合：

```cmake
foreach(var foo bar baz)
  message(${var})
endforeach()
# prints:
#  foo
#  bar
#  baz

set(my_list 1 2 3)
foreach(var ${my_list})
  message(${var})
endforeach()
# prints:
#  1
#  2
#  3

foreach(var ${my_list} out_of_bounds)
  message(${var})
endforeach()
# prints:
#  1
#  2
#  3
#  out_of_bounds
```

还有一种更现代的CMake foreach语法。下面的代码与上面的代码等效：

```cmake
foreach(var IN ITEMS foo bar baz)
  message(${var})
endforeach()
# prints:
#  foo
#  bar
#  baz

set(my_list 1 2 3)
foreach(var IN LISTS my_list)
  message(${var})
endforeach()
# prints:
#  1
#  2
#  3

foreach(var IN LISTS my_list ITEMS out_of_bounds)
  message(${var})
endforeach()
# prints:
#  1
#  2
#  3
#  out_of_bounds
```

与条件语句类似，它们通常表现出您所期望的行为，并且没有自己的范围。

CMake还支持`while`循环，尽管它们在LLVM中并未广泛使用。



## Modelus, Functions and Macros

### Modules

模块是CMake的工具，可实现代码重用。CMake模块只是CMake脚本文件。它们可以包含要在包含以及命令定义上执行的代码(They can contain code to execute on include as well as definitions for commands.)。

在CMake中，宏和函数通常称为命令，它们是定义可以多次调用的代码的主要方法。

在LLVM中，我们为不从源代码构建项目的开发人员提供了多个CMake模块，这些模块作为我们发行版的一部分。这些模块是使用CMake构建基于LLVM的项目所需的基本组件。我们还依靠模块来组织构建系统的功能，以实现可维护性和在LLVM项目中的重复使用。



### Argument Handling

在定义CMake命令时，处理参数非常有用。本节中的示例将全部使用CMake`function`块，但这也适用于该`macro`块。

CMake命令可以具有每个调用站点都需要的命名参数。另外，所有命令都将隐式接受可变数量的额外参数（用C语言来说，所有命令都是`varargs`函数）。当使用额外的参数（除了已命名参数之外）调用命令时，CMake会将完整的参数列表（已命名和未命名）存储在named列表中`ARGV`，并将未命名参数的子列表存储在中`ARGN`。以下是为CMake的内置函数提供包装函数的简单示例`add_dependencies`。

```cmake
function(add_deps target)
  add_dependencies(${target} ${ARGN})
endfunction()
```

本示例定义了一个新的名为的宏`add_deps`，该宏带有必需的第一个参数，并且仅调用通过第一个参数和所有尾随参数的另一个函数。

CMake提供了一个模块`CMakeParseArguments`，该模块提供了高级参数解析的实现。我们在LLVM上都使用了此方法，建议将其用于具有复杂的基于参数的行为或可选参数的任何函数。CMake的模块官方文档在手册 `cmake-modules`页中，也可以在 [cmake-modules在线文档中找到](https://cmake.org/cmake/help/v3.4/module/CMakeParseArguments.html)。



```
Note:
As of CMake 3.5 the cmake_parse_arguments command has become a native command and the CMakeParseArguments module is empty and only left around for compatibility

注意
从CMake 3.5开始，cmake_parse_arguments命令已成为本机命令，并且CMakeParseArguments模块为空，仅出于兼容性考虑而保留。
```



### Functions Vs Macros

函数和宏在用法上看起来非常相似，但是两者之间存在一个根本区别。函数有自己的作用域，而宏则没有。这意味着在宏中设置的变量将渗入调用范围。这使得宏仅适合于定义很少的功能。

CMake函数和宏之间的另一个区别是参数的传递方式。宏的参数不设置为变量，而是在执行宏之前在宏中解析对参数的dereferences 。如果使用未引用的变量，则可能导致某些意外行为。例如：

```cmake
macro(print_list my_list)
  foreach(var IN LISTS my_list)
    message("${var}")
  endforeach()
endmacro()

set(my_list a b c d)
set(my_list_of_numbers 1 2 3 4)
print_list(my_list_of_numbers)
# prints:
# a
# b
# c
# d
```

一般来说，此问题并不常见，因为它需要使用名称未在父作用域中重叠的未解引用变量，但请务必注意，因为它可能导致细微的错误。



## LLVM Project Wrappers

LLVM项目提供了许多围绕关键CMake内置命令的包装器。我们使用这些包装器在LLVM组件之间提供一致的行为并减少代码重复。

我们通常（但不总是）遵循这样的约定，即以命令开头的命令 `llvm_`只能用作其他命令的构建块。通常，旨在直接使用的包装器命令的名称紧随其后，在命令名称的中间（即`add_llvm_executable` 是的包装器`add_executable`）。LLVM`add_*`包装器功能全部定义`AddLLVM.cmake`为LLVM发行版的一部分。任何需要LLVM的LLVM子项目都可以包含和使用它。

```
Note:
Not all LLVM projects require LLVM for all use cases. For example compiler-rt can be built without LLVM, and the compiler-rt sanitizer libraries are used with GCC.

注意
并非所有LLVM项目在所有用例中都需要LLVM。例如，可以在不使用LLVM的情况下构建编译器-rt，并且将编译器-rt清理程序库与GCC一起使用。
```



## Useful Buitl-in Commands

CMake有很多有用的内置命令。由于CMake项目具有出色的文档，因此本文档将不涉及它们的详细信息。要突出显示一些有用的功能，请参见：

- [add_custom_command](https://cmake.org/cmake/help/v3.4/command/add_custom_command.html)
- [add_custom_target](https://cmake.org/cmake/help/v3.4/command/add_custom_target.html)
- [file](https://cmake.org/cmake/help/v3.4/command/file.html)
- [list](https://cmake.org/cmake/help/v3.4/command/list.html)
- [math](https://cmake.org/cmake/help/v3.4/command/math.html)
- [string](https://cmake.org/cmake/help/v3.4/command/string.html)

手册中提供了CMake命令的完整文档`cmake-commands`，可在[CMake网站上找到](https://cmake.org/cmake/help/v3.4/manual/cmake-commands.7.html)