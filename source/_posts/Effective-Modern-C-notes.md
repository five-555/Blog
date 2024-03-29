---
title: Modern C++学习笔记
categories: 学习笔记
date: 2023-08-15 13:14:50
tags: [C++, C++新特性]
cover:
top_img:
---
## Effective Modern C++学习笔记

> 重点掌握auto、智能指针、移动构造、lambda

### 第一章、类型推导

#### 条款1、模板类型推导

```
情况一、按值传递，传入副本
template<typename T>
void f(T param)

情况二、引用传递，传入地址
template<typename T>
void f(const T& param)

情况三、指针传递
template<typename T>
void f(T* param)

万能引用，即也可以引用右值
情况四、万能引用，如果是右值，则传入的是右值的地址
template<typename T>
void f(T&& param)
```



> 当向引用型别的形参传入const对象时，他们期望该对象保持其不可该修改的属性，即const引用
>
> 因此向持有T&型别的模板传入const对象时安全的

* 当使用万能引用时，型别推导规则会区分实参是左值还是右值，如果是左值就是单个引用情况“&”，如果是右值则是“&&”情况

* 如果是按值传递，则传入的是一个副本，无论做什么操作都无法改变原始的数据

```
const char name[] = "sda"
const char* ptrToName = name

template<typename T>
void f(T param)
f(name)

T会推到成const char*的类型
```

#### 条款2、auto类型推导

> auto类型推导就是模板类型推导，当某一个变量采用auto来声明时，auto则扮演模板中的角色，通过表达式的右边的值推出左边。

C++14允许auto来说明函数返回值需要推导

#### 条款3、decltype

decltype可以声明返回值型别依赖于形参性别的函数模板，因为函数模板的数据类型不确定，通过使用decltype引用一个模板类型的数据，可以推出该函数模板的类型，通过尾序返回值可返回该数据类型的结果。

在对万能引用进行推导时，返回值需要加上std::forward



### 第二章、auto

> 优先使用auto定义，而非使用显式定义，可以防止对象忘记初始化的错误。

auto优点

* 1、避免初始化变量和冗余的变量声明（如，迭代器声明）
* 2、在不同位的操作系统移植过程中可以避免参数位数表达不一致的问题

auto不能用在vector< bool >中，在单个返回operator[]时返回的是bool的引用。



### 第三章——模板还是有点晦涩难懂

#### 条款7、创建对象注意区分（）和{}

```
// 使用大括号初始化容器会强烈优先选择带有std::initializer_list类别形参的构造函数
vector<int> v{1, 3, 5}
```

空大括号表达的是没有形参，如果使用list的话，应该在小括号在再加一个大括号。

#### 条款8、优先使用nullptr

作为指针传入参数时，传入空指针优先使用nullptr，因为nullptr指代明确，不像0和NULL既可以指代整型也可以指代空指针。

nullptr可以隐式的转化为所有指针类型。

#### 条款9、优先使用别名声明，而非typedef---------回头看

using作用可以等效于typedef用于别名的声明，使用using的优势在于，可以将其与模板进行结合起来，别名模板。



#### 条款10、优先使用限定作用域的枚举

> enum class name{}；使用class关键词限定作用域，又称为枚举类。

使用枚举的成员需要用到`name::`来取。

* 使用限定作用域的枚举的枚举量是更强型别的，不限范围的枚举型别中的枚举量可以隐式转化到整数类别。
* 强类型的参数不能够和非同类型的参数进行比较，也不能进行隐式转换，必须通过强制转化才能够进行比较。
* 限定作用域的枚举型别可以进行前置声明。不限定作用域的枚举要想进行前置声明，则可以进行对底层型别的声明：enum color: std::uint8_t

```
// 条款10、优先使用限定作用域的枚举
enum color {
	black,
	white,
	red
};

// 重定义报错
// auto white = false;

// 枚举类，限定作用域在color2中
enum class color2 {
	black1,
	white1,
	red1
};

auto white1 = false;
auto c = color2::red1;
```



#### 条款11、优先选用删除函数

在类里面的函数后面加上`=delete`可以组x织客户去调用函数，说明该函数已经失效。

任何函数都可以成为删除函数，但只有成员函数能声明为private。

####  条款12、在需要改写的函数添后面加上override声明表示子类需要改写这个函数

函数重写的必要条件：

* 基类中的函数必须是虚函数
* 函数名字必须完全相同（析构函数例外）
* 函数形参型别完全相同
* 函数常量性必须完全相同
* 函数返回值和异常规格必须兼容
* 引用饰词必须完全相同

成员函数引用饰词的作用就是针对发起成员函数调用的现象，加一些区分度。

#### 条款13、优先选用const_iterator

```
// 建立iterator并插入
std::vector<int>::iterator it = std::find(values.begin(), values.end(), 1983);
values.insert(it, 1998)
```

容器的cbegin和cend都返回的是const_iterator型别，不能修改，但是可以通过insert插入元素到列表中。

将const元素传入begin函数中，会产生一个const_iterator

```
// 条款13、优先选用const_iterator
void demo13() {
	std::vector<int> vec{ 1,2,3,4,5 };

	for (std::vector<int>::iterator iter = vec.begin(); iter != vec.end(); ++iter) {
		std::cout << "old element:" << *iter;				// 打印元素

		(*iter) += 1;										// 通过迭代器修改对应元素
		std::cout << ", new:" << *iter << std::endl;

	}

	auto it = std::find(vec.cbegin(), vec.cend(), 3);
	vec.insert(it, 1);

	for (std::vector<int>::const_iterator iter = vec.cbegin(); iter != vec.cend(); ++iter) {
		std::cout << "old element:" << *iter << std::endl;				// 打印元素

	}
}
```

* 优先使用begin，end等，而非成员函数版本

#### 条款14、对于确保不发生异常的函数，加上noexcept声明

在C++98中，出现异常后，调用栈会开解至异常出现的调用方，C++11中，中止前只是可能会解开。因此优化器不需要再异常传出函数的前提下，将执行期间栈保持在开解状态，也不用在异常溢出函数的前提下，保证所有对象的构造顺序的逆序完成解析。

`push_back`中采用的原则是：能移动则移动，必须复制才复制。具有强异常性质。

使用`noexcept`能够极大程度的便于编译器优化。

析构函数和内存释放函数都隐式的具备又noexcept的性质。

#### 条款15、使用constexpr

const：表示“只读”的含义，被const修饰的变量不能够修改，可以定义编译器和运行期的常量

constexpr：表示“常量”的含义，只能够定义编译器的常量，可以提高运行效率

* constexpr修饰的函数在编译过程展开，被隐式的转化为了内联函数
* 如果要修改const修饰的变量的值，需要加上关键字volatile

constexpr函数仅限于掺入和返回字面型别：在编译器就可以确定的数据类型。

#### 条款16、使用const成员函数的线程安全性

#### 条款17、



### 第四章、智能指针

> 智能指针是用来管理内存泄漏的，内存泄漏：在为对象动态申请内存空间后，如存在忘记释放内存，或者运行过程中程序中断的问题，导致申请的内存空间被占用造成内存泄漏。

#### 条款18、使用unique_ptr管理具备专属所有权的资源

智能指针`unique_ptr`

* 独享它的对象
* 包含头文件#include<memory>
* 可以高效的转化为`shared_ptr`

unique_ptr和裸指针在默认情况下有着相同的尺寸，实现的是专属所有权语义，

工厂函数返回的是一个`unique_ptr`指针，可以用auto来接收。

* 将一个裸指针赋值给`unique_ptr`将不会通过编译

```
// 智能指针用法
class AA {
public:
	std::string _name;
	AA() {
		std::cout << _name << "调用构造函数1\n";
	}
	AA(const std::string& m_name) :_name(m_name) {

		std::cout << _name << "调用构造函数2\n";
	}
	~AA() {

		std::cout << "调用析构函数\n";
	}
};

void demo19() {
	using std::unique_ptr;
	using std::make_unique;
	unique_ptr<AA> p0(new AA("xiaoli"));
	unique_ptr<AA> p1 = std::make_unique<AA>("xiaowang");
	unique_ptr<int> pp1 = make_unique<int>();         // 数据类型为int。
	unique_ptr<AA> pp2 = make_unique<AA>();       // 数据类型为AA，默认构造函数。
	unique_ptr<AA> pp3 = make_unique<AA>("xiaoxi");  // 数据类型为AA，一个参数的构造函数。
}
```



#### 条款19、使用shared_ptr管理具备共享所有权的资源

RAII思想（Resource Acquisition Is Initialization）资源获取即初始化。将获取的资源和对象的生命周期绑定在一起。

* 设计RAII类的四个步骤
  * 设计一个类封装资源，资源可以是内存、文件、socket、锁等等一切
  * 在构造函数中执行资源的初始化，比如申请内存、打开文件、申请锁
  * 在析构函数中执行销毁操作，比如释放内存、关闭文件、释放锁
  * 使用时声明一个该对象的类，一般在你希望的作用域声明即可，比如在函数开始，或者作为类的成员变量

引用计数带来的一些性能问题：

* 尺寸是裸指针的两倍
* 引用计数的内存必须动态分配
* 引用计数的递增和递减必须是原子操作

从一个已有shared_ptr移动构造一个新的shared_ptr会将源shared_ptr置空，不会增加引用计数。

使用shared_ptr如果出现了循环引用则会导致计数用于也无法回到0，资源将无法被释放。使用`weak_ptr`配合使用便可以解决这个问题，因为`weak_ptr`不会控制对象的生命周期，但是知道对象释放的时间，会和对象共同消失。

#### 条款20、weak_ptr使用

1、使用`weak_ptr`代替可能空悬的`shared_ptr`

2、`weak_ptr`可以用于缓存，观察者列表，以及避免shared_ptr指针环路

#### 条款21、优先使用make_...ptr而不是直接使用new

1、使用new的代码更加冗余

2、使用new会触发两次内存分配，而使用make系列则只用触发一次内存分配

#### 条款22、Pimpl用法

将类内的数据成员使用struct结构体打包起来，并使用一个智能指针指向该结构体，在.cpp文件中对这些数据进行操作，能够极大便利的降低类的客户和类的实现者之间的依赖。

### 右值引用、移动语义和完美转发

* 移动语义可以使得编译器能够使用移动操作代替昂贵的复制操作。
* 完美转发使得人们可以撰写接收任意实参的函数模板，并将其转发到其它函数，目标函数会接收到与转发函数所接收到的完全相同的实参。

#### 条款23、理解std::move和std::forward

使用move可以将参数强制转化为右值

如果想要取得对某一个对象执行移动操作的能力，则不要将其声明为常量，因为针对常量对象执行的移动操作将会变化为复制操作。

forward是一个有条件的强制转化，只有当实参是使用右值完成初始化时，它才会执行向右值型别的强制转化。如果需要选择性的对某一些形参进行左值或者右值引用，则可以用forward来进行标识。

#### 条款24、区分万能引用和右值引用

在模板和类型推导中出现`& &`很大可能就是万能引用：即表达式或者传入的实参，既可以是左值也可以时右值。

#### 条款25、针对右值引用使用move，针对万能引用使用forward

因为万能引用使用`forward`它可以有条件的自动转化传入的实参作为左值还是右值。

#### 

#### 条例30、完美转发失败的情形

完美转发的含义：一个函数把自己的形参传递（转发）给另一个函数，为了使得第二个函数接受第一个函数所接受的同一个对象。

完美转发不仅仅转发对象，还转发其显著的特征：型别、是左值还是右值，是否带有const或者volatile修饰词。

转发失败的定义：使用某特定实参调用转发函数会执行某操作，但使用同一实参调用其封装后的函数会执行不同的操作。即右值引用成功，但是万能引用失败。

具体形参：

* 大括号初始化的列表
* 0或者NULL用作空指针
* 仅有声明的整型static const成员变量
* 重载的函数名字和模板名字
* 位域

### 第五章、lambda表达式

lambda运行期的对象是`闭包`，根据不同的捕获模式，闭包会持有数据的副本或引用。

闭包类就是实例化闭包的类，每个lambda式都会触发编译器生成一个独一无二的闭包类。闭包中的语句会变成它的闭包类成员函数的可执行指令。

#### 条款31、避免默认捕获模式

C++11有两种默认捕获模式：按值或按引用。按引用的默认捕获模式可能导致空悬引用。

按引用捕获会导致闭包包含指涉到局部变量的引用，或者指涉到定义lambda式作用域内的形参的引用。一旦lambda式所创建的闭包越过了该局部变量或者形参的生命周期，那么闭包内的引用就会悬空。

