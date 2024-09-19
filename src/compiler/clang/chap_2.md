``` cpp
int main(int argc, char *argv[])  
{  
    int a = 1;  
    int b = 2;  
    a += b;  
    return 0;  
}
```

``` bash
$ clang -emit-llvm -S test.cpp -o test.ll
```
產生的`test.ll`就是LLVM IR 
#### Generate Bitcode from IR
``` bash
$ llvm-as test.ll -o test.bc
```
現在用file檢查，他就是LLVM Bitcode了
``` bash
$ file test.bc
test.bc: LLVM IR bitcode
```
#### Generate Assembly Code from IR or Bitcode
``` bash
$ llc test.ll -o test.ll.s
$ llc test.bc -o test.bc.s
```
#### Disassemble from Bitcode to IR
``` bash
$ llvm-dis test.bc -o test.bc.ll
```
#### Execute bitcode
``` bash
$ lli test.bc
```
#### Link multiple bitcode into bigger one
現在我們將程式改寫一下，分成test1.cpp和test2.cpp兩個檔案
以下是test1.cpp
``` cpp
int func(int a)
{
    a = a * 2;
    return a;
}
```
test2.cpp
``` cpp
#include <stdio.h>

int func(int a);

int main(void)
{
    int num = 5;
    num = func(num);
    printf("number is %d\n", num);

    return 0;
}
```
將著產生兩個不同的BItcode並合併
``` bash
$ clang -emit-llvm -S test1.cpp -o test1.ll
$ clang -emit-llvm -S test2.cpp -o test2.ll
$ llvm-as test1.ll -o test1.bc
$ llvm-as test2.ll -o test2.bc
$ llvm-link test1.bc test2.bc -o test.bc
```
接著用lli驗證一下結果
``` bash
$ lli test.bc
number is 10
```
