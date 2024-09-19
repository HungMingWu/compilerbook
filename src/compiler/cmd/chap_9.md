在很多要求安全的程式中，不希望祕密被人擷取
而security就是最近受人關注的重點之一，例如以下程式
``` cpp
#include <iostream>
template<int N>
auto print(char (&a)[N]) { std::cout << std::string_view{a,N} << "\n"; }

auto f1() {
    char a[] = {'s', 'e', 'c', 'r', 'e', 't' };
    print(a);
}

auto f2() {
    char a[6];
    print(a);  // today this likely prints "secret"
}

auto f3() {
    char a[] = {'0', '1', '2', '3', '4', '5' };
    print(a);  // overwrites "secret" (if only just)
}

int main() {
    f1();
    f2();
    f3();
}
```
在當今的C++標準當中，在未初始化就先讀取就是個**undefined behavior**，而在C++26之後，它被明確定義成**erroneous behavior**．
不過在現在的情況之下，還是有辦法緩解這種情形
在gcc/clang中，你可以這麼做
``` bash
$ g++ test.cpp -o test -ftrivial-auto-var-init=pattern
```
這裡的`trivial-auto-var-init`有三個選項
``` bash
-ftrivial-auto-var-init=[pattern|zero|uninitialized]
```
可以控制未初始化的變數的強制初始化方式，如果是`uninitialized`就跟當今行為完全相同

MSVC也有類似的功能 **/RTC1**
``` powershell
$ cl  /c test.cpp /std:c++latest /RTC1
$ link test.obj /OUT:test.exe
```
