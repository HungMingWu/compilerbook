#### `-Wconversion`
可以偵測到常見的conversion問題
``` c
double func()
{
        return 3.14;
}
int main()
{
        return func();
}
```
``` bash
$ clang test.c -c -Wconversion
test.c:7:9: warning: implicit conversion turns floating-point number into integer: 'double' to 'int' [-Wfloat-conversion]
    7 |         return func();
      |         ~~~~~~ ^~~~~~
1 warning generated.
```
#### `-Wsign-conversion`
這個範例提供了unsigned和int做運算，可能出現的問題
``` c
int func(unsigned a, int b)
{
        return a * b;
}
```
``` bash
$ clang test.c -c -Wsign-conversion
test.c:3:11: warning: implicit conversion changes signedness: 'unsigned int' to 'int' [-Wsign-conversion]
    3 |         return a * b;
      |         ~~~~~~ ~~^~~
test.c:3:13: warning: implicit conversion changes signedness: 'int' to 'unsigned int' [-Wsign-conversion]
    3 |         return a * b;
      |                  ~ ^
2 warnings generated.
```
