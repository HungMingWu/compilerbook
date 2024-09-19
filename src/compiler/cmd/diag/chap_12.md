看著以下程式碼
``` c
#include <stdint.h>

int main(int argc, char** argv)
{
        char* p1 = 0;
        p1++;
        void* p2 = 0;
        p2++;
        return 0;
}
```
根據標準，不允許`void*`的指標做數字運算
不過這是gnu的extension，假設void的size為1
``` bash
$ clang test.c -c -Wpointer-arith
test.c:8:4: warning: arithmetic on a pointer to void is a GNU extension [-Wgnu-pointer-arith]
    8 |         p2++;
      |         ~~^
1 warning generated.
```
除非必要，盡量少用這種非標準的extension
