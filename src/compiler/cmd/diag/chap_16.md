檢查`#if`裡面是否有未定義的Symbol
```c
#if ENABLE_THIS
#endif
int main()
{
        return 0;
}
```
``` bash
$ clang test2c -c -Wundef
test.c:1:5: warning: 'ENABLE_THIS' is not defined, evaluates to 0 [-Wundef]
    1 | #if ENABLE_THIS
      |     ^
1 warning generated.
```
