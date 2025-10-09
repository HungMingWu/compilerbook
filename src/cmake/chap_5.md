這裡的Unity跟GameEngine沒有任何關係，而是CMake加速編譯時間的一種方式
#### 原理
假設我們Project有三個獨立的Implementation Unit
``` bash
$ g++ a.cpp -c -o a.o
$ g++ b.cpp -c -o b.o
$ g++ c.cpp -c -o c.o
$ g++ a.o b.o c.o -o main
```
進行三次編譯雖然能夠增加每個Implementation Unit的獨立性，但是造成資源和編譯時間的浪費，因此想出了這麼樣一個方法
``` bash
$ cat a.cpp b.cpp c.cpp > d.cpp
$ g++ d.cpp -o main
```
Unity build就是根據這樣的想法而生的

#### BATCH Mode
簡單的解決方案
``` cmake
add_library(example_library
            source1.cxx
            source2.cxx
            source3.cxx
            source4.cxx)

set_target_properties(example_library PROPERTIES
                      UNITY_BUILD_MODE BATCH
                      UNITY_BUILD_BATCH_SIZE 2
                      )
```
在上面標明了
- 我們使用的是Batch Mode
- 我們兩個兩個一起編譯
不過這樣子還是有點問題
例如`Anonymous Namespace
a.cpp
``` cpp
namespace {
    void print();
}
```
b.cpp
``` cpp
namespace {
    void print(); // compile error
}
```
當a和b一起編譯的時候，會產生問題，因此就有一個新的解決方式

#### Group Mode
``` cmake
add_library(example_library
            source1.cxx
            source2.cxx
            source3.cxx
            source4.cxx)

set_target_properties(example_library PROPERTIES
                      UNITY_BUILD_MODE GROUP
                      )

set_source_files_properties(source1.cxx source2.cxx source3.cxx
                            PROPERTIES UNITY_GROUP "bucket1"
                            )
set_source_files_properties(source4.cxx
                            PROPERTIES UNITY_GROUP "bucket2"
```
跟上面的方式差不多，不過
- 我們現在使用Group Mode
- 透過`set_source_files_properties`來分類
