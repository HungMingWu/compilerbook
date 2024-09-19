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

