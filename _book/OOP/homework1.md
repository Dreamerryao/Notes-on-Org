# 开始做题

> [!TIP|style:flat|label:这里有什么]
>
> 一些典型题和做错的题

//TODO 

流运算操作符的重载

异常

1. 操作符重载

   出师不利

   ```cpp
   #include <cstring>
   #include <iostream>
   using namespace std;
   class Str{
       char m_s[10];
       char * m_p;
       public:
       Str(char * s){strcpy(m_s,s);
       m_p = m_s;}
       char *operator++(){
           return ++m_p;
       }
       char operator[](int i){
           return m_s[i];
       }
       operator char*(){return m_p;}
   };
   int main(){
       Str s("Hi");
       cout<<*s<<endl;
       ++s;
       cout<<s[0]<<endl;
       cout<<*s<<endl;
   }
   ```

   > output:
   >
   > H
   >
   > H
   >
   > i

   竟然连 operator char*() 是啥都想不起来了...

   类型转换的运算符重载可以没有返回类型，在需要时自动调用，在这里就是把s转换成了char*

   *s就转换成了char类型，因此输出H

2. 继承类的构造与析构

   还行

   ``` cpp
   #include <iostream>
   using namespace std;
   class A{
       public:
       A(){cout<<"A()"<<endl;}
       ~A(){cout<<"~A()"<<endl;}
   };
   class B: public A{
       public:
       B(){cout<<"B()"<<endl;}
       ~B(){cout<<"~B()"<<endl;}
   };
   int main(){
      A a;
      B b;
   }
   ```

   > output:
   >
   > A()
   >
   > A()
   >
   > B()
   >
   > ~B()
   >
   > ~A()
   >
   > ~A()

   这很明显 先A构造，构造完之后B是A派生类，得先构造A，再构造B，然后析构反着来，over

3. 拷贝构造和赋值的区别

   ```cpp
   #include <iostream>
   using namespace std;
   struct A{
       A(){cout<<"A"<<endl;}
       A(const A &a){cout<<"B"<<endl;}
       A& operator=(const A&a){cout<<"C"<<endl; return *this;}
   };
   int main(){
      A a[2];//2*A
      A b = a[0];//1*B
      A c;//1*A
      c = a[1];//1*C
   }
   ```

   > output:
   >
   > A
   >
   > A
   >
   > B
   >
   > A
   >
   > C

   这题也easy，懂了拷贝构造和赋值就ok了，注意这里

   struct的缺省是public,class缺省是private

   operator = 返回类型设置为引用，防止 A=B=C情况发生

4. 派生类函数重载后 name hidden

   ``` cpp
   #include <iostream>
   using namespace std;
   class A
   {
   public:
       void F(int) { cout << "A:F(int)" << endl; }
       void F(double) { cout << "A:F(double)" << endl; }
       void F2(int) { cout << "A:F2(int)" << endl; }
   };
   class B : public A
   {
   public:
       void F(double) { cout << "B:F(double)" << endl; }
   };
   int main()
   {
       B b;
       b.F(2.0);
       b.F(2);
       b.F2(2);
   }
   ```

   > output:
   >
   > B:F(double)
   >
   > B:F(double)
   >
   > A:F2(int)

   这里F函数重载后，基类的F函数被隐藏了，因此class B只有F(double)接口

5.  **深入理解虚函数的动态绑定和缺省参数的静态绑定**

   奇怪的知识增加了

   ``` cpp
   #include <iostream>
   using namespace std;
   struct A{
       virtual void foo(int a = 1){
           cout<<"A"<<endl<<a;
       }
   };
   struct B:A{
       virtual void foo(int a = 2){
           cout<<"B"<<endl<<a;
       }
   };
   int main()
   {
       A *a = new B;
       a->foo();
   }
   ```

   > output:
   >
   > B
   >
   > 1

   永远不要重新定义虚函数继承的默认缺省参数，因为虚函数具有动态绑定特性但是默认缺省参数却具有静态绑定特性。如果重新定义了默认参数，就会出现混乱。

   dbq，深入理解失败，记一下吧(...

6. 显式的专门化: `template<>`

   ``` cpp
   #include <iostream>
   using namespace std;
   template <class T>
   void f(T &i) { cout << 1 << endl; }
   template <>
   void f(const int &i) { cout << 2 << endl; }
   int main()
   {
       const int i = 2;
       f(i);
       int h = 4;
       f(h);
   }
   ```

   > output:
   >
   > 2
   >
   > 1

   模板里是不允许类型转换的，都模板了还类型转换，那谁知道你想调用哪个((

   编译器找匹配时候先找精确匹配的，比如const int 就精确匹配上了，没有就去模板里专门化一个出来

7. **类中的const成员函数**

   迷惑行为大赏

   ```cpp
   #include <iostream>
   using namespace std;
   class A
   {
       int s[10];
   public:
       int operator[](int i) const
       {
           cout << "operator[](int)const" << endl;
           return s[i];
       }
       int &operator[](int i)
       {
           cout << "operator[](int)" << endl;
           return s[i];
       }
   };
   int main()
   {
       A a1;
       const A &a2 = a1;
       a1[0] = a2[1];
   }
   ```

   > output:
   >
   > 我的理论上: 
   >
   > `operator[](int)const`
   >
   > `operator[](int)`
   >
   > 实际上:
   >
   > `operator[](int)`
   >
   > `operator[](int)const`

   里面涉及到的知识点,总结时候没写过:

   **在C++中，只有被声明为const的成员函数才能被一个const类对象调用。**

   - const成员函数可以访问非const对象的非const数据成员、const数据成员，也可以访问const对象内的所有数据成员；
   - 非const成员函数可以访问非const对象的非const数据成员、const数据成员，但不可以访问const对象的任意数据成员；
   - 在声明一个成员函数时，若该成员函数并不对数据成员进行修改操作，应尽可能将该成员函数声明为const 成员函数。

   关于答案的思考：

   按照我的想法 应该是a2[1]先被拿出来，然后调用默认的 = 运算符，赋值给a1[0],赋值的时候才会调用a1[0]，但g++显然不这么想...

8. **类中的static**

   又是没整理过的知识点，起飞

   ``` cpp
   #include <iostream>
   using namespace std;
   class A
   {
   public:
      static void f(double){
          cout<<"f(double)"<<endl;
      }
      void f(int){
          cout<<"f(int)"<<endl;
      }
   };
   int main()
   {
       const A a;
       a.f(3);
   }
   ```

   > output:
   >
   > f(double)

   没啥好说的，static成员函数是**为类服务的函数**，而不是为对象服务的函数。

   static成员函数不存在this指针，无法访问非静态的成员。

   static成员函数存在有以下意义: 1.与类的实例无关，但可以限制该函数访问权限 2.可以更改类中的static变量

   当然也要提一提 static成员变量，放在全局数据区肯定不用说，也是与类的实例无关的。 static成员变量一定要初始化,并且一定要在类定义体外面~~~

9. **内置的转换运算符**

   这能这么考 绝了( , 没啥好说的, 给跪了

   ```cpp
   #include <iostream>
   using namespace std;
   class A
   {
   public:
      virtual ~A(){}
   };
   class B:public A{};
   int main()
   {
       A a;
       B b;
       A * ap = &a;
       if(dynamic_cast<B*>(ap))cout<<"OK1"<<endl;
       else cout<<"FAIL"<<endl;
       if(static_cast<B*>(ap))cout<<"OK2"<<endl;
       else cout<<"FAIL"<<endl;
       if(dynamic_cast<B*>(ap))cout<<"OK3"<<endl;
       else cout<<"FAIL"<<endl;
       if(static_cast<B*>(ap))cout<<"OK4"<<endl;
       else cout<<"FAIL"<<endl;
   }
   ```

   > output:
   >
   > FAIL
   >
   > OK2
   >
   > FAIL
   >
   > OK4

   没啥好说的好吧，阴险狡诈，考试时候爷直接四个FAIL填上爱咋咋

   其实真对dynamic_cast没啥底，又去搜了一下，将一个基类指针转换到继承类指针时会用到它。

   在类层次间进行上行转换时，dynamic_cast和static_cast的效果是一样的；

   在进行下行转换时，dynamic_cast具有类型检查的功能，比static_cast更安全。
    **基类一定要有虚函数！！！**，要不然直接编译出错

10. 关于析构函数的virtual

    这个海星

    ``` cpp
    #include <iostream>
    using namespace std;
    struct Base{
        virtual ~Base(){
            cout<<1<<endl;
        }
        virtual void f(){
            cout<<2<<endl;
        }
    };
    struct Derived:public Base{
        ~Derived(){
            cout<<3<<endl;
        }
        void f(){
            cout<<4<<endl;
        }
    };
    int main()
    {
       Base *p = new Derived();
       (*p).f();
       p->f();
       delete p;
    }
    ```

    > output:
    >
    > 4
    >
    > 4
    >
    > 3
    >
    > 1

    析构函数声明为virtual时，可以防止内存没删干净的情况发生，是个好的写法，

    这样析构完Derived后还会析构Base

    如果不virtual就会只调用1，那虚函数表的切分下面的Derived部分内存就不会被清理掉

11. **派生类和拷贝构造的结合**

    ``` cpp
    #include <iostream>
    using namespace std;
    class A{
        int i;
        public:
        A(int ii=0):i(ii){
            cout<<"call A(int ii=0)"<<endl;
        }
        A(const A&a){
            i = a.i;
            cout<<"call A(const A&)"<<endl;
        }
        void print() const{cout<<"A::i = "<<i<<endl;}
    };
    class B:public A{
        int i;
        A a;
        public:
        B(int ii=0):i(ii){
            cout<<"call B(int ii=0)"<<endl;
        }
        B(const B&b){
            i = b.i;
            cout<<"call B(const B&)"<<endl;
        }
        void print() const{cout<<"B::i = "<<i<<endl;}
    };
    int main()
    {
       B b(2);
       b.print();
       B c(b);
       c.print();
    }
    ```

    > output:
    >
    > call A(int ii=0)
    >
    > call A(int ii=0)
    >
    > call B(int ii=0)
    >
    > B::i = 2
    >
    > call A(int ii=0)
    >
    > call A(int ii=0)
    >
    > call B(const B&)
    >
    > B::i = 2

    拉胯了，又是一个没复习到的点，这里是对派生类在进行拷贝构造时，如果基类也要同时进行拷贝构造，就一定要在派生类的拷贝构造函数的initializer list中显式的调用基类的拷贝构造函数，否则就会调用基类的默认构造函数

    看两个例子

    ``` cpp
    #include <iostream>
    using namespace std;
    class A
    {
    public:
        A() { cout << "A default constructor" << endl; }
        A(A&) { cout << "A copy constructor" << endl; }
    };
    class B : public A
    {
    public:
        B() { cout << "B default constructor" << endl; }
        B(B &b) { cout << "B copy constructor" << endl; }
    };
    int main()
    {
        B b;
        B c = b;
        return 0;
    }
    ```

    > output:
    >
    > A default constructor
    >
    > B default constructor
    >
    > A default constructor
    >
    > B copy constructor

    ``` cpp
    #include <iostream>
    using namespace std;
    class A
    {
    public:
        A() { cout << "A default constructor" << endl; }
        A(A&) { cout << "A copy constructor" << endl; }
    };
    class B : public A
    {
    public:
        B() { cout << "B default constructor" << endl; }
        B(B &b) : A(b) { cout << "B copy constructor" << endl; }
    };
    int main()
    {
        B b;
        B c = b;
        return 0;
    }
    ```

    > output:
    >
    > A default constructor
    >
    > B default constructor
    >
    > A copy constructor
    >
    > B copy constructor

12. 基类和派生类指针赋值问题

    基本分为 从派生类到基类的向上转化 和从基类到派生类的向下转化

    向上转化一般都是安全的，因为派生类中本来就包含有基类的拷贝

    基类到派生类的转化 就有可能存在问题了

    因此，派生类的指针不能指向基类对象，不能用基类指针给派生类的指针进行赋值

    ``` cpp
    #include <iostream>
    using namespace std;
    class A
    {
        int i;
    public:
        A() : i(0) { cout << "A()" << i << endl; }
        virtual void set(int i) {cout<<"A set"<<endl; this->i = i; }
        virtual int get() { return i; }
    };
    class B : public A
    {
        int i;
    public:
        B() : i(10) { cout << "B()" << i << endl; }
        virtual void set(int i) {cout<<"B set"<<endl; this->i = i; }
        // virtual int get() { return i; }
    };
    int main()
    {
        B b;
        A *p = &b;
        p->set(30);
        cout << p->get();
        return 0;
    }
    ```

    > output:
    >
    > A()0
    >
    > B()10
    >
    > B set
    >
    > 0

    这题真的有点东西，当派生类的成员变量同名的时候，也会发生Name hidden,但这时候利用切片切出来的A，又把基类的i暴露出来了，绝了...

13. **什么函数不能被声明为虚函数**

    - 静态成员函数，友元函数 -->虚函数一定是类的成员函数
    - 显式定义的内联函数
    - 构造函数

14. 继承与构造析构

    根据一道脑残题想到的

    ```cpp
    #include <iostream>
    using namespace std;
    class C{
        public:
        C(){cout<<5<<endl;}
        ~C(){cout<<6<<endl;}
    };
    class A{
        public:
        A(){cout<<1<<endl;}
        ~A(){cout<<2<<endl;}
    };
    class B:public A{
        public:
        B(){cout<<3<<endl;}
        ~B(){cout<<4<<endl;}
        private:
        C c;
    };
    int main()
    {
     B b;
    }
    ```

    > output:
    >
    > 1
    >
    > 5
    >
    > 3
    >
    > 4
    >
    > 6
    >
    > 2

    可以看到 构造B之前，会先对基类A进行构造，然后对成员变量C进行构造

15. **恶心的问题之 基于const的重载**

    在类中会有这样一种重载，它是合法的。

    ``` cpp
    class A {
    int function ();
    int function () const;
    };
    ```

    可以看到在A类中，function函数是发生重载了，而且是合法的。而且在调用时，只用A类的const对象才能调用const版本的function函数，而非const对象可以调用任意一种，通常非const对象调用不是const版本的function函数。

    原因是：按照函数重载的定义，函数名相同而形参表有本质不同的函数称为重载。在类中，由于隐含的this形参的存在，const版本的function函数使得作为形参的this指针的类型变为指向const对象的指针，而非const版本的使得作为形参的this指针就是正常版本的指针。此处是发生重载的本质。重载函数在最佳匹配过程中，对于const对象调用的就选取const版本的成员函数，而普通的对象调用就选取非const版本的成员函数。

16. **论 virtual会给人脑翻译带来多大困难**

    先看个脑残例子

    ``` cpp
    #include <iostream>
    using namespace std;
    class A
    {
        int i;
    public:
         void set(int i) {cout<<"A set"<<endl; this->i = i; }
         int get() {cout<<"A get"<<endl; return i; }
    };
    class B : public A
    {
        int i;
    public:
        void set(int i) {cout<<"B set"<<endl; this->i = i; }
        int get() {cout<<"B get"<<endl; return i; }
    };
    int main()
    {
       B a;
       B b;
       a.set(10);
       b.set(20);
       A &p = a;
       p.set(30);
       cout<<"P get "<<p.get()<<endl;
       cout<<"A get "<<a.get()<<endl;
       p = b;
       p.set(40);
       cout<<a.get();
    }
    ```

    不带virtual 世界清静

    > output:
    >
    > B set
    >
    > B set
    >
    > A set
    >
    > A get
    >
    > P get 30
    >
    > B get
    >
    > A get10
    >
    > A set
    >
    > B get
    >
    > 10

    和预期一模一样，带virtual之后，啧啧啧

    ``` cpp
    #include <iostream>
    using namespace std;
    class A
    {
        int i;
    public:
         virtual void set(int i) {cout<<"A set"<<endl; this->i = i; }
         virtual int get() {cout<<"A get"<<endl; return i; }
    };
    class B : public A
    {
        int i;
    public:
        void set(int i) {cout<<"B set"<<endl; this->i = i; }
        int get() {cout<<"B get"<<endl; return i; }
    };
    int main()
    {
       B a;
       B b;
       a.set(10);
       b.set(20);
       A &p = a;
       p.set(30);
       cout<<"P get "<<p.get()<<endl;
       cout<<"A get "<<a.get()<<endl;
       p = b;
       p.set(40);
       cout<<a.get();
    }
    ```

    > output:
    >
    > B set
    >
    > B set
    >
    > B set
    >
    > B get
    >
    > P get 30
    >
    > B get
    >
    > A get 30
    >
    > B set
    >
    > B get
    >
    > 40

    因为这个时候虽然切片切出去了B的变量，但是要命的是，B的参数被传进了虚函数表里，操作的仍然是var_b

17. **C语言卑中卑**

    ``` cpp
    size_t strlen(const char* str);
    //strlen 不算\0,并且参数为空的时候会报错，所以要自己加处理
    char *strcpy(char * destination,const char* source);
    int strcmp(const char* str1,const char* str2);
    //strcmp 字符串相等 返回0，不相等返回1
    ```

18. **赋值运算符的调用顺序**

    好题好题(

    ```cpp
    #include <iostream>
    using namespace std;
    class A
    {
    public:
        A &operator=(const A &r)
        {
            cout << "A operator=" << endl;
        }
    };
    class B
    {
    public:
        B &operator=(const B &r)
        {
            cout << "B operator=" << endl;
        }
    };
    class C
    {
    private:
        B b;
        A a;
        int c;
    };
    int main()
    {
        C m, n;
        m = n;
    }
    ```

    > output:
    >
    > B operator=
    >
    > A operator=

    缺省的赋值运算符，会按照类中声明顺序来进行赋值

19. **来点刺激的，关于new delete**

    ``` cpp
    #include <iostream>
    using namespace std;
    class A
    {
    public:
        A(){
            cout<<"A()"<<endl;
        }
        ~A(){
            cout<<"~A()"<<endl;
        }
    };
    class B:public A
    {
    public:
        B(){
            cout<<"B()"<<endl;
        }
        ~B(){
            cout<<"~B()"<<endl;
        }
    };
    int main()
    {
       A*ap = new B[2];
    //    delete ap;
    //    delete [] ap;
    }
    ```

    > output:
    >
    > A()
    >
    > B()
    >
    > A()
    >
    > B()

    爷的析构函数去哪了...

    ohh,系统只会自动释放栈内空间，而堆内空间需要用户自己维护。C++中，除了new来的空间存放在堆内，其他均存放在栈中。

    但是，如果创建了指向new来的一块空间的指针的时候，如果在没有显示释放掉new到的堆空间时，系统是不会自动调用析构函数去释放栈空间中的指针的。

    当然了，要是加delete ap; 就只会加上一个~A() 典型的内存没删干净(

20. **c++不允许同时对函数进行static 和 const修饰**

    ```cpp
    static int get()const; //error!
    ```

    const修饰符用于表示函数不能修改成员变量的值，该函数必须是含有this指针的类成员函数，函数调用方式为thiscall
     而类中的static函数本质上是全局函数，调用规约是`__cdecl`或`__stdcall`,不能用const来修饰它

21. **自己写代码之 定义派生类的赋值运算符**

    ``` cpp
    // Base::operator=(const Base&) 不会被自动调用
    D& D::operator=(const D &rhs)
    {
        Base::operator=(rhs);  //为基类部分赋值
        //按照过去的方式为派生类的成员赋值
        return *this;
    }
    ```

    举个例子:

    ``` cpp
    class base
    {
    public:
      base(int initialvalue = 0): x(initialvalue) {}
    private:
      int x;
    };
    class derived : public base
    {
    public:
      derived(int initialvalue): base(initialvalue), y(initialvalue) {}
      derived& operator=(const derived& rhs);
    private:
      int y;
    };
    derived& derived::operator = (const derived& rhs)
    {
      if (this == &rhs)
        return *this;
      base::operator = (rhs);    // 调用this->base::operator=
      y = rhs.y;
      return *this;
    }
    ```

22. **流操作符重载**

    看个例子

    ``` cpp
    #include <iostream>
    using namespace std;
    class Complex
    {
    private:
        int real, imag;
    public:
        Complex(int r = 0, int i =0)
        {  real = r;   imag = i; }
        friend ostream & operator << (ostream &out, const Complex &c);
        friend istream & operator >> (istream &in,  Complex &c);
    };
    ostream & operator << (ostream &out, const Complex &c)
    {
        out << c.real;
        out << "+i" << c.imag << endl;
        return out;
    }
    istream & operator >> (istream &in,  Complex &c)
    {
        cout << "Enter Real Part ";
        in >> c.real;
        cout << "Enter Imaginary Part ";
        in >> c.imag;
        return in;
    }
    int main()
    {
       Complex c1;
       cin >> c1;
       cout << "The complex object is ";
       cout << c1;
       return 0;
    }
    ```

23. **nt题目大赏**

    传说中的0023

    ``` cpp
    #include <iostream>
    #include <vector>
    #include <iterator>
    #include <memory>
    #include <iomanip>
    #include <stdlib.h>
    #include <fstream>
    using namespace std;
    class A{
    public:
        A(int i=0):m_i(i){};
        void display(){
            cout << m_i << endl;
        }
        // ~A(){
        //     cout<<"A dtor"<<endl;
        // }
    private:
        int m_i;
        friend int main();
    };
    void createA(A* pA){
        pA = new A(1);
        cout<<pA<<endl;
    }
    A* createA(){
        A a(2);
        return &a;
    }
    void createAOnStack(){
        A a(3);
    }
    int main()
    {
        A a;
        A* pA = &a;
        pA->display();
        cout<<pA<<endl;
        createA(pA);
        pA->display();
        A* a2 = createA();
        cout << a2->m_i << endl;
        createAOnStack();
        cout << a2->m_i << endl;
        return 0;
    }
    ```

    > output:
    >
    > 0
    >
    > 0x6ffe00
    >
    > 0x1c5f50
    >
    > 0
    >
    > 2
    >
    > 3

    自己太菜，问了好些人......第一对00考的还算正常，把指针传参，修改指针值，形参不会反馈，可以用 \*\*来代替

    后一对 2 3

    2还行，编译器自动的析构函数不会修改成员变量值，虽然2被析构了，还是会强制输出，这个3就nm离谱，压栈之后又createA,强制写3,只要我人工加一个~A,立马暴毙....

24.  to be continued...
