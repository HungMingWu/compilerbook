exception的優缺點這邊不提，這裡只討論如何開關exceptions
``` cpp
int main()
{
 #if __cpp_exceptions
  throw "123";
 #endif
}
```
使用gcc編譯的時候
```  bash
$ g++ test.cpp -o test -fno-exceptions
```
可以正常運作
不過如果我們改成這樣
``` cpp
int main()
{
  throw "123";
}
```
重新使用`-fno-exceptions`編譯會跳出錯誤
``` bash
test.cpp: In function ‘int main()’:
test.cpp:4:9: error: exception handling disabled, use ‘-fexceptions’ to enable
    4 |   throw "123";
      |         ^~~~~
```
clang也同樣支援這個command
#### About MSVC
在MSVC中，Exception Handling是透過`/EH`來控制
可以參考[ Exception handling in MSVC]((https://learn.microsoft.com/en-us/cpp/cpp/exception-handling-in-visual-cpp)j中的資料，不過如果沒有涉及Windows SEH或是MFC的話，使用**C++ exception handling**即可
除此之外，MSVC沒有類似於`-fno-exceptions`等價的命令，意味著Exception Handling一定存在，只是處理的方式可能改變


