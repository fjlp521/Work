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

##### 2、static 函数

> static 函数 = private 函数，该函数仅限本文件使用