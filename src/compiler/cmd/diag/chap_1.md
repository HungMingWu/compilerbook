#### `-Wall`
用來偵測常見的Coding error，例如變數位初始化之前就使用
``` c
int func()
{
        int v;
        return v;
}
```
以下是可能的結果
``` bash
$ clang -c test.c -Wall
test.c:4:9: warning: variable 'v' is uninitialized when used here [-Wuninitialized]
    4 |         return v;
      |                ^
test.c:3:7: note: initialize the variable 'v' to silence this warning
    3 |         int v;
      |              ^
      |               = 0
1 warning generated.
```
