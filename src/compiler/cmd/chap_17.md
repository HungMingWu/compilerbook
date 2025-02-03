由於關於Bufer overflow導致的secuity issue越來越多，因此大家都關心起了安全性，而Compiler最常用的手段就是確認Calling stack有沒有被破壞

一個很簡單的範例，複製的字串長度超過array的長度，因此覆蓋了原先Stack的值
``` c
#include <string.h>
#include <stdio.h>

int main(void)
{
    char array[2] = {0};

    strcpy(array, "stackwilloverflow");

    return 0;
}
```
Compilerr就是在Calling stack中插入幾個值，然後檢查這些值有沒有被破壞，達成最基本的安全檢查
一般的編譯方式
``` bash
$ clang test.c -o test
$ ./test
Aborted (core dumped)
```
開啟stack-protector之後，gcc和clang的用法完全相同
``` bash
$ clang test.c -o test -fstack-protector-strong
$ ./test
*** stack smashing detected ***: terminated
Aborted (core dumped)
```
會告訴你是Stack出問題了

#### About MSVC
MSVC也有類似的功能`/GS`，不過實際測試的結果不盡人意，還是靠gcc/clang還偵測吧


