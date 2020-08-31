# 模板

> [!TIP|style:flat|label:简介]
>
> 模板是作为泛型编程基础而存在的，其目的是以独立于类型的方式来编写代码
>
> 我们所使用的stl库中的容器就使用了泛型编程的思想
>
> 比如 `vector<int>` `vector<string>` 我们如果一般编程就要写很多个只有类型不同的函数，想想都头疼。



## 使用方式

通常将模板的声明和实现都放在头文件中

``` cpp
template < class T >
void swap( T& x, T& y ){
    T temp = x;
    x = y;
    y = temp;
}

```

其中 class T就可以代表任何内建类型或用户定义的类型

也可以用模板生成**模板类**,模板类的一个典型用途就是在容器类中。

``` cpp
template <class T>
class Stack { 
  private: 
    vector<T> i;
 
  public: 
    void push(T const&);  
    void pop();            
    T top() const;            
    bool empty() const{       
        return i.empty(); 
    } 
}; 
```

> [!TIP|style:flat|label:注意！！]
>
> 对于模板函数来讲，是不会对它进行类型转换的，而且所有的T都必须是一个类型，不能在一处是int,一处是string

编译时糊优先调用已经显示写出的函数，找不到就会使用模板实例化一个函数出来

>如果函数没有参数，可以使用`<>`
>
>如 func`<int>`();



当然，模板允许使用多个类型

``` cpp
template < class T, class U>
class MyClass{
	const U& lookup (const T&) const;
}

```



## 模板与继承

模板类可以继承模板类和非模板类，非模板类也可以继承模板类

``` cpp
// method 1
template <class A>
class Derived : public Base{...}

//method 2
template <class A>
class Derived : public Base<A>{...}

//method 3
class Derived: public Base<int>{...}
```

