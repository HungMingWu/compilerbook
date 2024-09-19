這是有關C++的，當你的class有virtual function時，卻沒有virtual destructor，這樣會造成Resource Leak
``` cpp
struct Base {
        ~Base() = default;
        virtual void doSomething() = 0;
};
struct Derived : public Base {
        void doSomething() {}
};
```

``` bash
$ clang test.cpp -c -Wnon-virtual-dtor
test.cpp:2:2: warning: 'Base' has virtual functions but non-virtual destructor [-Wnon-virtual-dtor]
    2 |         ~Base() = default;
      |         ^
test.cpp:5:8: warning: 'Derived' has virtual functions but non-virtual destructor [-Wnon-virtual-dtor]
    5 | struct Derived : public Base {
      |        ^
2 warnings generated.
```
