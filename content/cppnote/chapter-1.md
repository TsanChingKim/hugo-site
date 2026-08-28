---
title: "Chapter-1 基本数据类型"
date: 2026-08-28
description: "详细讲解c++变量的笔记"
tags: ["note", "c++"]
showTableOfContents: true
---

## 一、整型（Integer Types）
整型用于表示整数，分为**有符号（signed）**和**无符号（unsigned）**两种。默认情况下，`int`、`short`、`long`、`long long` 都是**有符号**的。

| 类型 | 常见大小（64位系统） | 取值范围（典型） |
| --- | --- | --- |
| `short` / `short int` | 2字节 | -32768 ~ 32767 |
| `unsigned short` | 2字节 | 0 ~ 65535 |
| `int` | 4字节 | -2,147,483,648 ~ 2,147,483,647 |
| `unsigned int` | 4字节 | 0 ~ 4,294,967,295 |
| `long` | Windows: 4字节 / Linux: 8字节 | 平台相关 |
| `unsigned long` | 同上 | 平台相关 |
| `long long` | 8字节 | -9.22×10¹⁸ ~ 9.22×10¹⁸ |
| `unsigned long long` | 8字节 | 0 ~ 1.84×10¹⁹ |


**C++ 标准只保证最小尺寸**，而不是精确大小：

+ `short` ≥ 16 位
+ `int` ≥ `short` 的大小
+ `long` ≥ 32 位，且不小于 `int`
+ `long long` ≥ 64 位，且不小于 `long`

因此，**依赖固定字节数的代码应该使用 **`<cstdint>`** 中的固定宽度类型**，如 `int32_t`、`uint64_t` 等。

---

## 二、浮点型（Floating-Point Types）
用于表示带小数点的实数，采用 IEEE 754 标准（通常）。

| 类型 | 常见大小 | 精度 | 典型有效位数 |
| --- | --- | --- | --- |
| `float` | 4字节 | 单精度 | 约 7 位十进制 |
| `double` | 8字节 | 双精度 | 约 15~16 位十进制 |
| `long double` | 8、12或16字节 | 扩展精度 | 平台相关（x86上通常80位） |


**注意事项**：

+ 默认浮点字面量是 `double` 类型，如 `3.14` 是 `double`，`3.14f` 才是 `float`。
+ 浮点数不能精确表示所有十进制小数，比较时应使用误差范围（epsilon）。
+ 避免将浮点数与 `0` 直接用 `==` 比较。

---

## 三、字符类型（Character Types）
C++ 对字符的类型划分比 C 更为严格：

| 类型 | 用途 | 大小 | 说明 |
| --- | --- | --- | --- |
| `char` | 基本字符，通常是 ASCII | 1字节 | 是否是 signed 取决于实现 |
| `signed char` | 明确的有符号字符 | 1字节 | 范围通常 -128~127 |
| `unsigned char` | 明确的无符号字符 | 1字节 | 范围 0~255，常用于字节操作 |
| `wchar_t` | 宽字符（本地化） | 2或4字节 | 在 Windows 上是16位，Linux 上是32位 |
| `char16_t` | UTF-16 字符 | 2字节 | C++11 引入 |
| `char32_t` | UTF-32 字符 | 4字节 | C++11 引入 |
| `char8_t` | UTF-8 字符 | 1字节 | C++20 引入 |


**注意**：`char`、`signed char`、`unsigned char` 是**三种不同的类型**，虽然它们占用的空间可能一样，但不能随意混用。对于原始内存数据处理，推荐使用 `unsigned char` 或 `std::byte`。

---

## 四、布尔型（Boolean Type）
+ `bool` 类型占用 1 字节，取值只能是 `true` 或 `false`。
+ 可以隐式转换为整数：`true` → 1，`false` → 0。
+ 任何非零值赋给 `bool` 都会变成 `true`，零变成 `false`。

---

## 五、空类型（Void Type）
`void` 表示“没有类型”，主要用于：

+ 函数返回值：`void func();`
+ 通用指针：`void* ptr;`（可以指向任意类型，但需显式转换后才能解引用）
+ 模板与泛型编程中作为占位符。

---

## 六、类型修饰符
修饰符用于改变基本类型的含义：

+ **signed / unsigned**：仅适用于整型和字符类型。
+ **short / long / long long**：修饰整数类型，表示长度变化。
+ 可以组合：`unsigned long long`、`long double`（这里的 `long` 修饰浮点数，表示更高精度）。

**需要注意**，`long` 和 `int` 组合时，标准规定 `long int` 是合法的，但通常写作 `long`。

---

## 七、类型大小与最小范围
C++ 标准在 `<climits>` / `<cstdint>` 中定义了每种类型的最大值和最小值。你可以使用：

```cpp
#include <iostream>
#include <limits>

int main() {
    std::cout << "int max: " << std::numeric_limits<int>::max() << '\n';
    std::cout << "double min positive: " << std::numeric_limits<double>::min() << '\n';
    std::cout << "char is signed? " << std::numeric_limits<char>::is_signed << '\n';
}
```

这种方式比硬编码数值更加健壮。

---

## 八、类型转换
### 1. 隐式转换（自动发生）
+ 算术运算中，低级类型会提升为高级类型：`short` → `int` → `long` → `long long` → `float` → `double` → `long double`。
+ 赋值时，右值可能被截断或舍入：例如 `double d = 3.14; int i = d;` 会得到 `3`（丢弃小数部分）。
+ `bool` 与整数之间的自动转换。

### 2. 显式转换
推荐使用 C++ 风格的类型转换，而非 C 风格的 `(type)value`：

| 转换操作符 | 用途 |
| --- | --- |
| `static_cast<Type>(value)` | 常规类型转换（如 int 到 double） |
| `dynamic_cast<Type>(value)` | 多态类层次中的安全向下转换 |
| `const_cast<Type>(value)` | 移除或添加 `const` / `volatile` |
| `reinterpret_cast<Type>(value)` | 无关类型之间的位级转换（如指针转整数） |


```cpp
double d = 3.9;
int i = static_cast<int>(d);       // i = 3
```

### 3. 列表初始化（C++11 起）
花括号初始化会阻止窄化转换：

```cpp
int x = {3.9};   // 错误：浮点数到整数是窄化转换
int y{3};        // 正确
```

---

## 九、类型推导：`auto` 与 `decltype`
C++11 引入了 `auto`，让编译器自动推导变量类型。C++17 中更常用：

```cpp
auto i = 42;        // int
auto d = 3.14;      // double
auto v = 1.0f;      // float
auto b = true;      // bool
```

**注意**：

+ `auto` 会忽略顶层 `const` 和引用，但修饰符如 `const` 要显式写出。
+ `decltype(expr)` 可以获取表达式的精确类型。

```cpp
const int ci = 10;
auto a = ci;           // a 是 int（const 被丢弃）
decltype(ci) b = ci;   // b 是 const int
```

---

## 十、如何选择合适的基本类型？
+ **整数**：默认使用 `int`；需要更大范围用 `long long`；明确需要非负且范围恰好时用 `unsigned`（但注意无符号与有符号混用容易出问题）。
+ **浮点数**：默认使用 `double`；内存非常敏感且对精度要求不高时用 `float`；需要更高精度（如科学计算）用 `long double`。
+ **字符**：处理文本用 `char`（ASCII）/ `char8_t`（UTF-8）；处理 Unicode 用 `char16_t` / `char32_t`。
+ **布尔**：逻辑判断用 `bool`，不要用整型代替。
+ **跨平台**：需要固定宽度类型时使用 `<cstdint>` 中的 `int32_t`、`uint64_t` 等。

---

## 十一、常见陷阱
1. **无符号整数下溢**：`unsigned int u = 0; u--;` 会变成 4294967295，而不是 -1。
2. **有符号/无符号混用**：在表达式中混用有符号和无符号整型时，有符号会转为无符号，可能导致意外结果。
3. **溢出行为**：有符号整数溢出是未定义行为（UB），无符号溢出则定义良好（模运算）。
4. **浮点数精度比较**：用 `std::abs(a - b) < epsilon` 而不是 `a == b`。
5. **char 的签名依赖平台**：如果在不同平台间移植，请使用明确的 `signed char` 或 `unsigned char`。
6. **类型提升坑**：`char c = 200; int i = c;` 若 `char` 是有符号的，`i` 可能是 -56。

---

## 十二、现代 C++ 中的补充
+ C++17 引入了 `std::byte`，用于表示原始内存，比 `unsigned char` 更安全。
+ C++20 增加了 `char8_t` 用于 UTF-8 字符，以及 `std::endian` 等特性。
+ C++23 引入了固定宽度浮点类型（如 `std::float16_t`）的提议，但尚未完全普及。

---

## 总结
基本数据类型是 C++ 的基石。理解其大小、范围、符号性和转换规则，能够避免许多隐蔽的 bug。在设计程序时，优先选择自然、可移植的类型（如 `int`、`double`、`bool`），需要精确控制大小时借助 `<cstdint>`，并使用现代类型转换和列表初始化来保证代码安全性。


