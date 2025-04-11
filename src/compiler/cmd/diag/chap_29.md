當我們要分析錯誤的時候，有時候是訊息越多越好
``` c
#include <stdlib.h>

void test(void *ptr)
{
        free(ptr);
        free(ptr);
}
```
GCC 10之後，會吐出很多訊息
``` bash
$ gcc test.c -c -fanalyzer
test.c: In function ‘test’:
test.c:6:9: warning: double-‘free’ of ‘ptr’ [CWE-415] [-Wanalyzer-double-free]
    6 |         free(ptr);
      |         ^~~~~~~~~
  ‘test’: events 1-2
    |
    |    5 |         free(ptr);
    |      |         ^~~~~~~~~
    |      |         |
    |      |         (1) first ‘free’ here
    |    6 |         free(ptr);
    |      |         ~~~~~~~~~
    |      |         |
    |      |         (2) second ‘free’ here; first ‘free’ was at (1)
    |

```
不過有時候我們不希望這些訊息佔據我們太多螢幕空間，這時候就希望減少訊息輛
`-fdiagnostics-plain-output`就是幫我們做這件事的
``` bash
$ gcc test.c -c -fanalyzer -fdiagnostics-plain-output
test.c: In function ‘test’:
test.c:6:9: warning: double-‘free’ of ‘ptr’ [CWE-415] [-Wanalyzer-double-free]
test.c:5:9: note: (1) first ‘free’ here
test.c:6:9: note: (2) second ‘free’ here; first ‘free’ was at (1)
```

