gcc和clang都提供了static analyzer的功能，不過在指令上不一樣
### Test example
``` c
#include <stdio.h>
#include <stdlib.h>

int main()
{
        int *i = (int*)malloc(sizeof(int));
        *i = 1;
        printf("Hello, World! %d\n", *i);
        return 0;
}
```
gcc的使用方式
``` bash
$ gcc --analyzer  test.c
test.c: In function ‘main’:
test.c:7:12: warning: dereference of possibly-NULL ‘i’ [CWE-690] [-Wanalyzer-possible-null-dereference]
    7 |         *i = 1;
      |         ~~~^~~
  ‘main’: events 1-2
    |
    |    6 |         int *i = (int*)malloc(sizeof(int));
    |      |                        ^~~~~~~~~~~~~~~~~~~
    |      |                        |
    |      |                        (1) this call could return NULL
    |    7 |         *i = 1;
    |      |         ~~~~~~
    |      |            |
    |      |            (2) ‘i’ could be NULL: unchecked value from (1)
    |
```
clang則是
``` bash
$  clang --analyze  test.c
test.c:8:8: warning: Potential leak of memory pointed to by 'i' [unix.Malloc]
    8 |         printf("Hello, World! %d\n", *i);
      |         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
1 warning generated
```
至於分析的結果，就需要更多的實踐跟研究了，不在本文討論之間


