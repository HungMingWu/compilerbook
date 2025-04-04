目前只有MSVC和clang有實作自己的lifetimebound attribute
測試程式
``` cpp
#ifndef __has_cpp_attribute
    #define lifetime_bound
#elif __has_cpp_attribute(msvc::lifetimebound)
    #define lifetime_bound [[msvc::lifetimebound]]
#elif __has_cpp_attribute(clang::lifetimebound)
    #define lifetime_bound [[clang::lifetimebound]]
#elif __has_cpp_attribute(lifetimebound)
    #define lifetime_bound [[lifetimebound]]
#else
    #define lifetime_bound
#endif

struct Y {
        int& get() lifetime_bound;
};

void f() {
    int& r = Y{}.get();
}

```
編譯之後會出現warning
``` cpp
$ clang test.cpp  -c
test.cpp:21:14: warning: temporary bound to local reference 'r' will be destroyed at the end of the full-expression [-Wdangling]
   21 |     int& r = Y{}.get();
      |              ^~~
1 warning generated.
```
clang還提供了另一個有用的ifetime attribute
一個是`lifetime_capture_by`，將一個參數的lifecycle綁到另一個參數上

``` cpp
void addToSet(std::string_view a [[clang::lifetime_capture_by(s)]], std::set<std::string_view>& s) {
  s.insert(a);
}
void use() {
  std::set<std::string_view> s;
  addToSet(std::string(), s); // Warning: object whose reference is captured by 's' will be destroyed at the end of the full-expression.
  //       ^^^^^^^^^^^^^
  std::string local;
  addToSet(local, s); // Ok.
}
```
會出現以下warning
``` bash
test.cpp:9:12: warning: object whose reference is captured by 's' will be destroyed at the end of the full-expression [-Wdangling-capture]
    9 |   addToSet(std::string(), s);
      |            ^~~~~~~~~~~~~
1 warning generated.

```

