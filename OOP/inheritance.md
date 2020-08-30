# Inheritance

终于写到了C++第二牛逼的特性:**继承** 了

> 要明确一个观点: 继承代表的关系是 "is-a",在类中将另一个类作为成员代表的关系是"has-a"
>
> 因此，composition 和 inheritance 是两种不同的重用方式
>
> B 继承自 A，首先先将A copy一份，然后填上B特有的东西

## 基础

``` cpp
class X{
  int i;
public:
    X(){ i = 0;}
    void set(int ii){ i = ii};
};

class Y : public X{
    int i;
public:
    Y(){i = 0;}
}
```

Y对X进行了继承，意味着Y将包含X中的所有成员变量和成员函数

派生类可以直接访问基类的所有公有函数

> [!TIP|style:flat|label:派生类可以做的:]
>
> 可以增加新的成员变量和成员函数
>
> 可以重新定义基类中已有的成员函数
>
> 可以改变现有成员的属性



### 关于访问控制

- 系统默认为私有继承
- private成员在派生类中不可访问
- public成员在派生类中的访问属性和继承方式相同
- protected成员和继承方式取最小访问权限

### Name Hiding

派生类对基类的成员函数进行重载，在得到的派生类中所有其他版本的同名成员函数都被隐藏

### 继承了什么

不能继承的有 1.构造和析构函数 2.operator = 3.类中的private成员



## 进阶

### 向上类型转换 (Upcasting)

student继承自person，所以所有person能在的位置，都可以放进去一个student,因为student is a person,这就叫向上类型转换

指针和引用也可以被派生类所替代

但是 向上类型转换会损失对象的类型信息，因此调用函数会直接调用基类的函数，有些时候我们并不希望这样，因此引入了C++最后一个牛逼的特性 **多态**