雖然在C++20之後，可以直接透過`source_location`來達成
``` cpp
#include <iostream>
#include <source_location>

void my_function() {
    auto loc = std::source_location::current();
    std::cout << "file: " << loc.file_name() << ", function name: " << loc.function_name() << ", line: " << loc.line() << "\n";

}
int main() {
    my_function();
    return 0;
}
```
不過還有廣大的舊C++專案，以及C語言受眾，以上的方法對他們不適用，因此介紹各大編譯器用Preprocessor的方案
先介紹三大編譯器相同的方案
#### Print File Name
``` 
std::cout << "file: " << __FILE__ << "\n";
```
#### Print Line Number
``` cpp
std::cout << "line: " << __LINE__ << "\n";
```
不同之處在於Function Name的部分
#### Print Function Name
GCC和Clang使用
``` cpp
std::cout << "function name: " << __PRETTY_FUNCTION__ << "\n";
```
MSVC的方案則是
``` cpp
std::cout << "function name: " << __FUNCSIG__ << "\n";
```
