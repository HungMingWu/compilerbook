這也是gcc/clang獨有的Extensiion
以下的程式有什麼問題
``` c
#include <stdio.h>
#define max(a, b)( ((a) > (b)) ? (a) : (b))
int main()
{
        int a = 3;
        int b = 5;
        printf("max(3, 5) = %d\n", max(a++, b++));
}
```
可以看到`(b)`被重複計算，所以答案變成６
而這問題可以透過inline function，或是這邊介紹的`Statement Expression`來解決
### Statement Expression 
在 GNU C 中，定義了 compound statement ，由 { 和 } 所圍起，內含多個 statements ，如果將 compound statement 再用 ( ) 包起來的話，這就是一個 statement expression ，而最後一個statement必須改成expression的形式，例如
``` c
#include <stdio.h>
#define max(a,b) \
  ({int _a = (a), _b = (b); _a > _b ? _a : _b; })

int main()
{
        int a = 3;
        int b = 5;
        printf("max(3, 5) = %d\n", max(a++, b++));
}
```
最後一個statement就少了`return`存在
