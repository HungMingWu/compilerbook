如果有老一點的程式碼，大概會看到這種語法
``` c
#include <stdint.h>

int main(argc, argv)
int argc;
char**argv;
{
        return 0;
}
```
由於C有五十年以上的歷史，這種語法以前是支持的，不過隨著時代的演進，這種定義方式即將退出時代舞台
``` bash
$ gcc test.c -c -Wold-style-definition
test.c: In function ‘main’:
test.c:3:5: warning: old-style function definition [-Wold-style-definition]
    3 | int main(argc, argv)
      |     ^~~~
```
clang給的警告更清楚
``` bash
$ clang test.c -c
test.c:3:5: warning: a function definition without a prototype is deprecated in all versions of C and is not supported in C23 [-Wdeprecated-non-prototype]
    3 | int main(argc, argv)
      |     ^
1 warning generated.
```
C23之後就不再支援了
