這個警告也是針對C++的，看一下這段Code
``` cpp
struct Base {
        virtual ~Base() = default;
        virtual void doSomething() = 0;
};
struct Derived : public Base {
        void doSomething(int) {}
};
```

``` bash
$ gcc test.cpp -c -Woverloaded-virtual
test.cpp:3:22: warning: ‘virtual void Base::doSomething()’ was hidden [-Woverloaded-virtual=]
    3 |         virtual void doSomething() = 0;
      |                      ^~~~~~~~~~~
test.cpp:6:14: note:   by ‘void Derived::doSomething(int)’
    6 |         void doSomething(int) {}
      |           
```
這是防止你的原本打算overwrite function，但變成overload function了，可能是打錯字，也可能你本來就是要這樣設計



