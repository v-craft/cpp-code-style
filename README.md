<div align="center">

<h1>V-Craft: C++ Code Style Guide</h1>

## **命名风格**

英文单词命名，语义化，简洁明了。

> 除人名等特定场景，严禁中文拼音与拼音缩写。

### 1. 文件名

使用小写单词，下划线分隔。头文件后缀使用 `.hpp` ，源文件使用 `.cpp` ，模块接口文件使用 `.ixx` 。

```
main.cpp                # 普通源文件与模块实现文件
test_unit.ixx           # 模块接口文件
test_unit_macros.hpp    # 头文件
```

如果头文件仅含宏定义，请包含 `macros` 后缀。

### 2. 项目文件夹结构

提供两种方案：

1. `A.ixx` 为接口文件， `A.cpp` 为实现文件，同目录下 `A` 文件夹存放子模块文件。

2. `A` 文件夹存在 A 模块的所有文件，`A/mod.ixx` 存放模块接口文件，`A/impl.cpp` 存放实现文件。

方案 1 适用于文件较少时，比如仅一个接口文件（实现代码也写在接口中）。
方案 2 适用于文件较多且多层级时，比如有接口、实现，还有宏 `A/macro.hpp` 。

### 3. 宏定义

宏定义保持全大写，用下划线分隔单词，并以 `M_` 开头（表示是`macro`）。

```cpp
#define M_BOOL32_FALSE 0
#define M_BOOL32_TRUE 1
#define M_ADD(x, y) x+y
```

如果是仅头文件内部使用的宏定义（比如防止重复包含头文件的宏），可加上 `_` 前缀：

```cpp
#define _M_XXX_HPP
```

### 4. 命名空间

命名空间使用全小写，单层命名空间尽量单个单词，多单词时建议使用首字母小写缩写。

```cpp
namespace tools; // 小写，单个单词
namespace rhi; // rendering hardware interface 小写缩写
```

### 5. 函数

所有函数都使用全小写，单词之间使用下划线隔开：

```cpp
void my_func(){ }
```

除函数模板外，**不推荐**使用 `auto fun()-> type {}` 格式。

> 此格式存在部分兼容性问题。

### 6. 类名

所有类名都使用“大驼峰”写法，单词首字母大写，其余部分小写：

```cpp
class MyClassName{};
class RHIClass{}; // 缩写首字母时，保证全大写
```

如果是纯虚类，或明确用作接口的类型，可添加 `I` 前缀：

```cpp
class IMyInterface{};
```
 
枚举类暂定不需要 `E` 前缀，形似常规类名。

```cpp
enum class MyEnumClass{};
```

### 7. 枚举值

枚举值作为特殊类型，以 `e` 开头，使用“小驼峰”风格。

> 由于 `e` 开头，第一个单词也需要首字母大写。

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

### 8. 静态变量

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

### 9. 成员变量

类的非静态成员变量请使用 `m_` 前缀，依然保持全小写和下划线分隔：

```cpp
class MyClass{
    int m_index;
    std::string m_object_name;
}
```

### 10. 全局变量与临时变量

此处变量仅指函数局部定义的变量，请使用全小写，下划线分隔。

```cpp
void func() {
    int counter = 1;
    int func_index = 2;
}
```

全局的非常量非静态变量，命名等同于临时变量。


### 11. 常量

此处的“常量”仅指全局或静态生命周期的常量，不含宏定义或函数局部的临时常变量。

常量的标识符字面应全大写，单词之间使用下划线隔开。

```cpp
constexpr int BOOL32_FALSE = 0; // 全局作用域

void func() {
    static const std::string S_FUNC_NAME{ "void func()" }; // 静态生命周期

    const std::string str{ "Hello world!" }; // 临时不可变对象等同普通变量
}
```

### 12. 概念

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

## **缩进建议**

缩减使用 4 空格，请勿使用制表符。

### 1. 作用域写法

函数、for、if 等语句的左大括号 `{` 不换行：

```cpp
void func() { // 左括号不起新行

}
```

if 分支和 try-catch 等结构，使用紧凑的写法：

```cpp
if (1) {

} else if (2) {

} else {  // 不起新行

}

try {

} catch( ... ) {

}
```

### 2. 参数换行

函数或 if 的语句的参数过长时，可用多行表示：

```cpp
void func(
    int attr1, // 另起一行，带一次缩进
    int attr2,
    int attr3,
    int attr4
)  noexcept { // `) ... {` 独占一行
    ...
}
```

模板的过长参数与函数类似：

### 3. 链式调用

对于链式调用，如果代码很短，可以放于一行。否则，每次调用都需要换行。

```cpp
vk::CreateInfo info;
info.setAttr0( 0 )
    .setAttr1( 1 )
    .setAttr2( 2 )
    .setAttr3( 3 );
```

### 4. switch

`case` 比 `switch` 多一次缩进，左大括号 `{` 与 `case` 同行， `break` 紧贴右大括号 `}` 。


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


