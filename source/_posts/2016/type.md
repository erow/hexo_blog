---
title: "type"
date: "2015-05-02 13:01"
categories：
    -c plus plus
tags:
    
---

这里要提的不是简单的基本类型。主要内容包括：

####1.左值右值 2.引用 3. const 关键字 4.类型转换

##左值、右值
  
这是一种很隐含的类型。因为不能简单的通过关键字来进行修饰。为什么需要严格区分左值、右值？右值可以理解为一个临时的数据，它即将销毁，如果我们能够对它加以区分可以重新利用这部分资源。同时他们是理解 move, forward 等新语义的基础。或许这是c++11最重要的特性，因为如线程库一类只是语言的扩展，右值的引入使得程序设计有了新的思路。

完整的LValue和RValue的界定
![](/blog_images2012070118490149.png)

一般来说左值对应于修改操作，右值对应于赋值操作。

  1. lvalue（左值）:
  
  lvalue指代一个函数或者对象。例如：

 > E是指针，则*E是lvalue
  一个函数的返回值是左值引用，其返回值是lvalue。例如int& foo();

  2. xvalue（expiring value，临终值）:
    
    xvalue指代一个对象，但是和lvalue不同，这个对象即将消亡。具体来说，xvalue是包含了右值引用的表达式。因为右值引用是C++11新引入的东西，所以xvalue也是一个新玩意。例如：

  >一个函数的返回值是右值引用，其返回值是xvalue。例如int&& foo();

  3. glvalue（generalized lvalue，泛左值）
    glvalue即lvalue和xvalue的统称。

  4. rvalue（右值）
  rvalue是xvalue和prvalue的统称。因为引入了右值引用，rvalue的定义在C++中被扩大化了。

  5. prvalue（pure rvalue，纯右值）
  prvalue指代一个**临时对象**、一个临时对象的子对象或者一个没有分配给任何对象的**值**。prvalue即老标准中的rvalue。例如：


    一个函数的返回值是平常类型，其返回值是rvalue。例如int foo();
    没有分配给任何对象的值。如5.3，true。


以上都是偏学术的东西，下面来点干货。

**左值是对象操作的实体，而右值意味着它即将消亡。**
> 只有左值才能进行相应的操作如赋值，调用成员函数。左值是一个实实在在的东西。
>
> ```
> int a=1,b=2;
> a=b;
> ```
> 显然a是左值，那么b呢？
> 因为b内存储着资源，占用着空间，所以它也是一个左值。
> 
> 我凭什么这样说呢？因为``int&& c=b;``是一个错误的句子，所以我很肯定。

> 而右值就是那些马上就没的东西。如函数的返回值，没有声明变量的类。
> ```
> widget f1() { return widget();}
> widget&& w1=widget();
> ```
> 

需要注意的是右值与左值是可以相互转化的。比如我们可以调用f1返回的对象中的 成员函数，总的来说右值转换为左值容易一些，但是无法改变右值即将消亡的事实。对于右值我们必须马上救济``widget w0=f1();``

**一个简单判断右值的方法就是：能不能去初始化右值引用**

一个函数的参数总是左值。即使声明为右值引用``void f(Widget&& w);``

**引用都是左值**，右值引用不是右值，而是绑定到右值的左值。
##const
1. 修饰普通变量
```
int const a = 2;
const int b=0;
```
都是不可修改变量值

2. 修饰指针，函数返回值
```
    int test = 0;
	int* const a = 0;
	const int*  b=0;
	*a = 10;
	b = &test;
	a = b;	//wrong 
    
    ```
此时需要注意const 后面的内容。 const 修饰变量名称表示该变量的值不可更改。const 修饰前面的类型(int*)
表示其指向的内容不可更改，即 `*b=1`是**非法的**

  ```
  int* const fun(int& const a, const int&  b); //由于函数返回值不可作为左值，所以无意义
  const int*  fun1(int& const a, const int&  b);//返回的对象必须是const int*
  ```

3. 修饰引用
    ```
    int& const a = test;//vc编译能过，但是没有任何意义。clang不行。 所以避免出现这种用法。
	const int&  b=test;//1
	a = 8; a = t1;
    ```
    [1]成为常左值引用，不可做任何修改。
  
4. 转换
  ```
  void f2(const int& a);
  int c=1;
  f2(c);
  ```
  非 常 类型数据可以隐式地转化为 常 类型。

##引用
在传统c中，如果要将数据作为参数交给函数体修改，则必须使用指针。在c++中，引用是更加安全有效的办法。相比于指针，引用没有内存泄路漏的问题。再配合新的智能指针，传统的指针应当被取代弃用。

[引用作为一种类型也需要空间，可以理解为 T*  const  ](http://blog.csdn.net/webscaler/article/details/6577429)
    ```
    T fun();
    int main()
    {
	auto&& ref_r = fun<func>(); //fun:0
	{
		auto& ref = fun<func>();
	}//~fun:0
	func f;//fun:1
	fun<func>() = f;//~fun:1
    /*
    exit:
    ~fun:1
    ~fun:0
    ~fun:0
    */
        return 0;
    }
    ```
    
右值引用为fun()返回临时变量延长了生命周期，变为该引用的生命周期。


详细的内容请看这里 [引用](references.md)

## 类型推导

c++中使用了3种类型推导方法：``auto``,`template`,`decltype`.

####template

```
template<typename T>
void f(ParamType param);
f(expr); // deduce T and ParamType from expr
```
1. ParamType is a Pointer or Reference
```
template<typename T>
void f(T& param); // param is a reference
```
T的类型与调用时参数的类型一致，基本上和我们设想的结果一样
2. ParamType is a Universal Reference
```
template<typename T>
void f(T&& param); // param is now a universal reference
```
只要了解泛引用这个问题就好理解了，详情看[引用](references.md)。

  param 的实际类型由调用时的参数决定。
```
int x = 27; // as before
const int cx = x; // as before
const int& rx = x; // as before
f(x); // x is lvalue, so T is int&,
      // param's type is also int&
f(cx); // cx is lvalue, so T is const int&,
       // param's type is also const int&
f(rx); // rx is lvalue, so T is const int&,
        // param's type is also const int&
f(27); // 27 is rvalue, so T is int,
        // param's type is therefore int&&
```
3.  ParamType is Neither a Pointer nor a Reference
```
template<typename T>
void f(T param); // param is now passed by value
```
这种情况会去掉调用参数的类型修饰，如const,reference

  ```
  int x = 27; // as before
  const int cx = x; // as before
  const int& rx = x; // as before
  f(x); // T and param are both int
  f(cx); // T and param are again both int
  f(rx); // T and param are still both int

  const char* const ptr = // ptr is const pointer to const object
  "Fun with pointers";
  f(ptr); // pass arg of type const char * const
  // ptr的类型是const char* ,被const修饰表示变量值不可改变
  ```

4. 最后还有需要注意的一点是如果参数是数组或者函数指针，那么情况会变得有点奇怪。

```
int keyVals[] = { 1, 3, 7, 9, 11, 22, 35 };
fun(keyVals);
//调用以上语句
//如果fun的声明如下
template<typename T>
void fun(T a)//void fun<int*>(T)：T=int*
{
}

template<typename T>
void fun(T& a)//void fun<int[7]>(T (&)):T=int [7]
{
}
//T& 保留了数组的大小信息，因而优于T

//巧用模板
template<typename T, std::size_t N> // return size of
constexpr std::size_t arraySize(T (&)[N]) // an array as a
{ // compile-time
return N; // constant
}

//对于函数指针2者之间其实没有太大的区别。
 void someFunc(int, double); // someFunc is a function;
                             // type is void(int, double)
 template<typename T>
 void f1(T param); // in f1, param passed by value
 template<typename T>
 void f2(T& param); // in f2, param passed by ref
 f1(someFunc); // param deduced as ptr-to-func;
               // type is void (*)(int, double)
 f2(someFunc); // param deduced as ref-to-func;
               // type is void (&)(int, double)```

##auto
其实和template没有太大区别。最大的不同在于：
```
auto x5 = {...}; // std::initializer_list<T>
```
而在函数中{}无法被正确的解释，因而template也随之失效。

##decltype
根据表达式和对象来推导类型。这个推导最接近于对象的真实类型。
```
Widget w;
const Widget& cw = w;
auto myWidget1 = cw; // auto type deduction:
                     // myWidget1's type is Widget
decltype(auto) myWidget2 = cw; // decltype type deduction:
                               // myWidget2's type is
                               // const Widget&
int x=0;
decltype((x)); //int&
decltype(x);   //int

```
decltype(auto)使用的是decltype的类型推导而不是auto。

因为`(x)`是一个表达式，因而将其作为一个左值来解释。

## 类型转换

1. static_cast <new_type> (expression)
    
    基本上用于静态类型转换，也能转换指针但不进行运行时安全检查，所以是非安全的。
2. const_cast <new_type> (expression)

  可去除对象的常量性（const），它还可以去除对象的易变性（volatile）。
3. dynamic_cast <new_type> (expression)

  它能安全地将指向基类的指针转型为指向子类的指针或引用。
4. reinterpret_cast <new_type> (expression)
  常用的一个用途是转换函数指针类型，即可以将一种类型的函数指针转换为另一种类型的函数指针，但这种转换可能会导致不正确的结果。无任何条件的转换，一般只用于底层代码。