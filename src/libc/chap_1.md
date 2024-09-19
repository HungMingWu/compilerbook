glibc引進了`_FORTIFY_SOURCE`用來捕嘬嚴重的secuity error，例如
``` c
#include <stdio.h>  
#include <string.h>  
  
char a[4];  
  
int main(int argc, char *argv[]) {  
  strcpy(a, argv[1]);  
  puts(a);  
}
```
要開啟Runtime check有兩個必要條件
- 加上`_FORTIFY_SOURCE`的Marco
- 開啟最佳化，至少`-O1`
不過就`_FORTIFY_SOURCE`的處理，兩個編譯器就目前版本的測試情況有所不同
- gcc13：就算不加`_FORTIFY_SOURCE`，gcc也會預設為3
- clang20：一定要加`_FORTIFY_SOURCE`

至於`_FORTIFY_SOURCE`的Level，越大表示檢測能力越強，不過code size跟performance也會受到影響，因此需要斟酌使用
#### Comparsion between `_FORTIFY_SOURCE` and sanitizers
`_FORTIFY_SOURCE`能處理的狀況有限，對於Bug的偵測也不如sanitizer．部過他的開銷很小，易於使用
