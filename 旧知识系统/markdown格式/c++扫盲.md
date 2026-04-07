## 画布 1: c++扫盲

# c++扫盲

- std库及常用类
  - content
    - std::reinterpret<char*>(&p)
      - def
        - 将对象的内存表示重新解释为字符数组
      - core
        - &p（可能是别的类型） -> char*
    - std::memcpy
    - std::for_each(起始指针，终止指针，函数对象)
  - 模块
    - <algorithm>
    - <map>
      - def
        - 映射类型
      - content
        - 初始化
        - 访问元素
    - <memory>
    - <string>
      - string相关的操作
        - C风格字符串
          - 初始化方式
            - char str1[] = "Hello";  // 字符数组
            - const char* str2 = "World";  // 字符串字面量
          - 字符串长度
            - strlen
          - 复制
            - strcpy(dest, src)
              - 要注意是dest在前，src在后
          - 连接
            - strcat(dest, src)
              - 将src追加到dest的末尾
          - 比较
            - strcmp(str1, str2)
          - 查找子串
            - strstr(str1, substr)
        - C++风格字符串
          - 构造
            - std::string str = "..."
          - 基本操作
            - .size()
              - 长度
            - .length()
              - 长度
            - .empty()
              - 是否为空
            - .clear()
              - 清空字符串
          - 访问元素
            - str[0]
              - 访问字符
            - str.at(0)
              - 更安全地访问字符
          - 修改操作
            - .append()
              - 等价于 +=
            - .insert(position, "content")
              - 在索引position的地方进行插入
              - 相当于在原position的位置左插值
or 
position-1的位置右插值
          - 查找
            - .find(“...”)
              - 查找子串，返回前索引
            - str.find_first_of("aeiou");      // 查找字符集合
              - 细分主题 1
            - str.find_last_of("aeiou");
          - 子串
            - std::string sub = str.substr(0, 5);
          - 比较
            - str.compare("Hello World");      // 比较字符串
            - str == "Hello";
          - 转换
            - const char* cstr = str.c_str();  // 转C风格字符串
            - const char* data = str.data();   // 获取数据指针
            - std::string numStr = std::to_string(123);  // 数字转字符串
            - int num = std::stoi("123");      // 字符串转整数
            - double d = std::stod("3.14");    // 字符串转浮点数
          - 遍历
            - for (char c : str) {
    std::cout << c;
}
            - for (auto it = str.begin(); it != str.end(); ++it) {
    std::cout << *it;
}
        - 字符串视图
        - 字符串流（#include <sstream>）
          - 字符串分割
            - std::stringstream ss("apple,banana,orange");
std::string token;
while (std::getline(ss, token, ',')) {
    std::cout << token << std::endl;
}
              - 需要使用的
                - stringstream
                - std::getline(stringstream, token, "parameter")
// 其中token是一个string对象
                - while循环判断getline函数返回值
          - 格式化字符串
            - std::stringstream fmt;
fmt << "Value: " << 42 << ", PI: " << 3.14;
std::string result = fmt.str();
              - 需要使用的
                - stringstream.str()
                - stringstream对象的流式输入
        - 正则表达式
        - 常用算法
    - <vector>
      - vector.begin() -> vector::iterator(类似于指针，可看作指针)
  - 常用类
    - 容器类
      - 数组
        - 初始化
          - 全初始化为某个值
        - 访问和遍历
        - 数组复制
        - 数组排序
          - 前两个变量
            - 细分主题 1
          - 第三个变量：标志函数的解析
            - 返回 true：第一个参数应该在第二个参数之前
            - 返回 false：第一个参数不应该在第二个参数之前（可能相等或之后）
          - 注意
            - sort函数是对原列表进行排序，会修改src的值
      - vector类
- 关键字
  - explicit
    - core
      - 针对构造函数
只能显式调用
    - 特例
      - explicit operator bool() const {  // 防止意外的 bool 转换
        return value;
    }

// if(bw)  // 错误！需要显式转换
if(static_cast<bool>(bw))  // OK - 显式转换
// int x = bw;  // 错误！不能隐式转换为 int
    - exp
      - Mystring ts = 10是不合理的构造函数
      - Mystring ts = Mystring(10);才是合理的，这叫显式调用
  - typedef
    - typedef 原始类型 新类型名
  - export - import
    - 使用方法
（针对mymodule.cppm文件）
      - 导出
        - export module MyModule;  // 声明这是一个模块
        - // 导出函数
export int add(int a, int b) {
    return a + b;
}
        - // 导出类
export class MyClass {
public:
    void doSomething();
};
        - // 导出变量
export const double PI = 3.14159;
      - 导入
  - template
    - example
      - template<
    class Key,
    class T,
    class Compare = std::less<Key>,
    class Allocator = std::allocator<std::pair<const Key, T>>
> class map;
      - class = typename = 模板参数（需要传入具体值）
  - boolalpha
    - example
      - cout << boolalpha << ......;
    - def
      - 用true/ false代替1/0
      - 流控制字符
  - inline
    - def
      - inline要和别的组合使用
    - exp
      - 在命名空间中将其某个子命名空间设置为默认命名空间
      - 内联函数
        - 类似于宏替换
        - 不允许循环/不允许过长的代码
        - 只是建议
      - 内联静态成员
        - 可以在类内部初始化静态成员
        - inline static int count = 0;
  - typeid
    - def
      - 返回const type_info&类型的变量
      - 内部有.name()可以判断name
      - 支持==判断
  - noexcept
    - def
      - 确保函数不抛异常
  - new - malloc
    - c++风格
      - new
        - int *p1 = new int; // 未初始化
        - int *p2 new int(42); // 初始化为42，或者使用列表初始化
      - delete
        - delete p1
        - delete[] arr1
    - c风格(#include <cstdlib>)
      - malloc
        - def
          - 分配未初始化的内存
        - w2u
          - int* p1 = (int*)malloc(sizeof(int));
      - calloc
        - def
          - 分配全被初始化为0的内存
      - free
        - def
          - 释放内存
  - const
    - w2u
      - int getID() const
        - 常成员函数，不可以修改成员变量
        - 相当于给this指针加了const
        - 设置常成员函数和普通同名函数重载的原因是，
支持const对象调用
      - 指针的const
        - const int (*p1)
          - const的是int，也就是数据本身
        - int (const *p1)
          - const的是*，也就是指针
- 语法
  - 文件操作
    - 核心类
      - ifstream
(input file stream)
      - ofstream
      - fstream
    - func
      - 基本操作流程
        - 打开文件
          - ifstream inFile
inFile.open()
          - (另一种)
ifstream inFile(...)
        - 检查是否打开
          - .is_open()
        - 打开模式
          - location
            - open()的第二个参数
          - w2u
            - 按位或：|
          - content
            - ios::out
              - 写入内容
            - ios::trunc
（truncate）
              - 清空内容
            - ios::app
              - 追加到末尾
            - ios::binary
              - 二级制模式
            - ios::in
              - 读取内容
          - 组合
      - 文本文件操作
        - 写入数据
          - ofstream outFile(...)
if (outFile) {
outFile << ...
}
outFile.close()
        - 读取数据
          - ifstream inFile(...)
string line
// 逐行读取
while (getline(inFile, line)) {
cout << line
}
// 逐词读取
while (inFile >> word) {
...
}
//逐字符读取
char ch
while (inFile.get(ch)) {
...
}
inFile.close()
        - 检查文件状态
          - .good()
          - .fail()
          - .eof()
            - 是否到达文件末尾
          - .clear()
            - 重置状态标志
      - 二级制文件操作
        - 写入
          - ofstream outFile(..., ios::binary);
if (ofstream) {
outFile.write(reinterpret<char*>(&p), sizeof(Person)) // Person是一个结构体
outFile.close()
}
        - 读取
          - Person p
inFile.read(...) (与写入相同)
      - 文件定位
        - forward
          - fstream file("test.txt", ios::in | ios::out);
        - 获取当前文件位置
          - streampos pos = file.tellg()
        - 移动文件读/写指针
          - 绝对
            - 读
              - file.seekg(128, ios::beg)
// beg: begin
            - 写
              - file.seekp(......)
          - 相对
            - 读
              - ios::beg -> ios::cur
// cur: current
            - 写
          - 移动到末尾
            - ios::end
  - 运算符
    - content
      - 逻辑运算符：针对布尔值（隐式转换）
（记忆：与位运算符的简洁形成对比）
        - &&
        - ||
        - !
      - 位运算符：整型数据的二级制位
        - &
        - |
        - ^
        - ~
        - <<
        - >>
      - 逗号运算符
      - ”子“运算符
        - .
        - ->
        - ::
      - 类型转换运算符
        - (type)value
        - static_cast<type>(value)
    - OTH
      - 短路求值
        - def
          - a&&b
            - a为0的时候，不执行b
          - a||b
            - a为1的时候，不执行b
        - 用途
          - 只有a对，才执行b的场景
  - 结构体成员初始化
    - 设A为结构体类
属性：b, c
    - 初始化方法
      - A a_0 = {
.b = xxx,
.c = xxx
};
        - 否则为NULL
      - A a_0 = {
xxx,
xxx
};
        - 必须全给出
  - C++11统一初始化
    - def
      - 默认值+花括号（部分或全部属性）
  - 使用::v访问全局变量v（作用域分辨符）
  - 引用类型
    - example
      - type& xxx = yyy
        - 相当于给yyy起别名：xxx
  - 左值 - 右值
    - 左值引用
      - exp
        - int a = 1;
int& b = a;
      - core
        - 创建别名
    - 右值引用
      - exp
        - func(int&& a) {
a = 2;
cout<<a;
}

func(3);
      - core
        - 将右值变成左值，延续右值的生命周期
    - 左值
      - def
        - 有身份（有地址）的表达式
      - ftr
        - 有名字的变量
        - 能取地址（&操作符）
        - 通常在等号左边
        - 生命周期持续到作用域结束
    - 右值
      - def
        - 可移动的表达式（通常是临时的，或是被标记为可移动的）
      - ftr
        - 临时对象
        - 字面量（除了字符串字面量）
        - 不能取地址
        - 即将销毁的对象
        - 使用std::move转换后的对象
    - 函数参数传递方式
      - func(int& a)
        - a可以是右值也可以是左值
      - func(const int& a)
        - a只能是左值，a不可变
      - func(int&& a)
        - a只能是右值
  - 初始化
    - 变量初始化方式
      - int a(0)
      - int a{0}
      - char a{'A'}
      - char a[20]{'hello!'}
    - 结构体/类的初始化
      - #include <memory>
auto a = std::make_unique<cls>(args);
// or make_shared<...>(...)
      - 少用new
  - 枚举
    - exp
      - 定义枚举
        - enum SHAPE {CIRCLE, RECT, ...}
      - 使用枚举（定义枚举变量）
        - enum SHAPE myshape = CIRCLE
        - 实际上myshape就是0
    - 用处
      - exp
        - switch (SHAPE)
{
case CIRCLE:
...;
break;

case ...
...
break;

......

default:
...
break;
}
  - 进制表示
    - 十进制
      - int a = 520
    - 八进制
      - int oct = 01010
    - 16进制
      - int hex = 0x208
    - 2进制
      - int bin = 0b1000001000
      - 可以用单引号做分隔符
  - string类型操作
    - 字符串连接
      - a += b
    - 属性
      - .length() = .size()
      - .data() = .c_str()
        - 对应的类型是const char*
    - 字符串转换
      - to_string()
  - 虚析构函数
    - 细分主题 1
  - 构造函数
    - 拷贝构造函数
      - exp
        - Number(const Number& other){
m_num = other.m_num;
}
          - 也就是，传入的值是类本身的构造函数就是拷贝构造函数
    - 委托构造
      - def
        - 使用
      - exp
        - Stu(const char* name){...}

Stu(int age) : Stu("null"){...}
  - 深拷贝
    - 智能指针相关
      - exp
        - 假设由智能指针创建的对象a
          - a.data.get()
            - a对象 -> a的智能指针 -> a的原始指针
    - 实现方式
      - std::copy
        - 使用方式
          - std::copy(源对象起始地址，源对象结束地址，目标对象的智能指针)
  - 友元函数
    - w2u
      - 声明友元函数
        - 在类的私有属性中声明：friend void aaa(type& obj)
        - 在类外定义，并且可以使用类的私有成员
      - 声明友元类
        - 在类的私有属性中声明：friend class cls;
  - 函数指针
    - def
      - 将函数名抽象为指针，动态传递函数，同时保持参数声明，以达到回调函数等效果
    - w2u
      - 作为函数的参数，成为其回调函数
      - int (*funcptr)(int, int)
funcptr = &add
result = funcptr(3, 4)
      - typedef int (Mathfunc)(int, int);
Mathfunc funcptr = add;
      - using Mathfunc = int (*)(int, int);
Mathfunc funcptr = add;
      - std::function<int(int, int)> func = add;
// 也可存储lambda表达式
        - add 首先会隐式转换为函数指针（int(*)(int, int)）
        - 这个函数指针被用来构造 std::function 对象
        - std::function 内部存储了这个函数指针（或它的拷贝）
    - OTH
      - 普通函数指针
        - 声明：void (*pdog)(Dog&);
        - 定义：pdog = dog_bark();
        - 调用：pdog();
      - 类的成员函数指针
        - 声明：void (Dog::*pb)();
        - 定义：pb = &Dog::bark;
        - Dog d;
调用：(d.*pb)();
  - 运算符重载
    - 语法
      - type class::operator op (args)
    - 前置/后置++的区别
      - Cls& operator++()是前置++
        - 前置++直接把值+1，再返回对象的引用（*this）
      - Cls operator(int)是后置++
        - 后置++一般是先拷贝一份*this的副本t（Int t = *this）， 并把原来的对象的值+1， 然后把t返回
  - 动态分配
    - def
      - 分配在堆上，程序运行期间永久存储
  - 流运算符重载
    - exp
      - 实现cout重载
        - 友元函数声明（类内部）：
friend ostream& operator<<(ostream& os, const Dog& d);
函数定义（重载，类外部）
ostream& operator<<(ostream& os, const Dog& d) {
os << d.getName;
return os;
}
  - 用户自定义字面量
    - grammar
      - 返回值类型 operator"" _后缀(参数类型);
    - exp
      - constexpr long long operator"" _km(unsigned long long meters) {
    return meters * 1000;
}
  - CRTP（Curiosity Recurring Tempelate Pattern）
    - def
      - 一种模板编程技巧，主要用于编译期多态和静态多态。
      - CRTP的核心思想是：一个类继承自一个模板类，而该模板类的模板参数是这个派生类本身。
    - words
      - 模板类
        - exp
          - 1
      - 派生类
        - def
          - 在C++中，派生类（Derived Class） 是从另一个类（称为基类/父类）继承而来的类。继承是面向对象编程的核心概念之一，允许派生类复用、扩展或修改基类的特性。
  - 面向对象4大特性
    - 抽象
      - def
        - 抽象是从现实世界中提取出我们关心的属性和行为，忽略不相关的细节，从而创建出一个可操作的软件模型。
        - 抽象方法只声明做什么，而不指定怎么做
        - 只暴露必要的接口
    - 封装
      - def
        - 组合属性方法：将相关的属性和方法组合成一个类（封装成一个整体）
        - 信息隐藏：隐藏对象的内部实现细节，只通过公共接口与外界交互
        - 数据保护：通过访问修饰符（private/protected/public）控制对数据的访问
    - 继承
    - 多态
      - def
        - 多态是同一操作作用于不同的对象，可以有不同的解释和执行结果。
      - type
        - 编译时多态（方法重载）
        - 运行时多态（方法重写+继承） - 这是你提到的部分
  - 继承相关
    - 在继承关系中
      - 会先调用父类的构造函数，再调用子类的构造函数
      - 先调用子类的析构函数，在调用父类的析构函数
      - 如果子类存在类的组合，那么会先调用父类的构造函数，再依次执行组合类的构造函数，再去执行子类的构造函数
    - 多继承
      - 细分主题 1
    - 虚继承
      - core
        - 避免二义性
  - 多态相关
    - 类型兼容原则
      - def
        - 在需要父类对象的任何地方，都可以使用公有派生类的对象来替代。
      - content
        - 子类对象可以当作父类使用
          - showMoney(Father f)可以传入Father类，也可以传入Child类
        - 子类对象可以直接赋值给父类对象
          - Father f;
Child c;
f = c;// 是可以的
        - 子类对象可以直接初始化父类对象
          - Father f(c); //是可以的
        - 父类指针可以直接指向子类对象
          - Animal* animalPtr = new Dog();
animalPtr->speak();  // 输出 "Woof!"（多态）
          - 指针从父类扩大到了子类
        - 父类引用可以直接引用子类对象
          - Dog dog;
Animal& animalRef = dog;
animalRef.speak();   // 输出 "Woof!"
    - 静态成员在父类中初始化，在所有子类中共享
    - OTH
      - 如果父类和子类拥有相同的成员，那么优先调用本类的成员
      - 如果一定要调用父类的同名成员，使用::访问
        - 例如在子类中使用Father::_money
- 基础知识
  - 数据类型
    - 基本类型
      - 整型
        - 短整型
        - 整型
        - 长整型
      - 字符型
        - char
      - 浮点型
        - 单精度
        - 长双精度
      - 布尔型
        - bool
    - 构造类型
      - 枚举类型
      - 数组类型
      - 结构体类型
      - 共用体类型（union）
      - 类 类型
    - 指针类型
    - 引用类型
    - 空类型
  - 基本运算符
    - 算术
      - +-*/
      - 正负号
      - %
      - ++，--
    - 赋值
    - 比较
    - 逻辑
    - 位
    - 杂项
    - 运算符优先级

## 画布 2: sth

# ACM中C++

- 输入与输出
  - C风格
    - scanf和printf
      - 功能
        - 输入
          - %d;%s;%f;%ld
        - 输出
  - C++风格
    - 前置设置
      - ios::sync_with_stdio(false);
cin.tie(nullptr);
    - 功能
      - 输入
        - 获取行
          - string line;
(or char line[256];)
cin.getline(line, 256);
        - 获取单词
          - string a;
cin >> a;
        - 获取单个字符
          - char a;
cin >> a;
      - 输出
        - 将endl换成\n
