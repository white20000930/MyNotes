

[Mac 键盘快捷键 - 官方 Apple 支持 (中国)](https://support.apple.com/zh-cn/102650)

# 编程规范

[Google编程规范 链接]([Google 开源项目风格指南——中文版 — Google 开源项目风格指南 (zh-google-styleguide.readthedocs.io)](https://zh-google-styleguide.readthedocs.io/en/latest/))

## 头文件

### #define保护

头文件的命名应该基于所在项目源代码树的**全路径.** 

例如, 项目 `foo` 中的头文件 `foo/src/bar/baz.h` 可按如下方式保护:

```c++
#ifndef FOO_BAR_BAZ_H_
#define FOO_BAR_BAZ_H_
...
#endif // FOO_BAR_BAZ_H_
```

### 前置声明

尽可能避免

### #include 的路径及顺序

标准的头文件包含顺序：

1. 相关头文件
2. C 库
3. C++ 库
4. 其他库的 .h
5. 本项目内的.h，项目内头文件应按照源代码目录树结构排列,`#include "base/logging.h"`

同类型的按照字母顺序排序

每个某个头文件的文件都要包含，比如两个文件用到`bar.h`，都要包含一次

条件编译相关代码放在include之后

## 作用域

### 全局函数

使用

1. 静态成员函数
2. 命名空间内的非成员函数

**不要使用裸的全局函数**

不要用类的静态方法模拟出命名空间的效果，类的静态方法应当和类的实例或静态数据紧密相关.

### 局部变量

有一个例外, 如果变量是一个对象, 每次进入作用域都要调用其构造函数, 每次退出作用域都要调用其析构函数. 这会导致效率降低.

在循环作用域外面声明这类变量要高效的多:

```c++
Foo f; // 构造函数和析构函数只调用 1 次
for (int i = 0; i < 1000000; ++i)
{
    f.doSomething(i);
}
```

### 静态全局变量

不要定义静态存储周期的非pod变量

静态生存周期的对象，即包括了全局变量，静态变量，静态类成员变量和函数静态变量，都必须是原生数据类型 (POD : Plain Old Data)

**只允许 POD 类型的静态变量，即完全禁用 `vector` (使用 C 数组替代) 和 `string` (使用 `const char []`)。**

## 类

### 隐式类型转换

不要定义隐式类型转换，对于转换运算符和单参数构造函数，使用`explicit`关键字

```c++
class MyInt {
public:
    MyInt(int value) : value(value) {}

    explicit operator int() const { return value; }  // 使用 explicit 关键字防止隐式类型转换

private:
    int value;
};

int main() {
    MyInt mi(42);

    // int i = mi;  // 这行代码会报错，因为类型转换运算符被声明为 explicit

    int i = static_cast<int>(mi);  // 这样是可以的，因为这是显式类型转换

    return 0;
}
```

### 可拷贝类型和可移动类型

拷贝操作，方式：拷贝构造函数 或 拷贝复制操作符

移动操作：移动构造函数，移动赋值操作符

如果需要, 就让它们支持拷贝 / 移动的两种方式，

否则, 就把隐式产生的拷贝和移动函数禁用。

### 结构体和类

仅当只有数据成员时使用 `struct`, 其它一概使用 `class`.

### 继承

使用组合 (注: 这一点也是 GoF 在 *Design Patterns* 里反复强调的) 常常比使用继承更合理. 如果使用继承的话, 定义为 public 继承.

### 存取控制

将 *所有* 数据成员声明为 `private`, 除非是 `static const` 类型成员 (遵循 常量命名规则 ). 

### 声明顺序

将类似的声明放在一起, 并且建议以如下的顺序：

类型 (包括 `typedef`, `using` 和嵌套的结构体与类), 常量, 工厂函数, 构造函数, 赋值运算符, 析构函数, 其它函数, 数据成员

## 函数

### 参数顺序

输入参数先，输出参数后

### 编写简短函数

函数超过 **40 行** , 可以思索一下能不能在不影响程序结构的前提下对其进行分割.

### 引用参数

所有按引用传递的参数必须加上 `const`，防止修改参数

函数参数列表中, 所有引用参数都必须是 `const`:

```c++
void Foo( const string &in, string *out);
```

输入参数是**值参**或 **`const` 引用**, 输出参数为指针. 输入参数可以是 `const` 指针, 但决不能是非 `const` 的引用参数, 除非特殊要求, 比如 `swap()`

有时候, 在输入形参中用 `const T*` 指针比 `const T&` 更明智. 比如:

- 可能会传递空指针.
- 函数要把指针或对地址的引用赋值给输入形参.

### 函数重载

若要使用函数重载, 则必须能让读者一看调用点就胸有成竹, 而不用花心思猜测调用的重载函数到底是哪一种

### 缺省参数

缺省参数与 函数重载 遵循同样的规则. **相比缺省参数，一般情况下建议使用函数重载**, 尤其是在缺省函数带来的可读性提升不能弥补下文中所提到的缺点的情况下

虚函数不允许使用缺省参数

## 命名约定

（不同于Google C++ Code Style）

### 表格总结

总结如以下表格

| 类型                                               | 命名风格      |
| :------------------------------------------------- | :------------ |
| 类类型，结构体类型，枚举类型，联合体类型等类型定义 | 大驼峰        |
| 函数(包括全局函数，作用域函数，成员函数)           | 小驼峰        |
| 局部变量，函数参数，结构体和联合体中的成员变量     | 小驼峰        |
| 类的成员变量                                       | m+大驼峰      |
| 全局变量(包括全局和命名空间域下的变量，类静态变量) | g_开头小驼峰  |
| 常量(const)                                        | k+大驼峰      |
| 宏，枚举值                                         | 大写 + 下划线 |
| 命名空间                                           | 全小写        |

小驼峰：第一个单词，首字母小写。后面单词，首字母大写

大驼峰：每个单词，首字母大写

### 文件命名

**大驼峰**

不要使用已经存在于 `/usr/include` 下的文件名 (注: 即编译器搜索系统头文件的路径), 如 `db.h`.

### 类型命名

类, 结构体, 类型定义 (`typedef`), 枚举, 类型模板参数

**大驼峰**，类型名称的每个单词首字母均大写, 不包含下划线: `MyExcitingClass`, `MyExcitingEnum`.

### 变量命名

变量 (包括函数参数) 和数据成员名均采用**小驼峰**命名法命名

类的成员变量以m+小驼峰命名法，类静态变量采用g+小驼峰命名法

### 常量命名

声明为 `constexpr` 或 `const` 的变量, 或在程序运行期间其值始终保持不变的, 命名时以 “k” 开头, 大小写混合. 例如:

```
const int kDaysInAWeek = 7;
```

### 函数命名

**小驼峰**

`addTableEntry()`，`deleteUrl()`，`openFileOrDie()`

取值和设值函数的命名与变量一致.

例如 `void setCount(int count)`

### 枚举命名

**枚举名采用大驼峰**的方式，**枚举值采用常量**的方式（k+大驼峰）

```
enum UrlTableErrors {
  kOK = 0,
  kErrorOutOfMemory,
  kErrorMalformedInput,
};
```

### 宏命名

**通常 不应该 使用宏**. 如果不得不用, 其命名全部大写, 使用下划线

## 注释

### 文件注释

在每一个文件开头加入版权公告。

如果一个文件只声明, 或实现, 或测试了一个对象, 并且这个对象已经在它的声明处进行了详细的注释, 那么就没必要再加上文件注释. 除此之外的其他文件都需要文件注释。

### 类注释

每个类的定义都要附带一份注释, 描述类的功能和用法

### 函数注释

函数声明处的注释描述函数功能; 定义处的注释描述函数实现.

**注释使用叙述式 (“Opens the file”)** 而非指令式 (“Open the file”)

函数声明处可以注释的内容:

- 函数的输入输出.
- 对类成员函数而言: 函数调用期间对象是否需要保持引用参数, 是否会释放这些参数.
- 函数是否分配了必须由调用者释放的空间.
- 参数是否可以为空指针.
- 是否存在函数使用上的性能隐患.
- 如果函数是可重入的, 其同步前提是什么

**但，要避免罗罗嗦嗦，对于显而易见的内容没必要说明**

### 变量注释

通常变量名本身足以很好说明变量用途. 某些情况下, 也需要额外的注释说明。

#### 类数据成员

每个类数据成员都应该用注释说明用途

特别地, 如果变量可以接受 `NULL` 或 `-1` 等警戒值, 须加以说明. 比如:

```c++
private:
  // Used to bounds-check table accesses. -1 means
  // that we don't yet know how many entries the table has.
  int numTotalEntries;
```

### 全局变量

所有全局变量也要注释说明含义及用途, 以及作为全局变量的原因.

## 格式

### 行长度

每一行代码字符数<80











ffplay -f f32le -ar 48000 /Users/baigege/Public/ffmpeg_demo/outpcm.pcm

ffplay -f rawvideo -pixel_format yuv420p -video_size 1920x1080