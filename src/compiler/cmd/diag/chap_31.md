gcc之後的版本，對Diagnostics的輸出做了很多改進
例如以下這個程式
``` c
void test (int n)
{
    while (n)
    {
    }
}
```
使用`gcc -fanalyzer`在不同版本有不同的輸出
以下是gcc 14輸出的一部分
``` bash
      |            **(1)** infinite loop here
```
而gcc 15則是
``` bash
| **(1)** ⚠️ infinite loop here
```
唯一的差異就是輸出emoji符號，這功能好不好目前不好評論，有些環境下不能正常顯示emoji或是unicode，這功能就是用來調整文字輸出的
**-fdiagnostics-text-art-charset=[none|ascii|unicode|emoji]**
預設就是emoji
