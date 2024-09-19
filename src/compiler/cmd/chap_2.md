在gcc/clang中，難免有使用gdb debug的需求
這時候就會在編譯的時候加上debug info
``` bash
$ gcc -glevel test.c -c
```
目前有四個level
- -`g0`：不帶任何debug info，基本上沒用
- `-g1`：只有極少數的debug info，例如行號，function name等
- `-g2`:  gcc預設的level，`-g`就等同於`-g2`．包含大部分需要的資訊，例如變數名稱，Data Type等
-  `-g3`: 最詳盡的Level．除了`-g2`的所有妹榮之後．還有macro definition

另外還有一個選項
- `-ggdb`：為gdb生成額外的debug info，不過僅供gdb使用，不能用於lldb

#### About MSVC
MSVC沒有控制debug level的命令，唯一可以控制的是debug info儲存於哪的選項，例如`/Z7`，`/Zi`等，需要的時候再去查官方文件

