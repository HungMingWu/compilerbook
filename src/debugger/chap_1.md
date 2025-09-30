在MSVC裡面，debug info 預設是和object code是分開的，而我們在linux上也可以模擬類似的操作，以下是我們的測試程式
``` cpp
#include <iostream>

int main() {
	std::cout << "Hello, World!" << std::endl;
	return 0;
}
```
首先編譯時帶上debug symbol
``` bash
$ g++ test.cpp -o test -g
```
接著使用`objcopy`抽出debug info出來
``` bash
$ objcopy --only-keep-debug test test.dbg
```
將原先的debug info從executable file抽出來
``` bash
$ strip -o test.stripped test
$ ./test.stripped
Hello, World!
```
接著使用gdb
``` bash
$ gdb test.stripped --quiet
Reading symbols from test.stripped...

(No debugging symbols found in test.stripped)
(gdb)

```
可以看出現在沒有debug symbols了，此時我們可以手動載入
``` bash
(gdb) symbol-file test.dbg
Reading symbols from test.dbg...
(gdb) start
Temporary breakpoint 1 at 0x1171: file test.cpp, line 4.
Starting program: /home/hm/test.stripped
warning: Probes-based dynamic linker interface failed.
Reverting to original interface.

Temporary breakpoint 1, main () at test.cpp:4
4               std::cout << "Hello, World!" << std::endl;

```
如果覺得每次在在gdb下symbol-file麻煩的話，可以透過objcopy，讓executable file和debug symbol關聯起來
``` bash
$ objcopy --add-gnu-debuglink=test.dbg test.stripped
```
這樣gdb就會感知到了
``` bash
$ gdb test.stripped --quiet
Reading symbols from test.stripped...
Reading symbols from /home/hm/test.dbg...
```

至於`# debuginfod`，是將debug info放在一個http server中，在此就不多說了，有興趣自行研究

