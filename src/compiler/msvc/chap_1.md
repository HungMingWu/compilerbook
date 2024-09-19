MSVC最著名的，就是他不按照標準做很久了，為了Backward compatible，它必須支持它自己的非標準語法，例如這個
``` cpp
#include <stdio.h>
int main() {
        int array[] = {1, 2, 30, 40};
        for each (int i in array)
        {
                printf("%d\n", i);
        }
}
```
為了跟標準相容，MSVC提供了`/permissive-`，告訴你這些地方不合標準，請你盡快修正
因此上面會出現類似這樣的錯誤
``` powershell
$ <source>(4): error C4496: nonstandard extension 'for each' used: replace with ranged-for statement
```

