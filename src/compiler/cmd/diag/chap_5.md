`-Wevenyting`將會提供clang所有的警告，遠超過`-Wall`和`-Wextra`的範圍
有些警告不太實用
拿上面的例子來當範例
``` bash
$ clang -c test.c -Weverything
test.c:1:14: warning: unused parameter 'value' [-Wunused-parameter]
    1 | int func(int value)
      |              ^
test.c:1:5: warning: no previous prototype for function 'func' [-Wmissing-prototypes]
    1 | int func(int value)
      |     ^
test.c:1:1: note: declare 'static' if the function is not intended to be used outside of this translation unit
    1 | int func(int value)
      | ^
      | static
2 warnings generated.
```
如果沒有必要，`-Wall`和`-Wextra`和`-Wpedantic`就是一個很好的Diagnostic bassic，之後再加上額外的檢查就可以了
