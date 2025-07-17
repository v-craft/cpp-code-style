<div align="center">

<h1>V-Craft Code Style Guide</h1>

<p>
   <a href="#ENGLISH"><img src="https://img.shields.io/badge/English-blue?style=for-the-badge" alt="English" /></a>
   &nbsp;&nbsp;
   <a href="#中文"><img src="https://img.shields.io/badge/中文-red?style=for-the-badge" alt="中文" /></a>
</p>
</div>

---

<div id="中文">

## **命名风格**

除了局部循环使用的临时变量外，所有标识符都应该使用英文单词命名，保证语义化。

### 1. 文件名

使用小写单词，下划线分隔。头文件后缀使用 `.hpp` ，源文件使用 `.cpp` ，模块接口文件使用 `.ixx` 。

```
main.cpp                # 普通源文件与模块实现文件
test_unit.ixx           # 模块接口文件
test_unit_macros.hpp    # 头文件
```

本项目将使用标准库模块，并对所有需要的第三方库进行抽象。因此，除抽象层外，应尽量避免使用头文件（除了仅宏定义的头文件）。

模块无法导出宏，因此将所有需要的宏放在头文件中，并使用 `macros` 标志，表示头文件仅含宏定义。

### 2. 宏定义

所有宏定义保持全大写，下划线分隔，并以 `M_` 开头显式表示为宏（ `macro`）。

```cpp
#define M_BOOL32_FALSE 0
#define M_BOOL32_TRUE 1
#define M_ADD(x, y) x+y
```

如果是仅内部使用的宏定义，比如控制头文件防止重复包含的宏，请额外加上 `_` 前缀：

```cpp
#define _M_XXX_HPP
```

### 3. 命名空间

命名空间使用全小写，单层命名空间仅一个单词。如果需要多单词，请使用首字母缩写。

```cpp
namespace tools; // 小写，单个单词
namespace rhi; // rendering hardware interface 缩写，保持小写且简洁
```

### 4. 函数

任何函数（普通函数、静态、成员、模板等）都使用全小写，单词之间使用下划线隔开：

```cpp
void my_func(){ }
```

除函数模板外，**不推荐**使用 `auto fun()-> type {}` 格式。


### 5. 类名

任何类名（普通类名，模板类，枚举，抽象类等）都使用“大驼峰”写法，单词首字母大写，其余部分小写：

```cpp
class MyClassName{};
class RHIClass{}; // 缩写首字母时，保证全大写
```

如果是纯虚类，或明确用作接口的类型，请添加 `I` 前缀：

```cpp
class IMyInterface{};
```
 
枚举类暂定不需要 `E` 前缀，形似常规类名，但枚举值需要特殊命名（参考下面枚举值部分说明）。

```cpp
enum class MyEnumClass{};
```

### 6. 枚举值

枚举值作为特殊类型，此处以 `e` 开头，整体使用“小驼峰”风格。但由于 `e` 开头，实际第一个单词也需要首字母大写。

```cpp
enum class JsonType{
    eNull,
    eString,
    eObject,
    eArray,
    eIntOrFloat, // 仅用于演示，实际项目请使用 eNumber
    eTrueOrFalse // 仅用于演示，实际项目请使用 eBool
};
```

### 7. 临时变量

此处变量仅指函数局部定义的变量，请使用全小写，下划线分隔。

```cpp
void func() {
    int counter = 1;
    int func_index = 2;
}
```

### 8. 成员变量

类的非静态成员变量请使用 `m_` 前缀，依然保持全小写和下划线分隔：

```cpp
class MyClass{
    int m_index;
    std::string m_object_name;
}
```

### 9. 静态变量

如果变量使用 `static` 修饰，请使用 `s_` 前缀，无论是类成员还是函数局部：

```cpp
class MyClass{
    static std::string s_class_name;
};
int func(){
    static int s_func_counter = 0;
    return ++s_func_counter;
}
```

特殊的是，如果静态变量还以 `const` 或 `constexpr` 修饰，则参考下方的常量，保持全大写：

```cpp
class MyClass{
    const static std::string S_CLASS_NAME;
};
```

### 10. 常量

此处的“常量”仅指全局或静态生命周期的常量，不含宏定义或函数局部的临时常变量。

常量的标识符字面应全大写，单词之间使用下划线隔开。

```cpp
constexpr int BOOL32_FALSE = 0; // 全局作用域

void func() {
    static const std::string S_FUNC_NAME{ "void func()" }; // 静态生命周期

    const std::string str{ "Hello world!" }; // 临时不可变对象等同普通变量
}
```

### 11. 概念

C++20 的概念 `concept` ，使用小写字母，下划线分隔。

```cpp
template<typename T>
concept json_types = 
    std::is_same_v<T, std::nullptr_t> || 
    std::is_same_v<T, bool> || 
    std::is_same_v<T, double> || 
    std::is_same_v<T, std::string> || 
    std::is_same_v<T, Object> || 
    std::is_same_v<T, Array>;
```

### 12. 注释

文档注释使用 javadoc 风格，即：

```cpp
/**
 * @brief .....
 * @xxx .....
 */
```

对于类成员，无需大量注释，则使用简化的 `///<` 写法：

```cpp
enum class JsonType{
    eObject, ///< JSON对象类型，由键值对组成的无序集合
    eArray  ///< JSON数组类型，由值组成的有序列表
};
```

## **换行建议**

缩减使用 4 空格，请勿使用制表符。

### 1. 作用域写法

函数、for/if 等语句的 `{` 不换行：

```cpp
void func() { // 左括号不起新行

}
```

对应 if 分支和 try-catch 等结构，使用紧凑的写法：

```cpp
if (1) {

} else if (2) {

} else {

}

try {

} catch( ... ) {

}
```

### 2. 参数换行

函数或 if 的语句的参数过长，需要换行表示：

```cpp
void func(
    int attr1,  // 类名和变量名建议左对齐
    int attr2,
    int attr3,
    int attr4
) { // ) { 独占一行，成员函数的 ) const { 也是一行 
    ...
}
```

模板的过长参数与函数类似：

```cpp
template<typename T>
concept json_types = std::disjunction_v<
    std::is_same<T, std::nullptr_t>,
    std::is_same<T, bool>,
    std::is_same<T, double>,
    std::is_same<T, std::string>,
    std::is_same<T, Object>,
    std::is_same<T, Array>
>;
```

### 3. 链式调用

对于链式调用，如果代码很短，可以放于一行。否则，每次调用都需要换行。

```cpp
vk::CreateInfo info;
info.setAttr0( 0 )
    .setAttr1( 1 )
    .setAttr2( 2 )
    .setAttr3( 3 );
```

### 4. switch 块

建议 `case` 比 `switch` 多一次缩进，左大括号 `{` 与 `case` 同行， `break` 紧贴右大括号 `}` 。


```cpp
switch (1){
    case 2: break;
    case 3: {
        int tmp = 3;
        std::cout << tmp;
    } break;
    default:
        std::cout << "default";
        break;
}
```

</div>

---

<div id="ENGLISH">

## **Naming Style**

Except for temporary variables used in local loops, all identifiers should be named using English words to ensure semantic clarity.

### 1. File Names

Use lowercase words separated by underscores. Header files use `.hpp` suffix, source files use `.cpp`, and module interface files use `.ixx`.

```
main.cpp                # Regular source files and module implementation files
test_unit.ixx           # Module interface file
test_unit_macros.hpp    # Header file
```

This project will use standard library modules and abstract all required third-party libraries. Therefore, except for the abstraction layer, header files should be avoided as much as possible (except for header files containing only macro definitions).

Modules cannot export macros, so all required macros are placed in header files with the `macros` identifier to indicate that the header file contains only macro definitions.

### 2. Macro Definitions

All macro definitions should be in uppercase, separated by underscores, and prefixed with `M_` to explicitly indicate they are macros.

```cpp
#define M_BOOL32_FALSE 0
#define M_BOOL32_TRUE 1
#define M_ADD(x, y) x+y
```

### 3. Namespaces

Namespaces use all lowercase. Single-level namespaces should be a single word. If multiple words are needed, use acronyms.

```cpp
namespace tools; // Lowercase, single word
namespace rhi; // "rendering hardware interface" acronym, keep lowercase and concise
```

### 4. Functions

All functions (regular functions, static, member, template, etc.) use all lowercase with words separated by underscores:

```cpp
void my_func(){ }
```

Except for function templates, the `auto fun()-> type {}` format is **not recommended**.

### 5. Class Names

All class names (regular classes, template classes, enums, abstract classes, etc.) use "PascalCase" with the first letter of each word capitalized and the rest lowercase:

```cpp
class MyClassName{};
class RHIClass{}; // For acronyms, keep all letters uppercase
```

If it's a pure virtual class or a type explicitly used as an interface, add the `I` prefix:

```cpp
class IMyInterface{};
```

Enum classes tentatively do not need the `E` prefix and are similar to regular class names, but enum values need special naming (refer to the enum values section below).

```cpp
enum class MyEnumClass{};
```

### 6. Enum Values

Enum values, as a special type, start with `e` and use "camelCase" style. However, due to the `e` prefix, the first word also needs to be capitalized.

```cpp
enum class JsonType{
    eNull,
    eString,
    eObject,
    eArray,
    eIntOrFloat, // For demonstration only, use eNumber in actual projects
    eTrueOrFalse // For demonstration only, use eBool in actual projects
};
```

### 7. Local Variables

Local variables refer to variables defined within function scope. Use all lowercase with underscores separating words.

```cpp
void func() {
    int counter = 1;
    int func_index = 2;
}
```

### 8. Member Variables

Non-static member variables of classes should use the `m_` prefix, maintaining all lowercase with underscores:

```cpp
class MyClass{
    int m_index;
    std::string m_object_name;
}
```

### 9. Static Variables

If a variable is modified with `static`, use the `s_` prefix, whether it's a class member or function local:

```cpp
class MyClass{
    static std::string s_class_name;
};
int func(){
    static int s_func_counter = 0;
    return ++s_func_counter;
}
```

Specifically, if static variables are also modified with `const` or `constexpr`, refer to the constants below and keep them in uppercase:

```cpp
class MyClass{
    const static std::string S_CLASS_NAME;
};
```

### 10. Constants

"Constants" here refer only to constants with global or static lifetime, excluding macro definitions or temporary constant variables within functions.

Constant identifiers should be in all uppercase with words separated by underscores.

```cpp
constexpr int BOOL32_FALSE = 0; // Global scope

void func() {
    static const std::string S_FUNC_NAME{ "void func()" }; // Static lifetime

    const std::string str{ "Hello world!" }; // Temporary immutable objects are treated as regular variables
}
```

### 11. Concepts

C++20 concepts use lowercase letters separated by underscores.

```cpp
template<typename T>
concept json_types = 
    std::is_same_v<T, std::nullptr_t> || 
    std::is_same_v<T, bool> || 
    std::is_same_v<T, double> || 
    std::is_same_v<T, std::string> || 
    std::is_same_v<T, Object> || 
    std::is_same_v<T, Array>;
```

### 12. Comments

Documentation comments use javadoc style:

```cpp
/**
 * @brief .....
 * @xxx .....
 */
```

For class members that don't require extensive comments, use the simplified `///<` format:

```cpp
enum class JsonType{
    eObject, ///< JSON object type, an unordered collection of key-value pairs
    eArray  ///< JSON array type, an ordered list of values
};
```

## **Line Breaking Guidelines**

Indentation uses 4 spaces, do not use tabs.

### 1. Scope Formatting

The `{` for functions, for/if statements should not be on a new line:

```cpp
void func() { // Opening brace does not start a new line

}
```

For if branches and try-catch structures, use compact formatting:

```cpp
if (1) {

} else if (2) {

} else {

}

try {

} catch( ... ) {

}
```

### 2. Parameter Line Breaking

When function or if statement parameters are too long, they need to be broken into multiple lines:

```cpp
void func(
    int attr1,  // Class names and variable names should be left-aligned
    int attr2,
    int attr3,
    int attr4
) { // ) { occupies its own line, member functions' ) const { is also on one line
    ...
}
```

Template parameters with excessive length are similar to functions:

```cpp
template<typename T>
concept json_types = std::disjunction_v<
    std::is_same<T, std::nullptr_t>,
    std::is_same<T, bool>,
    std::is_same<T, double>,
    std::is_same<T, std::string>,
    std::is_same<T, Object>,
    std::is_same<T, Array>
>;
```

### 3. Method Chaining

For method chaining, if the code is short, it can be placed on one line. Otherwise, each call should be on a new line.

```cpp
vk::CreateInfo info;
info.setAttr0( 0 )
    .setAttr1( 1 )
    .setAttr2( 2 )
    .setAttr3( 3 );
```

### 4. Switch Blocks

It is recommended that `case` has one more indentation than `switch`, the opening brace `{` is on the same line as `case`, and `break` is close to the closing brace `}`.

```cpp
switch (1){
    case 2: break;
    case 3: {
        int tmp = 3;
        std::cout << tmp;
    } break;
    default:
        std::cout << "default";
        break;
}
```

</div>

---

