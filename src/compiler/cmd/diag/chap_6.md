當函數回傳的type被轉換成不匹配的type，即產生警告
``` c
#include <stdint.h>

char* func();
int main(int argc, char** argv)
{
        int* p = func();
        return 0;
}
```
這邊看起來就是個錯誤，所以編譯器會吐出警告
``` bash
$ gcc test.c -c -Wbad-function-cast
test.c: In function ‘main’:
test.c:6:18: warning: initialization of ‘int *’ from incompatible pointer type ‘char *’ [-Wincompatible-pointer-types]
    6 |         int* p = func();
      |       
```
