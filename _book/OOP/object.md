# 储备知识



## C++ 储备知识 (跳跳跳

``` cpp
#include <iostream>
using namespace std; //命名空间
int main(){
    cout<<"OOP zhuaba"<<endl;
    return 0;
}
```



``` cpp
#include <iostream>
#include <string>
#include <fstream>
using namespace std;
int main(){
    //string
    string str;
    str = "Hello";
    string str1(str+"asdhiad");
    cout<<str1<<endl;
    string str3 = str1.substr(0,11);
    //char char1[20];
    //char char2[20] = "jsad";
    //char1 = char2;
    //error: char1不是可修改的左值


    //file I/O
    ofstream file1(".//test.txt");
    file1<<str3<<endl;
    ifstream file2(".//test.txt");
    file2>>str;
    cout<<str<<endl;

    //pointer to Objects
    string s = "Hello";
    string *ps = &s;
    cout<<"string address"<<ps<<endl;
    cout<<"string length"<< ps->length()<<endl;

    
    return 0;
}
```

## C++ 动态分配内存操作符 new && delete

:star: 指针是访问该种方法分配出的内存的唯一方式

``` cpp
int * pint = new int[10];
delete [] pint;
```

### new VS malloc

new会对对象进行初始化操作，而malloc不会

同理，delete会先对内存中的对象进行清理(析构)，再进行内存的回收

因此，需要注意，malloc要和free对应，new和delete对应



### Tips

- 不要delete同一块内存两次

  ``` cpp
  #include <iostream>
  #include <string>
  using namespace std;
  class test{
      private:
      int ohh;
      public:
      test(){ cout<<"test ctor"<<endl;}
      ~test(){cout<<"test dtor"<<endl;}
  };
  
  int main(){
      test* ptest = new test;
      delete ptest;
      delete ptest;
      return 0;
  }
  ```

  程序会暴毙,output

  ```
  test ctor
  test dtor
  test dtor
  ```

- 要用delete [] 来进行数组内存释放

- delete null指针是安全的

## 变量 && 内存

:facepunch:变量：

1. 全局变量(global vars)

   可以通过extern在其他文件中共享

2. 静态全局变量 (static global vars)

   控制了该变量的作用域只能在该文件中，不能跨文件访问

3. 局部变量(local vars)

4. 静态局部变量(static local vars)

   在程序运行期间可以进行值的保存，在函数第一次调用的时候进行初始化，再次访问到时忽略初始化过程。

5. 动态分配变量(allocated vars)

:facepunch:内存：

1. 全局区
2. 堆
3. 栈

:facepunch:规则：

1. 全局变量和静态变量都放在全局区中 
2. 事先不知道多大，由程序员在代码中决定大小的变量(malloc出来的变量)放在堆中
3. 局部变量在代码运行时进行压栈



## ::运算符  ->作用域

看完代码就懂了

``` cpp
#include <iostream>
using namespace std;
int a;
void f(){}
class S{
    public:
    int a;
    void f();
};
void S::f(){
    ::f(); //如果不这么写就会调用自己的f(),即递归
    ::a++;//全局变量a
    a++;//S内部的变量a++
}
int main(){
    S s;
    f();
    s.f();
}
```



## :star: Tips

- 关于日常弄混的 >>  <<

    ``` cpp
    int number;
    std::cin>>number;// 尖括号指到哪里就表示写到哪
    std::cout<<number<<std::endl;
    ```
    
- 分清赋值(Assignment)和构造(Ctor)

    ``` cpp
    str3 = str1+str2; //赋值
    str3(str1+str2);//构造
    ```

- 对象 和 指针

    ``` cpp
    string s //内存中会创建并且初始化，调用构造函数
    string *ps  //只声明了一个指针，没有初始化操作，不会在内存中开辟一块空间
    ```

