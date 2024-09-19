在編譯程式碼的時候，可以選擇用何時的C/C++的標準規則來編譯
使用gcc/clang的時，使用`-std`來指定要使用的標準，例如
``` bash
$ g++ test.cpp -o test -std=c++11
```
這裡能接受的std種類有C語言的version，C++的標準，以及gnu自訂的標準等，依照需求來決定
至於MSVC也有類似的功能，叫做`/std:xxx`，`std:c++latest`和`/std:clatest`分別對應當今MSVC所支持的所有語言功能


