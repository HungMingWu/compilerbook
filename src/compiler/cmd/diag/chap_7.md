這檢查對C語言的printf/scanf特別有用，C++20之後已經有fmt了，所以C++Code盡量往那邊偏移
``` c
#include <stdio.h>
int main()
{
        printf("%d\n", "hello world\n");
        return 0;
}
```
現在的gcc/clang就算不開起這個選項，預設也是會檢查的
``` bash
$ clang test.c -c
test.c:4:17: warning: format specifies type 'int' but the argument has type 'char *' [-Wformat]
    4 |         printf("%d\n", "hello world\n");
      |                 ~~     ^~~~~~~~~~~~~~~
      |                 %s
1 warning generated.

```
不過預設是`-Wformat=1`，而設成2有更多的檢查
