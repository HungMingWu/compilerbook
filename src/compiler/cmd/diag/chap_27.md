對於C++ Safety要求越來越高，所以Compilier也需要偵測出更多的錯誤
### gcc
#### -Wdangling-pointer
``` c
int *p;
void store(int num) {
        p = &num;
}
```
``` bash
$ gcc test.c -c -Wdangling-pointer
test.c: In function ‘store’:
test.c:3:11: warning: storing the address of local variable ‘num’ in ‘p’ [-Wdangling-pointer=]
    3 |         p = &num;
      |         ~~^~~~~~
test.c:2:16: note: ‘num’ declared here
    2 | void store(int num) {
      |            ~~~~^~~
test.c:1:6: note: ‘p’ declared here
    1 | int *p;
      |      ^
```
這個問題是p指向了一個已經無效的變數`num`，所以變成`dangling pointer`
#### -Wdangling-reference
``` cpp
class C {
    int i_;
public:
    C();
    int & iRef();
};

void f() {
    const int & i = C().iRef();
}
```
這問題是`i`是一個reference，指向一個`C`物件的其中一部份，不過這個物件已經被銷毀了，所以是個`danlging reference`
看看gcc吐出的訊息
``` bash
$ g++ test.cpp -c -Wdangling-reference
test.cpp: In function ‘void f()’:
test.cpp:9:17: warning: possibly dangling reference to a temporary [-Wdangling-reference]
    9 |     const int & i = C().iRef();
      |                 ^
test.cpp:9:29: note: the temporary was destroyed at the end of the full expression ‘C().C::iRef()’
    9 |     const int & i = C().iRef();
      |                     ~~~~~~~~^~
```
不過這個問題可以更好的用lifetimebound attribute來做，可以參照attribute那部分
### clang
`-Wdangling`預設就是開啟了，所以不用特別帶參數進去
``` cpp
#include <string>
#include <string_view>
int main()
{
        std::string_view v = std::string();
}
```
``` bash
$ clang test.cpp -c
test.cpp:5:23: warning: object backing the pointer will be destroyed at the end of the full-expression [-Wdangling-gsl]
    5 |         std::string_view v = std::string();
      |                              ^~~~~~~~~~~~~
1 warning generated.
```
#### About MSVC
MSVC有自己的[ C++ Core Guidelines checkers](https://learn.microsoft.com/en-us/cpp/code-quality/using-the-cpp-core-guidelines-checkers)，需要的話透過IDE操作吧
`
