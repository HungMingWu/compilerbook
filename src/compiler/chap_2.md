在這個方面，由於這非標準，Compilier有不同的考量
- gcc有 trampoline
- clang有blocks
- MSVC的C語言模式不支持，請用C++的lambda
#### gcc
gcc的技術叫做`trampoline`，屬於gcc特有的功能
不過由於安全問題，盡量不要用
``` c
#include <stdio.h>
int outer(int x)
{
    int nested(int y)
    {
        return x + y;
    }
    return nested(3);
}

int main()
{
        printf("Result is %d\n", outer(5));
}
```
### clang
clang的方式叫做blocks，靈活性和安全性都高於gcc，不過仍然是非標準，且要有其他library的搭配
``` c
#include <stdio.h>

int outer(int x)
{
    int (^nested)(int) = ^(int y) {
        return x + y;
    };

    return nested(3);
}

int main()
{
    printf("Result is %d\n", outer(5));
}
```
編譯的時候要特別加上
``` bash
$ clang test.c -o test -fblocks -lBlocksRuntime
```

