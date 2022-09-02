##### 1、多文件共享变量

> 在其中一个文件中定义变量，在其余文件中声明使用该变量

```c++
// test.h
//在全局作用域中定义
int a = 10;
```

```c++
// a.cpp
//在任意作用域内声明
extern int a;
```

```c++
// b.cpp
extern int a;
```

##### 2、virtual

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

