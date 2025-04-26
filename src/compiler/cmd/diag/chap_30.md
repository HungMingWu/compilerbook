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
