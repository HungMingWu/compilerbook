在gcc和clang中，你可以使用`-I`來指定compilier在額外的目錄下搜尋header files
例如
``` bash
$ gcc -I /path/to/extra/system/includes -o hello hello.c
```
#### -isystem
類似於`-I`指令，找尋不過是搜尋系統所需要的include path．
例如
``` bash
$ gcc -isystem /path/to/extra/system/includes -o hello hello.c
```
跟`-I`唯一的差別就是`-I`找到的header file如果有warning會列出來，而`-isystem`預設不會
可以寫個程式測試一下
header file的部分
``` c
nline void foo(int unused) {}  // warning: unused parameter 'unused'
```
source code的部分
``` 
#include <test.h>
```
然後編譯它
``` bash
$ gcc test.c -c -I. -Wall -Wextra
In file included from test.c:1:
./test.h: In function ‘foo’:
./test.h:1:21: warning: unused parameter ‘unused’ [-Wunused-parameter]
    1 | inline void foo(int unused) {  // warning: unused parameter 'unused'
$ gcc test.c -c -isystem . -Wall -Wextra
```
不過加上`-Wsystem-headers`就跟`-I`有一樣的行為了
clang在這方面跟gcc完全相同

### About MSVC
MSVC的情形，不僅命令不同，對Warning的處理也不同
拿上面的範例來試試
``` powershell
$ cl /c test.c /I .
```
這裡是用`/I`來指定Include Path，不過不同的是他不會僅告Warning，必須手動將Warning Level打開成4才有用
``` powershell
$ cl /c test.c /I . /W4
```
而也有類似`-isystem`的對應功能，`/external:I:I`，不過跟上面`/I`的不同的是，external的Warning level是必須的
``` powershell
$ cl /c test.c /external:I . /external:W4
```
如果不想跳出Warning的話，將Warning level調低就可以


