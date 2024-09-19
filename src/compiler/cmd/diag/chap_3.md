#### `-pedantic` and `-Wpedantic`
兩者是一致的，`-pedantic`是古老的gcc流傳下來的命令，為了命令的一致性，才加入了`-Wpedantic`
`-Wpedantic`是用更嚴格的標準來檢查，幫助寫出更合乎標準的Code
例如
``` c
int func()
{
        return 5;
}
```
編譯會出現警告
``` bash
$ clang -c test.c -Wpedantic
test.c:1:9: warning: a function declaration without a prototype is deprecated in all versions of C [-Wstrict-prototypes]
    1 | int func()
      |         ^
      |          void
1 warning generated.
```
這是`-WAll`和`-Wextra`所偵測不到的，畢竟它的要求是符合標準
