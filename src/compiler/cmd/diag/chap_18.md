`Variable-length arrays`不一定是個好主意，帶來的問題可能比解決的問題還多
``` c
#include <stdio.h>

int main(int argc, char** argv)
{
        int v[argc];
}
```
``` bash
$ clang test.c -c -Wvla
test.c:5:8: warning: variable length array used [-Wvla]
    5 |         int v[argc];
      |               ^~~~
1 warning generated.
```
不過是否改變還是得視情況而定
