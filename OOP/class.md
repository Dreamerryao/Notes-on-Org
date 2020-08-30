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

//TODO 没写全



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

:star: 出大问题，为什么要出现这个initializer list? 看几段代码

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





## Tips

> [!TIP|style:flat]
>
> 在类中传入参数名与成员变量名字相同，优先考虑的是局部变量，可以使用this->来访问成员变量



