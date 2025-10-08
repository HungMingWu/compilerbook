這裡的module指的不是C++20 module，而是在之前clang自行建立的擴充功能
而目前也只有clang支持這套機制
以下是一個範例，目錄結構是這個樣子
``` bash
project/
├─ include/
│  └─ foo.h
├─ module.modulemap
└─ main.cpp
```
簡單貼一下程式碼，畢竟不是重點
`include/foo.h`
``` cpp
#ifndef FOO_H
#define FOO_H

#include <string>
#include <iostream>

inline void say_hello(const std::string& name) {
    std::cout << "Hello, " << name << "!\n";
}
#endif
```
`main.cpp`
``` cpp
#include "foo.h"

int main() {
    say_hello("Clang Module");
    return 0;
}
```
而重點放在`module.modulemap`的內容
這就是所謂的`Module Map`，映射Module以及header file的關聯
``` bash
module Foo {
    header "include/foo.h"
    export *
}
```
這裡指定module `Foo`中包含了`foo.h`所有內容
編譯的話就是這個樣子
``` bash
$ clang++ -fmodules -fmodule-map-file=module.modulemap  -Iinclude main.cpp -o main
```
接著你就能在`~/.cache/clang/ModuleCache/`找到Binary PCM擋了

#### Reference
 [Clang  module documentation](https://clang.llvm.org/docs/Modules.html)
 [理解 Clang Module 和 Module Map 语法](https://zhuanlan.zhihu.com/p/602783297)
 [Clang Module](https://chuquan.me/2021/02/11/clang-module/)
