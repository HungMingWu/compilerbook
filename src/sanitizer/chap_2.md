UndefinedBehavior Sanitizer是在Runtime檢查那些動作會造成C/C++最惡名昭彰的UndefinedBehavior，雖然不能完全消除程式中的所有UndefinedBehavior，不過一定要盡量減少到最小

現在只有gcc/clang有支持，MSVC是不支持的
一樣給個範例
``` cpp
#include <iostream>

int main(int argc, char **argv) {
        int k = 0x7fffffff;
        k += argc;
        return 0;
}
```
進行編譯，並觀察結果
``` bash
$ g++ -fsanitize=undefined test.cpp -o test
$ ./test
test.cpp:5:4: runtime error: signed integer overflow: 2147483647 + 1 cannot be represented in type 'int'
```
