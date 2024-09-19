#### `-Wextra`
包含其他次要，但可能還是錯誤的警告
修改一下以上的範例
``` c
int func(int value)
{
        int v = 5;
        return v;
}
```
同樣編譯它
``` bash
$ clang -c test.c -Wall
$ clang -c test.c -Wextra
test.c:1:14: warning: unused parameter 'value' [-Wunused-parameter]
    1 | int func(int value)
      |              ^
1 warning generated.
```
可以看出，未使用的參數必須透過`-Wextra`才能測出
所以一般使用上，`-Wall`和`-Wextra`通常一起使用，才能測出最多問題
