##### 1、virtual

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

