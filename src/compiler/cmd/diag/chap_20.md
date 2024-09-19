#### -Wcast-qual
當cast的時候，丟棄了type qualifier，例如const．編譯器會發出警告
例如
``` c
void func(const char* str)
{
        char *p = (char*)str;
}
```

``` bash
$ gcc test.c -c -Wcast-qual
test.c: In function ‘func’:
test.c:3:19: warning: cast discards ‘const’ qualifier from pointer target type [-Wcast-qual]
    3 |         char *p = (char*)str;
      |         
```

#### -Wcast-align
關於這個選項，gcc和clang的行為有些微差異

假設target type的alignment比source type大
gcc:
- `-Wcast-align`假設target machine支持這種cast，不發出警告
- `-Wcast-align＝strict` 一律發出警告
clang:
- `-Wcast-align`的行為和gcc的`-Wcast-align＝strict` 相同`

``` c
int main()
{
        char data[10];
        int ptr = (int *)data;
}
```
``` bash
$ clang test.c -c -Wcast-align
test.c:4:13: warning: cast from 'char *' to 'int *' increases required alignment from 1 to 4 [-Wcast-align]
    4 |         int* ptr = (int *)data;
      |                    ^~~~~~~~~~~
1 warning generated.
```


