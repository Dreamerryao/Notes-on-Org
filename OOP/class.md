# class

> [!TIP|style:flat]
>
> class就是体现c++ data abstract的地方，也展示了c++第一大牛逼特性:**封装**



## 封装

### class && object

> [!TIP|style:flat]
>
> class defines object
>
> object is a class

把数据和操作函数封装在一起，就成了一个类

类中有，成员变量和成员函数

```cpp
class A{
    public:
    A(){}
    ~A(){}
    void publicFunc(){cout<<"?"<<endl;}
    private:
    int privateData;
}
```

> [!TIP|style:flat]
>
> 通过public 和private、protected来选择性的隐藏和暴露一些类内部的成员,也称c++的访问控制
>
> 将类的成员划分为不同的访问级别有两个好处：一是**信息隐蔽**，即实现封装；二是**数据保护**，即将类的重要信息保护起来，以免其它程序不恰当地修改。

public 对外完全开放

private 只能由本类中的成员函数访问，外部访问都不被允许

protected 可以由本类的成员函数访问，也可以由本类的派生类的成员函数访问

这里就引出了一个有趣的东西 ——友元，它打破了规定的访问控制

### 友元

> [!TIP|style:flat]
>
> 友元可以访问与其有friend关系的类中的private成员
>
> 友元可以分为 **友元函数** 和 **友元类** 友元可以是一个全局函数，也可以是另一个类的成员函数，也可以是整个类

友元调用另一个类的成员函数 使用方法:

``` cpp
#include <iostream>
using namespace std;
class Date; //对 Date类的提前引用声明
class Time{ //定义 Time类
public:
    Time(int,int,int);
    void display(Date &); //display是成员函数，形参是Date类对象的引用
private:
    int hour;
    int minute;
    int sec;
};
class Date{ //声明 Date类
public:
    Date(int,int,int);
    friend void Time∷display(Date &);
private:
    int month;
    int day;
    int year;
};
Time∷Time(int h,int m,int s) //类Time的构造函数
{
    hour=h;
    minute=m;
    sec=s;
}
void Time∷display(Date &d) 
{
    cout<<d.month<<″/″<<d.day<<″/″<<d.year<<endl;
    cout<<hour<<″:″<<minute<<″:″<<sec<<endl;
}
Date∷Date(int m,in d,int y) //类Date的构造函数
{
    month=m;
    day=d;
    year=y;
}
int main( )
{
    Time t1(10,13,56); //定义Time类对象t1
    Date d1(12,25,2004); //定义Date类对象d1
    t1.display(d1); //调用t1中的display函数，实参是Date类对象d1
    return 0;
}
```

调用友元类 

不仅可以将一个函数声明为一个类的“朋友”，而且可以将一个类(例如B类)声明为另一个类(例如A类)的“朋友”。这时B类就是A类的友元类。友元类B中的所有函数都是A类的友元函数，可以访问A类中的所有成员。

```
class Y {
    //…
};
class X {
    //…
    friend Y; // 声明类Y为类X的友元类
    //…
};
```



> [!TIP|style:flat]
>
> - 友元关系是单向的，不具有交换性
>
>   即若类X是类Y的友元，但类Y不一定是类X的友元。
>
> - 友元关系不具有传递性
>
>   即若类X是类Y的友元，类Y是类Z的友元，但类X不一定是类Z的友元。

## 构造、析构函数

### 一般介绍

> c++觉得依靠程序员自觉进行初始化的形式太nt了，为了防止用户常常因为忽视初始化而造成的种种bug, 就提供了构造函数来保证每个对象在定义时就被初始化。
>
> 如果类有构造函数,就会在对象创建时自动调用，否则，编译器会自动调用缺省构造函数，构造函数名与类名相同。

> [!TIP|style:flat|label:关于构造函数]
>
> 程序运行到对象创建语句时，才会为该对象进行内存分配和构造函数的调用，所以c++严格禁止跳过对象创建语句的情况发生！
>
> 一旦自己定义了一个构造函数，编译器就会确保不论在什么情况下都会调用自己定义的构造函数。

构造函数和析构函数属于类中特殊函数，没有返回值。



> [!TIP|style:flat|label:关于析构函数]
>
> 当对象超出其作用域时，会自动调用析构函数。
>
> 析构函数不能拥有任何参数，分配参数会error

### 开始花里胡哨了

#### Initializer list

> Initializer list 对类中变量进行初始化，可以初始化任何类型的数据
>
> 初始化的顺序是类中定义的顺序，而不是自己写代码时list的顺序
>
> 使用方法：

``` cpp
class A{
  const int size;
    A(int s = 5):size(s){
        ...
    }
};
```

:star: 出大问题，为什么要有initializer list这个东西? 看几段代码

``` cpp
#include <iostream>
using namespace std;
class X{
    const int size;
    X(int s){
        size  = s;
    }
};
int main(){
    X x(1);
}
```

> [!DANGER|style:flat|label:output]
>
> error: uninitialized const member in 'const int' 
> const int X::size' should be initialized
> assignment of read-only member 'X::size'



``` cpp
#include <iostream>
using namespace std;
class A
{
    int i;
public:
    A(int a){cout<<"!!!"<<endl;}
};

class B
{
    A a;
public:
    B(int x)
    {
        a = A(x);
        cout << "B's Constructor called";
    }
};

int main()
{
    B obj(10);
    return 0;
}
```

> [!DANGER|style:flat|label:output]
>
> error: no matching function for call to 'A::A()'

研究一下这里为什么会出现这种情况 在调用构造函数时，a会调用缺省构造函数，这里的'='是赋值运算符，因此执行的操作是 1.执行A(x)构造函数 2.赋值 3.将 A(x)临时变量销毁掉



>必须使用initializer list的几种情况:
>
>1. 初始化非静态的const 数据成员
>2. 对于没有默认构造函数的成员对象的初始化
>3. 没有默认构造函数的派生对象对基类对象的初始化

简单来说，对于子对象构造调用函数，C++提供专门语法，即 initializer list

### 构造函数和析构函数调用顺序

当一个对象有很多子对象时，构造函数和析构函数调用顺序就有讲究起来了

构造是从类层次的最根处开始，在每一层会首先调用基类的构造函数，然后调用成员对象构造函数，调用成员对象构造函数的顺序按照类中的声明顺序，而不是initializer list中的顺序。

析构函数正好与之相反。

## 拷贝构造

``` cpp
T::T(const T&);
```

> [!TIP|style:flat|label:关于拷贝构造函数]
>
> - 只有一个参数，是本类对象的引用
> - 通过等于号复制对象时，系统自动调用拷贝构造函数
> - 拷贝构造函数与原来的构造函数实现了重载

调用拷贝构造函数的情况

- 代入法/赋值法调用一个对象**初始化**另一个对象
- 调用函数，形参和实参结合时
- 返回值是对象，函数执行完成，返回调用者时

> [!WARNING|style:flat|label:存在指针]
>
> 缺省的拷贝构造函数会把指针简单赋值，不会进行深拷贝
>
> 如果需要进行深拷贝需要显式定义拷贝构造函数

如果不想对这个类来进行拷贝构造，就声明一个private的拷贝构造函数，这样就可以防止外界对此类进行拷贝构造

> [!TIP|style:flat]
>
> 在类中传入参数名与成员变量名字相同，优先考虑的是局部变量，可以使用this->来访问成员变量





