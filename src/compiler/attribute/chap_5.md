將某個函數或是某個變數標記成`deprecated`，任何使用到此相關的操作都會被報Warning，需要的話就改寫，以獲得更好的程式品質
``` c
#include <stdio.h>

struct X {
        [[deprecated("the value isn't used anymore")]] int value;
};

int main()
{
        struct X x;
        x.value = 1;
}
```
編譯並執行
``` bash
$ gcc -o test test.c
$ ./test
test.c: In function ‘main’:
test.c:10:9: warning: ‘value’ is deprecated: the value isn't used anymore [-Wdeprecated-declarations]
   10 |         x.value = 1;
      |         ^
test.c:4:60: note: declared here
    4 |         [[deprecated("the value isn't used anymore")]] int value;
      |                                                            ^~~~~
```
