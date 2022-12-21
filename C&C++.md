##### 1、extern

> 本质用于变量/函数声明

- 声明：说明变量/函数的类型信息，并没有分配内存

- 定义：干了声明干的事，另外加上分配内存，可认为定义是声明的超集；声明可多次，但定义只有一次

- 变量声明和定义

  ```c
  extern int a;	//声明
  int a;			//定义
  ```

- 函数声明

  ```c
  //声明
  extern void test(int);	//通常将extern省略，直接写成 void test(int); 
  //C标准库里面将函数都显式带上了extern，如 extern int printf (const char *__restrict __format, ...);
  
  //定义
  void test(int a) {
      
  }
  ```

- 常见误解：extern指找寻其他文件里的变量，当然这也是其中一种用法，真实情况应该是：告诉编译器，我声明一个变量，先用着，至于定义在哪，你在整个程序范围内找去（包括引入的其他文件），也就是extern将可见性扩展到整个程序

  ```c
  //在本文件中使用extern
  extern int a;
  int main(int argc, char** argv) {
      printf("%d\n", a);	//10
      exit(0);
  }
  int a = 10;
  ```

##### 2、static 函数/变量

> 注意书写位置，static 写在头文件和写在源文件的是不一样的

###### 1、写在源文件

定义在源文件内的 static 变量/函数，只能在这个源文件内使用，这时 static = private

###### 2、写在头文件

定义在头文件内的 static 变量：所有引入该头文件的源文件都会定义自己的 static 变量，而不是直接使用该头文件内的 static 变量。所以，也就造成了在头文件中定义 static 变量，其他包含该头文件的源文件也能使用 static 变量的假象。**结论**：避免在头文件中定义 static 变量，会造成变量多次定义浪费内存，经测试，两个源文件引入的 static 变量地址是不一样的。

定义在头文件内的 static 函数：所有引入该头文件的源文件都可以调用头文件内的 static 函数，有一点好处：static 函数在内存中只维持一份，而普通函数每次调用都会维持一份拷贝。**结论**：在头文件中可以定义 static 函数。嗯~~这一点存疑，先放这。

##### 3、宏定义define的使用

> 其核心在于文本替换，define也可以像函数那样传参数

###### 1、使用场景

- 场景一：句子太长了，换个短点的；或者定义一个更具有表达意义的词语

    ```c++
    #define HAVE_ERROR(args) ASSERT_EQ(args, topsSuccess)	
    #define print(args) std::cout << (args) << std::endl
    #define max(a, b) (a) > (b) ? (a) : (b)
    #define	MAXLINE	4096
    ```

- 场景二：定义一种模板，用于生成更多重复的代码

    ```c++
    #define WRAPPER(nums)                                    \
        TEST(random, test##nums) {                           \
            std::random_device r;                            \
            std::uniform_int_distribution<int> dist(0, 100); \
            int index = dist(r);                             \
            std::cout << index << std::endl;                 \
        }
    WRAPPER(1)
    WRAPPER(2)
    WRAPPER(3)
    WRAPPER(4)
    ```

- 注意以下几个关键字：const/constexpr、typedef、template，#define可以部分覆盖（甚至完全覆盖）他们的使用场景，但意义不同，当然还是要针对应用场景选择对应的关键字

###### 2、define中的特殊符号

```c++
#define Conn(x,y)  x##y 	//Conn(test, Name)=testName
#define ToString(x) #x		//ToString(123)="123"
#define ToChar(x)  #@x
```

- ##：将前后拼接到一块
- #：转换为字符串
- #@：转换为字符，好像并不支持
- \：代表换行，宏定义太长，一行写不完，注意最后一行无需加 \
- 注意：当宏参数是另一个宏的时候，需要注意的是凡宏定义里有用 # 或 ## 的地方宏参数不会再展开

##### 4、virtual

> 用于多态，人话：不加 virtual 看等号左边，加 virtual 后看等号右边，非人话：虚函数的调用取决于指向或者引用的对象的类型，而不是指针或者引用自身的类型

不加 virtual

```c++
class parent {
public:
    void show() {
        cout << "parent" << endl;
    }
};
class son : public parent {
public:
    void show() {
        cout << "son" << endl;
    }
};
int main() {
    son* s = new son();
    parent* p = new son();
    s->show();  //son
    p->show();  //parent
}
```

加 virtual

```C++
class parent {
public:
    virtual void show() {
        cout << "parent" << endl;
    }
};
class son : public parent {
public:
    void show() {
        cout << "son" << endl;
    }
};
int main() {
    son* s = new son();
    parent* p = new son();
    s->show();  //son
    p->show();  //son
}
```

##### 5、多线程生成不一样的随机数

```c++
#include <gtest/gtest.h>
#include <random>
#include <iostream>

//dist(0, 100)，左右都为闭区间[0, 100]
#define WRAPPER(nums)                                    \
    TEST(random, nums) {                                 \
        std::random_device r;                            \
        std::uniform_int_distribution<int> dist(0, 100); \
        int index = dist(r);                             \
        std::cout << index << std::endl;                 \
    }
WRAPPER(1)
WRAPPER(2)
WRAPPER(3)
WRAPPER(4)
//g++ xxx.cpp -lgtest -lgtest_main -lpthread
```

##### 6、template

> 写的很好：[C++ Templates Tutorial (fiu.edu)](http://users.cis.fiu.edu/~weiss/Deltoid/vcstl/templates)
###### 1、函数模板和类模板的区别

- 模板使用时，函数模板可以省略模板参数类型，由编译器根据传入的函数实参推断模板类型，但对于类模板，必须显示指出模板参数类型；当然函数模板省略模板参数类型时，只能省略最右边的几个
- 特例化方面：函数模板只支持完全特例化，类模板支持完全特例化和部分特例化

###### 2、非类型参数（nontype parameter）

> 非类型参数即显式指出类型的模板参数，只能是整型（各种 int 和 bool ）、指针以及（左值）引用，别的像float都不行，而且绑定到非类型整型参数的实参必须是常量表达式

```c++
template <int a> 
void bar() {
   int arr[a];
   print(sizeof(arr));
}

int main() {
   const int a = 10;
   int b = 10;
   bar<a>();   //输出40
   bar<b>();   //error
}
```

```c++
template <unsigned M, unsigned N>
int compare(const char (&p)[M], const char (&q)[N]) {
   return strcmp(p, q);
}
//调用
compare("hello", "hi"); // M=6，N=3
```


###### 3、template <>，特例化
- template定义了一种所有类型通用的模板，但可能某些类型有特殊需求，需要特例化，比如 max(a, b)，对于数字直接比较就行，但对于字符串比较就需要调用函数strcmp；
- 特例化，即针对这些特殊的类型，额外定义的模板，定义时需显式指明模板类型；举例，一群普通员工吃一样的盒饭，领导的饭是特制的，且领导的饭上标注了名字，用以区分；
- 特例化分为完全特例化和部分特例化，完全特例化指所有的模板类型都需显式指出，部分特例化指其中一部分模板类型显式指出。template<> 表示完全特例化，对于函数模板，只能完全特例化，对于类模板，两种均可

```c++
//函数模板特例化，注意函数模板只能完全特例化

template <class T>
T max(T a, T b){
    return a > b ? a : b ;
}

// Specialization of max for char*
template <>
char* max(char* a, char* b){
    return strcmp(a, b) > 0 ? a : b ;
}
```
```c++
//类模板特例化，类模板完全特例化

//所有类型通用的模板，范例
template <typename T>
struct Stack {
   T a;
};

//针对int类型的特例
template <>		
struct Stack<int> {
   char a;
};

```

```c++
//类模板特例化，部分特例化

//base template class
template<typename T1, typename T2> 
class X {};

//partial specialization
template<typename T1> 
class X<T1, int> {}; 
```

- 甚至有极端情况，全是特例，这就跟函数重载有点像了

```c++
//基础模板
template <typename T> int length() { return 0; };
//全是特例
template <> int length<int>() { return 32; }
template <> int length<float>() { return 32; }
template <> int length<int16_t>() { return 16; }
template <> int length<int64_t>() { return 64; }
//...
    
//函数重载方式    
int testLen(int a) { return 32; }
int testLen(float a) { return 32; }
int testLen(int16_t a) { return 16; }
int testLen(int64_t a) { return 64; }
```

###### 4、可变模板参数列表

```c++
template<typename... Args>
void test(Args... dim) {	//扩展Args
   int a[] = {dim...};		//扩展dim
   for(int i = 0; i < sizeof...(dim); i++) {
      print(a[i]);
   }
   std::cout << '\n';
}
```

