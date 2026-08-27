---
title: "Chapter-2 变量"
date: 2026-08-27
description: "讲解了c++中变量相关的内容"
tags: ["c++", "note"]
showTableOfContents: false
---

## 一、变量（Variable）
变量是程序中用于存储数据的命名内存区域。你可以把变量想象成一个“盒子”，里面可以存放不同类型的值，比如整数、小数、字符等。变量的值在程序运行期间可以改变。

### 1. 变量的声明与初始化
在 C++ 中，声明变量的基本语法是：

```plain
数据类型 变量名;
```

例如：

```cpp
int age;          // 声明一个 int 类型的变量 age
double salary;    // 声明一个 double 类型的变量 salary
char grade;       // 声明一个 char 类型的变量 grade
```

声明后，变量还没有确定的值（如果是局部变量，其内容是不确定的）。通常我们会在声明的同时进行初始化：

```plain
数据类型 变量名 = 初始值;
```

或者使用 C++11 引入的列表初始化（大括号初始化）：

```cpp
int age = 25;        // 传统初始化
int count{10};       // 列表初始化
double pi = 3.14159;
char letter = 'A';
bool isReady = true;
```

列表初始化更安全，能防止窄化转换（例如把 `double` 赋给 `int` 时发生精度丢失）。

### 2. 变量的类型
C++ 提供了丰富的基础数据类型：

+ **整型**：`int`、`short`、`long`、`long long`（以及无符号版本 `unsigned`）
+ **浮点型**：`float`、`double`、`long double`
+ **字符型**：`char`（也有 `wchar_t` 等用于宽字符）
+ **布尔型**：`bool`（`true` 或 `false`）
+ **字符串**：`std::string`（需要包含 `<string>` 头文件）

另外还有自定义类型（如结构体、类）和指针、引用等复合类型。

### 3. 变量的作用域
变量的作用域决定了它在程序中哪些位置可以被访问。

+ **局部变量**：在函数或代码块内声明，只能在该块内使用。
+ **全局变量**：在所有函数之外声明，从声明处到程序结束都可以访问。
+ **静态变量**：使用 `static` 关键字，在函数内部保持其值不变，但只在首次进入时初始化。
+ **命名空间变量**：在命名空间内定义，可被该命名空间内的代码访问。

示例：

```cpp
int globalVar = 100;   // 全局变量

void func() {
    int localVar = 5;  // 局部变量，只在 func 内有效
    static int counter = 0;  // 静态变量，多次调用 func 时 counter 会保留上次的值
    counter++;
}
```

### 4. 变量的 `auto` 类型推导
C++11 起，可以使用 `auto` 关键字让编译器根据初始值自动推导变量类型：

```cpp
auto number = 42;       // number 是 int
auto pi = 3.14;         // pi 是 double
auto name = std::string("Alice"); // name 是 std::string
```

`auto` 让代码更简洁，尤其是在处理复杂类型时。

## 二、常量（Constant）
常量是值在程序运行期间不能被修改的量。它也是命名内存区域，但一旦初始化，值就不能改变。常量让程序更清晰，也避免误修改。

### 1. `const` 关键字
最常用的定义常量的方式是使用 `const`：

```cpp
const int MAX_SIZE = 100;
const double PI = 3.1415926;
```

+ 声明 `const` 变量时必须初始化。
+ 任何试图修改 `const` 变量的操作都会导致编译错误。

### 2. `constexpr` 关键字（C++11 引入）
`constexpr` 用于声明**编译期常量**，即它的值在编译时就能计算得到。它比 `const` 更强，因为 `constexpr` 变量必须用常量表达式初始化，并且编译器会验证。适合用于数组大小、模板参数等需要编译期值的地方。

```cpp
constexpr int SQUARE = 5 * 5;       // 编译期可知
constexpr int FACTORIAL_5 = 120;    // 直接给字面量
```

注意：`const` 变量可能是编译期常量，也可能是运行期才确定但之后不变。`constexpr` 则强制是编译期常量。

### 3. 宏定义 `#define`
这是 C 语言风格的常量定义，使用预处理器指令：

```cpp
#define MAX_LENGTH 1024
```

它实际上是在编译前进行文本替换，不占用内存，也没有类型检查。虽然仍可使用，但现代 C++ 建议用 `const` 或 `constexpr` 代替，因为后者有类型安全且遵循作用域规则。

### 4. 枚举常量
使用 `enum`（枚举）定义一组命名的整数常量：

```cpp
enum Color { RED, GREEN, BLUE };
```

`RED` 默认值为 0，`GREEN` 为 1，`BLUE` 为 2。也可以指定：

```cpp
enum Status { OK = 200, NOT_FOUND = 404, ERROR = 500 };
```

C++11 还提供了 `enum class`，更加类型安全：

```cpp
enum class Color { RED, GREEN, BLUE };
// 使用时需要 Color::RED
```

### 5. 字面量常量
直接写在代码中的值，如 `42`、`3.14`、`'A'`、`"hello"` 都是字面量，它们本身就是常量。

## 三、变量与常量的区别总结
|  | 变量 | 常量 |
| --- | --- | --- |
| 值可变性 | 可以改变 | 初始化后不可改变 |
| 声明方式 | `int x;` | `const int x = 5;` |
| 用途 | 存储动态数据 | 存储固定值或配置参数 |
| 内存 | 通常分配内存 | 可能分配内存（编译期常量也可能优化） |


## 四、最佳实践建议
1. 现代 C++ 中，优先使用 `constexpr` 定义编译期常量。
2. 避免使用 `#define` 定义常量，因为它没有类型检查，容易引发错误。
3. 常量命名一般用全大写加下划线，如 `MAX_BUFFER_SIZE`；变量命名用驼峰或下划线风格。
4. 尽量在声明变量时初始化，避免使用未初始化的局部变量。
5. 使用 `auto` 合理简化类型声明，但不要过度使用。


