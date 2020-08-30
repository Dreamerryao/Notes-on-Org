# :no_mouth:引用

## 新手村

``` cpp
//type & refVar = existVar
char c;
char &r = c;
```

- 相当于对原始变量进行了别名,引用变量和原变量的地址是相同的，操作的是同一块内存空间，因此对引用变量的修改会反映到原始变量中

  ``` cpp
  #include <iostream>
  using namespace std;
  int main(){
      int y = 2;
      int &h = y;
      int &z = h;
      int *p = &h;
     	cout<<&h<<endl;
      cout<<&y<<endl;
      cout<<&z<<endl;
      return 0;
  }
  ```

  ```
  0x6ffe0c
  0x6ffe0c
  0x6ffe0c
  ```

- **当引用被创建时，它必须被初始化，后面的 = 都会被认为是赋值运算符**

- 一旦引用与某对象进行了绑定，就不能更改，这点和指针不同 ->指针可以指向别的变量

- 不可能有NULL引用，必须确保引用是和一块合法的存储单元相关联

- **如果函数里参数位置上是一个引用，该位置一定要放入一个真实存在的参数**，因为在函数调用时会进行绑定，如果没有实际的地址就不能完成引用的绑定。

  这里不能写入计算值！！！

  ```cpp
  #include <iostream>
  using namespace std;
   int f(int &x){
       return x++;
  }
  int main(){
      int y = 2;
      int z = f(y*3);
      return 0;
  }
  ```

> [!DANGER|style:flat|label:Error]
>
> invalid initialization of non-const reference of type 'int&' from an rvalue of type 'int'
>
> 非常量引用初始值必须为左值

## pointer VS reference

> 指针是独立于现有对象的，而引用则是与现有的变量绑定，作为别名出现
>
> :star: 可以把指针指向别的对象，而不能更改引用到新的地址中。
>
> **指针可空，引用不可空**

## 进阶规则

> [!TIP|style:flat|label:Tips]
>
> 不能用指针变量指向一个引用，但可以对指针变量来进行引用

``` cpp
#include <iostream>
using namespace std;

void f(int * &p){
    p++;
}
int main(){
    int y = 2;
    int &h = y;
    int *p = &h;
    cout<<p<<endl;
    f(p);
    cout<<p<<endl;
   
    return 0;
}
```

```
0x6ffe14
0x6ffe18
```



> [!DANGER|style:flat|label:Error]
>
> cannot declare pointer to 'int&'



## 扩展： 常量引用

``` cpp
#include <iostream>
using namespace std;

void f(int &){
}
void g(const int &){
}
int main(){
    //f(1);//Error invalid initialization of non-const 
    //reference of type 'int&' from an rvalue of type 'int'
    g(1);
    return 0;
}
```

> [!TIP|style:flat]
> f(1)在调用时发生以下动作: 1.为int分配一个存储单元，同时将其值赋值为1，并产生一个地址和引用绑定。而存储的内容一定是const,因为**改变它没有任何意义**。
>
>因此，编译器对于所有的临时对象，都会认为他们是不可存取的，因为我们在传入参数后，这个临时对象就失去了作用，因此不是const引用就会报错。

因此，上面提到的错误可以添加const 来解决,当然前提是我们知道函数内部并不会对对象产生更改。