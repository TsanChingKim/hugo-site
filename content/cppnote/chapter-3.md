---
title: "Chapter-3 标识符和关键字"
date: 2026-08-27
description: "详细介绍了c++中的标识符和关键字相关的内容"
tags: ["c++", "note"]
showTableOfContents: false
---

在程序中，我们需要给变量、函数、类等起名字，这些名字就是**标识符**；而关键字则是 C++ 语言规定好的、具有特殊含义的单词，不能挪作他用。两者紧密相关，但必须区分清楚。

## 一、标识符（Identifier）
标识符是程序员为变量、常量、函数、类、结构体、命名空间等实体起的名字。它本质上是用来标识程序中的某个实体。

### 1. 标识符的命名规则
C++ 标识符必须遵守以下规则（语法层面）：

+ 只能由 **字母**（a-z、A-Z）、**数字**（0-9）和 **下划线**（`_`）组成。
+ **不能以数字开头**。
+ **不能是关键字**（比如不能把某个变量命名为 `int` 或 `if`）。
+ **区分大小写**：`age`、`Age` 和 `AGE` 是三个不同的标识符。
+ 理论上长度不限，但不同编译器可能对长度有限制（通常 255 个字符以内没问题）。

合法标识符示例：

```cpp
age
_privateVal
max_value
StudentName
myClass2
```

非法标识符示例及原因：

```cpp
2ndNumber    // 以数字开头
my-var       // 包含减号 -，不合法
my var       // 包含空格
int          // 是关键字
class        // 也是关键字
```

### 2. 预保留的标识符
除了关键字以外，还有一些名字是预留给编译器/标准库使用的，**建议**不要使用：

+ 以**双下划线**开头的标识符，例如 `__func`。
+ 以**单个下划线 + 大写字母**开头的标识符，例如 `_Value`。
+ 在**全局作用域**下，以 **单个下划线** 开头的标识符，例如 `_global`。

这些规则是为了防止你的代码与编译器内部实现冲突。

### 3. 命名规范（行业习惯）
以下不是语法规则，但属于良好的编码习惯：

+ **变量、函数名**：通常用小驼峰（`myVariable`）或全小写下划线（`my_variable`）。
+ **常量**：通常用全大写加下划线，如 `MAX_SIZE`。
+ **类名**：通常用大驼峰，如 `StudentInfo`。
+ 避免使用过于相似的名字（如 `abc` 和 `abd`），容易引起阅读困难。

例如：

```cpp
int studentScore = 90;        // 变量
const double PI = 3.14159;    // 常量
class CustomerManager { ... }; // 类名
```

## 二、关键字（Keyword）
关键字是 C++ 语言本身预先定义的、具有特殊含义的单词。它们不能被用作标识符。关键字是语言结构的一部分，用来控制编译器和程序行为。

### 1. 常见的 C++ 关键字分类
为了便于记忆，可以按用途分类：

**基本数据类型相关：**

```cpp
bool  char  int  float  double  void  wchar_t
char16_t  char32_t         // C++11 新增
unsigned  signed  short  long
```

**类型修饰与限定：**

```cpp
const  volatile  constexpr  mutable  static  extern  register  thread_local
decltype  auto        // 类型推导
typename  class  struct  enum  union  using  typedef
```

**控制语句相关：**

```cpp
if  else  switch  case  default
for  while  do  break  continue  return  goto
```

**异常处理相关：**

```cpp
try  catch  throw  noexcept
```

**面向对象相关：**

```cpp
public  private  protected  virtual  override  final  this
friend  operator  new  delete  explicit  abstract
```

**模板与泛型：**

```cpp
template  typename  class  concept  requires   // concept/requires 是 C++20 新增
```

**其他重要关键字：**

```cpp
true  false  nullptr  NULL   // NULL 不是关键字，是宏
new  delete  sizeof  alignas  alignof
static_cast  dynamic_cast  const_cast  reinterpret_cast
namespace  using  export  module  import  export
```

### 2. 需要注意的关键字扩展
C++ 在长期演化中不断增加新关键字，例如：

+ C++11 新增：`constexpr`、`decltype`、`nullptr`、`static_assert`、`noexcept`、`thread_local`、`alignas`、`alignof` 等。
+ C++20 新增：`concept`、`requires`、`char8_t`、`co_await`、`co_yield`、`co_return` 等。
+ C++23 新增：`assume` 等（不同版本可能略有差异）。

这意味着如果你编写的代码不小心使用了未来的关键字，在新标准下编译可能会失败。

### 3. 替代记号（Alternative Tokens）
C++ 提供了一些等价于操作符和标点的关键字，主要用于非 ASCII 字符集。它们同样属于关键字，不能作为标识符使用。包括：

```cpp
and      // &&
or       // ||
not      // !
bitand   // &
bitor    // |
xor      // ^
compl    // ~
and_eq   // &=
or_eq    // |=
xor_eq   // ^=
not_eq   // !=
```

例如：

```cpp
if (a and b)   // 等价于 if (a && b)
```

## 三、标识符和关键字的区别
| 项目 | 标识符 | 关键字 |
| --- | --- | --- |
| 定义者 | 程序员 | 语言设计者 |
| 用途 | 给实体命名 | 构成语法结构 |
| 能否被改变 | 可以被赋予不同含义（声明/定义） | 固定含义，不能重新定义 |
| 示例 | `myVariable`、`Student`、`add()` | `int`、`for`、`class`、`const` |
| 是否区分大小写 | 区分 | 通常都是全小写（如 `int`），区分大小写 |
| 是否可作标识符 | 是，但需遵守规则且不能与关键字冲突 | 否 |


需要注意：C++ 关键字都是**小写**的，如 `int`、`for`。而 `Int`、`FOR` 是合法的标识符，不是关键字。

## 四、实践中的注意事项
1. **不要用关键字命名**：这是编译错误，例如：

```cpp
int class = 10;   // 错误：class 是关键字
double return = 1.5; // 错误
```

2. **不要以 **`_`** 开头命名普通变量**：虽然 `_var` 通常能编译，但在全局作用域或者以大写字母开头时是保留的，容易冲突。
3. **注意大小写敏感**：`default` 是关键字，但 `DEFAULT` 不是，你可以用它当标识符，但容易混淆，一般不建议。
4. **使用 **`const`** 和 **`constexpr`** 而不是 **`#define`** 定义常量**，因为宏定义的名称不经过编译器检查，可能会无意中与标识符冲突。
5. **写代码时善用 IDE 高亮**：现代 IDE 会把关键字高亮为特定颜色，如果自己选的名字与关键字颜色相同，就要警惕是否是关键字了。

## 五、如何查看完整关键字列表？
最简单的方式是查阅最新的 C++ 标准文档（如 cppreference.com 的 _C++ keywords_ 页面）。页面上会列出所有关键字，并注明从哪个标准版本开始引入。

---

希望这份讲解让你对 C++ 的标识符和关键字有了清晰的认识。如果还有疑问，欢迎继续交流！


