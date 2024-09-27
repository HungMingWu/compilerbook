雖然可以透過IDE直接做Code  analysis，不過也是可以直接在Command Line執行
``` c
void test() {
        int *p;
        *p = 0;
}
```
接著執行看看
``` powershell
$ cl /analyze test.c
Microsoft (R) C/C++ Optimizing Compiler Version 19.40.33813 for x64
Copyright (C) Microsoft Corporation.  All rights reserved.

test.c
test.c(3) : warning C6001: Using uninitialized memory 'p'.: Lines: 2, 3
test.c(3) : warning C4700: uninitialized local variable 'p' used
Microsoft (R) Incremental Linker Version 14.40.33813.0
Copyright (C) Microsoft Corporation.  All rights reserved.

/out:test.exe
test.obj
LINK : fatal error LNK1561: entry point must be defined
```
