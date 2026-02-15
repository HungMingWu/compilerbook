如果寫過golang，對defer絕對不陌生
在C語言實作defer macro的文章搜索就能就是一大堆，不過都不適語言層面的作法
C2Y有望納入標準，clang最先實作了TS供大家試用

``` c
#include <stdlib.h>
int main() {
  int * p = malloc(sizeof *p);
  _Defer free(p);
}
```
編譯的時候要帶`-fdefer-ts`

