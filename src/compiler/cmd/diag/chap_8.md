警告一些編譯器看起來可疑的logic operation，可能是打錯字，或者是邏輯有問題
例如像這樣
``` c
#include <stdint.h>
int main(int argc, char** argv)
{
        if (argc < 0 && argc < 0) {
        }
}
```
``` bash
$ gcc test.c -c -Wlogical-op
test.c: In function ‘main’:
test.c:4:22: warning: logical ‘and’ of equal expressions [-Wlogical-op]
    4 |         if (argc < 0 && argc < 0) {
      |                  
```
