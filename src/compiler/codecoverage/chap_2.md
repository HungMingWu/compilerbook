這個方法只適用於clang，還是之前gcov那個範例
``` c
#include <assert.h>
int div(int a, int b) {
        if (b == 0) {
                assert(0);
        } else {
                return a / b;
        }
}

int main()
{
        return div(4, 2);
}
```
編譯並執行
``` bash
$ clang -fprofile-instr-generate -fcoverage-mapping test.c -o test
$ ./test
```
不同於gcov，clang現在產生`default.profraw`
可以透過環境變數`LLVM_PROFILE_FILE`改變產生規則，這邊不詳細說明，以預設值繼續說明
接著產生coverage report`default.profdata`
``` bash
$ llvm-profdata merge default.profraw -o default.profdata
```
接著用之前介紹的`llvm-cov`來操作coverage report

``` bash
$ llvm-cov show ./test -instr-profile=default.profdata
    1|       |#include <assert.h>
    2|      1|int div(int a, int b) {
    3|      1|        if (b == 0) {
    4|      0|                assert(0);
    5|      1|        } else {
    6|      1|                return a / b;
    7|      1|        }
    8|      1|}
    9|       |
   10|       |int main()
   11|      1|{
   12|      1|        return div(4, 2);
   13|      1|}
```
和
``` bash
$ llvm-cov report ./test -instr-profile=default.profdata
```
結果太長就不列了，如果需要更詳細的使用說明，請參照clang官方文件
不過比較有用的功能是輸出成lcov能吃的格式
``` bash
$ llvm-cov export ./test -instr-profile=default.profdata --format=lcov > test.info
```
就能使用上一節介紹的
``` bash
$ genhtml test.info -o report
```
生成report

