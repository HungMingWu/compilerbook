Sanitizer家族中最有名的成員，三大編譯器都支持了
``` cpp
#include <stdio.h>
int x[100];
int main() {
        x[100] = 5;
        return 0;
}
```
gcc和clang的使用方式如出一轍
``` bash
$ g++  test.cpp -o test -fsanitize=address
```
而MSVC的使用方式就有點不同
``` powershell
$ cl test.cpp /fsanitize=address /Zi
```
就會出現類似這樣的結果
``` bash
=================================================================
==2749==ERROR: AddressSanitizer: global-buffer-overflow on address 0x62bfb5256230 at pc 0x62bfb5253203 bp 0x7fff22b36c10 sp 0x7fff22b36c00
WRITE of size 4 at 0x62bfb5256230 thread T0
    #0 0x62bfb5253202 in main (/home/hm/test+0x1202) (BuildId: bc3ed38be83040e6c95459317d67a202d9723b23)
    #1 0x7db47002a1c9 in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58
    #2 0x7db47002a28a in __libc_start_main_impl ../csu/libc-start.c:360
    #3 0x62bfb5253104 in _start (/home/hm/test+0x1104) (BuildId: bc3ed38be83040e6c95459317d67a202d9723b23)

0x62bfb5256230 is located 0 bytes after global variable 'x' defined in 'test.cpp:2:5' (0x62bfb52560a0) of size 400
SUMMARY: AddressSanitizer: global-buffer-overflow (/home/hm/test+0x1202) (BuildId: bc3ed38be83040e6c95459317d67a202d9723b23) in main
```
再依照結果去分析哪裡錯了
