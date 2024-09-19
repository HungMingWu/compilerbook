在編譯的時候，你也希望過濾掉某些Warning
這時候你就可以使用`-Wno-${warning_name}`了
以上面的pedantic範例來修改
``` bash
$ clang -c test.c -Wpedantic -Wno-strict-prototypes
```
這下就沒跳warning出來了
