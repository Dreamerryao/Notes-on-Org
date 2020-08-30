# 多态

继承中介绍了向上类型转换，转换时会产生信息的丢失，从而只能调用基类的成员函数，但我们期望通过在派生类改写函数使得转换后仍然可以保留派生类中最新的函数体，例如

``` cpp

#include <iostream>
using namespace std;
class Person
{
    string t;

public:
    Person(string tt) : t(tt)
    {
        cout << "..." << endl;
    }
    void play()
    {
        cout << "person play" << endl;
    }
};
class student : public Person
{
public:
    student(string tt) : Person(tt)
    {
        cout << "...." << endl;
    }
    void play()
    {
        cout << "play pi" << endl;
    }
};
int main()
{
    student wzb("wzb");
    Person &WZB = wzb;
    WZB.play();

    return 0;
}
```

我们期望输出 "play pi" 实际输出的是 "person play"

这个时候我们就可以通过引入virtual关键字来实现动态捆绑。简单的在person类中play前加入virtual,就可以打印出"play pi"



> [!TIP|style:flat|label:virtual functions]
>
> 当一个对象有虚函数时，就会产生一个虚函数表
>
> 编译器就会去寻找虚函数表来进行函数体的绑定，因此可以表现出子类的行为



**抽象基类**：

我们有的时候 只希望基类作为派生类的一个接口，而不希望实际创建一个基类的对象。

这个时候，可以在基类中加入至少一个纯虚函数来使基类成为抽象类。

**纯虚函数**

``` cpp
virtual void play() = 0;
```



一个类中全是纯虚函数，我们称之为**纯抽象类**



## 题外话

既然我们使用了virtual ，我们所关心的name hiding就会被完全避免了么？

实际上，name hiding规则仍然有效，只不过加了一些补充条款

1. 如果参数列表相同，派生类重载的函数返回类型不能发生改变
2. 重新定义一个不是已存在的接口，基类的virtual版本也会被隐藏



**析构函数可以是虚函数，并且基类的析构函数最好定义成虚函数**





