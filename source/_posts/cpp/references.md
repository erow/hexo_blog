title: "references"
date: "2015-05-02 13:01"
categories：
    -c plus plus
tags:
    
---

引用比我想象中的难，因此我觉得有必要单独来讲。

首先提一下引用的作用：1. 取代指针 2.延长生命周期

引用的声明很简单，总共就2种形式。
```
T&;为左值引用
int&&;右值引用
T&&;或为左值引用或为右值引用。叫泛引用（universal reference）

void f(Widget&& param); // rvalue reference
Widget&& var1 = Widget(); // rvalue reference
auto&& var2 = var1; // not rvalue reference
template<typename T>
void f(std::vector<T>&& param); // rvalue reference
template<typename T>
void f(T&& param); // not rvalue reference
```
大家可以使用如下方法实验：
```
template <typename T>
class type;
template<typename T>
auto f3(T&&a)
{
	return type<decltype(a)> ();//widget&
}
int main()
{
	
    auto&& w0=(f2());
    w0.print();
    auto&& w1 = w0;
    decltype(w0)&& w2 = f2();
    type<decltype(w0)>();//widget&&
    type<decltype(w1)>();//widget&
    type<decltype(w2)>();//widget&&
    f3(w0);
    widget && w3=w0;//error 无法将右值引用绑定到左值
 }
 
```
结果可以在编译器的错误信息里面得到

以上结果可以得到以下信息：
> 1. 类型推导（auto\template\decltype）加上&& 得到的是泛引用。
> 2. 普通类型+&&,只是右值引用。
> 3. 右值引用在使用的时候可以理解为左值。
> 4. 泛引用到底是左值还是右值引用，是根据在初始化时的初始变量的类型。

在最开始的例子中``void f(std::vector<T>&& param); // rvalue reference``虽然使用了template，但函数的参数是`std::vector<T> &&`，这意味着其实它是一个vector类型，因此对于一个具体的特点类型&& 仅仅表示右值引用。

#移动语义
```
std::thread t(my_func),t1;
t1=std::move(t);//转移线程资源，否则会有资源冲突
```
当我们打算移动某个数据时，使用`std::move`绝对是个明智的选择。在上面的例子中，它的确做到了我们想要的。
```
template<class _Ty> inline
	_CONST_FUN typename remove_reference<_Ty>::type&&
		move(_Ty&& _Arg) _NOEXCEPT
	{	// forward _Arg as movable
	return (static_cast<typename remove_reference<_Ty>::type&&>(_Arg));
	}
    ```
然而事实上`move`并没有转移任何资源，唯一可以肯定的是它返回的是一个右值引用。`move`只提供一个语义上的说明。换个说法就是一个右值引用的变量是一个即将销毁的变量，我们应该赶紧利用它。即便说在上面的例子中t不是一个临时变量，但那也意味着它应该被清除，在move之后t不能再掌管线程资源。

所以要想合理的使用move就要求我们所使用的类支持move语义。

```
int a1 = 4,a2;
a2 = move(a1);//只是做了一个拷贝~~~

class string { // std::string is actually a
public: // typedef for std::basic_string<char>
…
string& operator=(const string& rhs); // copy assignment
string& operator=(string&& rhs); // move assignment
…
};//这样的类才能正确的使用move
```

但是值得注意c++的隐式类型转换。一个const string&& 与 string&& 是不匹配的，然而它却与const string& 匹配。所以多加个const的结果是事与愿违，在使用move的时候不要加const!**不要、不要、不要 const**！

这并不是语言设计上的错误，而是编程逻辑的错误。使用move的对象应该是即将被销毁的，而const却又保护了它。唯一的缺陷是编译器没有报错，甚至是警告。

# forward
与move类似，forward只是在类型上面做文章。其作用也很简单，就是保留变量的类型。
```
void process(const Widget& lvalParam); // process lvalues
void process(Widget&& rvalParam); // process rvalues
template<typename T> // template that passes
void logAndProcess(T&& param) // param to process
{
auto now = // get current time
std::chrono::system_clock::now();
makeLogEntry("Calling 'process'", now);
process(std::forward<T>(param));//不用forward将调用process lvalues

}
```
forward的本质就是带条件的类型转换
```
template<typename T> // conceptual impl. of
T&& // std::forward (in
forward(T&& param) // namespace std)
{
if (is_lvalue_reference<T>::value) { // if T indicates lvalue
return param; // return param as lvalue
} else { // else
return move(param); // return param as rvalue
}
}
```
forward的使用也很单一，就是在使用泛引用而不知道到底是左值引用还是右值引用时。利用forward原封不动的传给其他调用者。

#生命周期

```
widget f2() {
	return widget();
}
widget& f3() {
	widget t;
	return t;
}
widget&& f4() {
	return f2();
}
int main()
{
    auto& w0=(f3());
    auto& w2 = (f2());
    widget&& w1 = f4();
    w0.print(); //error
    w2.print(); //正确
    w1.print();	//error
}
```
使用引用来延长临时变量的生命周期是函数的返回值必须是非引用类型的。
```
widget f2() {
	return widget();
}

widget f2() {//结果没问题，但是在return 的时候会调用一次move构造函数。
    widget t;
	return t;
}
```
在c中函数的调用过程其实是先拷贝参数，在return的时候返回一份拷贝。在c++中返回的是带move语义的拷贝。

```
函数调用过程
 copy -> param a         
         ....
 move <- return a
         destructed a
```

f3,f4的错误在于尽管我们试图利用引用来延长生命周期，但拷贝后的引用失去了这一作用。或许它仅仅对所初始化的对象有效，在f3,f4中它可能延长了另一个引用的生命周期。