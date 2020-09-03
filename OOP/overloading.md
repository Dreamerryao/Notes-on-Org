# Overloading

## 函数重载

C++中禁止使用返回值重载

允许使用不同参数列表进行函数重载

``` cpp
void f(int x){};
void f(char c){};
```

### 函数重载 VS 默认参数

默认参数 从右到左来使用，不能在中间搞一个

``` cpp
int test(int m,int n = 6,int k); //illegal
```

发生冲突，选择重载还是默认参数呢？编译器会给出不明确的error

## 运算符重载

对运算符进行重载可以使代码的可读性变得更好

### 不允许进行重载的运算符

- 成员选择operator .
- 成员指针间接引用 operator .*
- 三元表达式运算符 ?:
- 类型转换 cast操作
- 不能用户自定义运算符
- 不能改变优先级
- 没有求幂运算符



### 重载规则

``` cpp
const String String::operator + (const String& that);
```

> 就是一个特殊的函数，作为成员函数可以缺省第一个参数this
>
> 如果作为全局函数，两个参数就都要显式表达出来



举例子

``` cpp
class Integer { 
public: 
  Integer( int n = 0 ) : i(n) {} 
  const Integer operator+(const Integer& n) const{ 
      return Integer(i + n.i); 
  } 
  ...  
private: 
   int i; 
};
```

这个时候执行 x+y 调用的是 x.operator+(y);  y作为参数传递进来

``` cpp
z = 3+y;//error
```

> 因为3是内建类型，没有自定义的operator+,因此这样操作是不被允许的



> 如果作为全局函数来overload，可能需要friend友元声明
>
> 但全局函数允许 z = 3+y;因为会将3构造成Integer对象

 

>[!TIP|style:flat|label:成员函数]
>
>= () [] -> ->*运算符的重载必须作为成员函数，单目运算符最好也作为成员函数


.
> [!TIP|style:flat|label:关于返回值]
>
> 返回值为const 防止返回值成为左值 造成 x+y = z被允许的情况，这样是毫无意义的

### 特殊的重载

**++运算符的前缀和后缀**

当编译器看到 ++a时会调用 operator++(a),返回值是引用

看到a++时 会调用 operator++(a,int),返回值是对象



**operator[]的重载**

必须是成员函数，并且只接受一个参数。因为它作用的对象应该类似数组一样操作，所以这个运算符应该返回一个引用，可以成为左值。



**operator = 的重载**

- 在使用 = 时也会引起拷贝构造函数的调用，可以在构造函数中使用explicit关键字来表示该构造函数不能用作隐性类型转换，使用=会报错

  ``` cpp
  class A{
      public:
      A(int a){}
      A(char c){}
  }
  int main(){
      A a = 1;//A(1)
  }
  ```

- 编译器会自动创建赋值运算符，按成员赋值

- 返回值需要是reference 因为要考虑 A=B=C，B=C的返回值还要进行等于操作

- 可以将operator = 声明为private,就表示不允许别人进行赋值操作



**conversion operator的重载**

- 转换运算符
- 将要转换成的类型表达出来
- 可以不写返回类型
- 会在需要的时候自动调用
- 没有参数和返回类型



所以将A类型转换为B类型可以有两种方式:

1. 在B类型中声明构造函数B(A)
2. 在A类型中定义操作符B()

两个都有就会产生冲突

解决方法: 可以使用explicit来防止隐性类型转换



**编译器对类型的匹配机制**

首先，编译器会去找完全匹配的

然后，找涉及到内置转换的匹配

最后是用户定义的转换

### 内置的转换运算符介绍

- `static_cast<type>(expression)`

  `int b = static_cast<int>(a);`

  注意 const常量类型不能转换成非const 有专门的函数

- `dynamic_cast<type>`(expression)

  只管多态虚函数的转换，必须由virtual函数

  用来检查 downcast是否安全

- `const_cast<type>`(expression)

  ``` cpp
  const int g = 20;
  int &h = const_cast<int &>(g);
  ```

  将const转换为非const类型

- `reinterperet_cast<type>`(expression)

  指针和int相互转换

  ```cpp
  int a, b;
  int *pA = &b;
  a = reinterpret_cast<int>(pA); //correct
  pA = reinterpret_cast<A*>(a);  //correct
  b = reinterpret_cast<int>(a);  //Error, can not be used to convert int to int
  ```

  



