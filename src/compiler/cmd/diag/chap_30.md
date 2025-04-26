假設我們有這樣一個char array
``` c
int main()
{
	char foo[8] = "NUL-free";
}
```
陣列的長度無法在後面補上0，造成一個長度無法確定的string，可能不是我們想要的
GCC 15之後新增了這個功能，幫助Developer來找出錯誤
``` bash
$ gcc test.c -o test -Wunterminated-string-initialization
**<test>:** In function '**main**':
**<test>:7:16:** **warning:** initializer-string for array of '**char**' truncates NUL terminator but destination lacks '**nonstring**' attribute (9 chars into 8 available) [**-Wunterminated-string-initialization**]
    7 |  char foo[8] = **"NUL-free"**;
      |                **^~~~~~~~~~**
Compiler returned: 0
```
不過char array[]並非全部都是string，有些時候你只是要當個lookup table使用，另如linux kernel中的
``` c
static const char cachefiles_charmap[64] =
	"0123456789"			/* 0 - 9 */
	"abcdefghijklmnopqrstuvwxyz"	/* 10 - 35 */
	"ABCDEFGHIJKLMNOPQRSTUVWXYZ"	/* 36 - 61 */
	"_-"				/* 62 - 63 */
	;
```
如果編譯時加上`-Wunterminated-string-initialization`，這種Warning並非我們想要的，所以我們需要gcc專用的atttribute抑制這種warning，就像這樣
``` c
int main()
{
	__attribute__((__nonstring__)) char foo[8] = "NUL-free";
}
```

#### About MSVC
發現MSVC也有類似的功能
``` powershell
$ cl test.c /W4
Microsoft (R) C/C++ Optimizing Compiler Version 19.43.34809 for x64
Copyright (C) Microsoft Corporation.  All rights reserved.

test.c
test.c(3): warning C4295: 'foo': array is too small to include a terminating null character
test.c(3): warning C4189: 'foo': local variable is initialized but not referenced
Microsoft (R) Incremental Linker Version 14.43.34809.0
Copyright (C) Microsoft Corporation.  All rights reserved.
```
`Warning C4295`就是我們所關心的，至於抑制方法就有自己的一套了
``` c
int main()
{
	__pragma(warning(suppress : 4295))  char foo[8] = "NUL-free";
}
```

