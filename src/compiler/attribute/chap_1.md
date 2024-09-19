在C/C++發展歷史之中，gcc引進了`__attribute__`作為編譯氣指令，指示編譯契作某些高階操作，例如
``` c
struct packed_str  
{  
	uint8_t x;  
	uint16_t y;  
}__attribute__ ((packed));
```
之後clang也沿襲了gcc的這部分，而MSVC一直沒做，這情況在C++11後改變
C++11將Attribute列為標準的一部分，而最新的C23也正式將Attribute加入標準
因此上面的範例可以寫成
``` c
struct packed_str
{
        uint8_t x;
        uint16_t y;
} [[gnu::packed]];
```
這解決掉原先的痛點
- 要用Preprocessor來過濾MSVC不支持的Case，例如

``` c
#ifdef WIN32
#define PACKED
#else
#define PACKED __attribute__ ((packed))
#endif
struct packed_str  
{  
	uint8_t x;  
	uint16_t y;  
} PACKED;
```
- 當Compilier看到不支持的attribute，會忽略看不懂的地方，只支持自己支持的attribute
	- 所以三家Compiler有自己獨有的attribute
		- 例如`[[msvc::forceinline]]`，`[[clang::scoped_lockable]]`等
- AttributeList可以有一個以上的Attribute

之後就用心的語法來寫範例
