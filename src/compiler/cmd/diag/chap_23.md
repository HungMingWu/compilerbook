#### `-Wdouble-promotion
``` c
double func()
{
        float a = 3.0f, b = 5.0f;
        return 3.14 * a / b;
}
```
用來檢測implicit conversion from float to double的情況
``` bash
$ gcc test.c -c -Wdouble-promotion
test.c: In function ‘func’:
test.c:4:21: warning: implicit conversion from ‘float’ to ‘double’ to match other operand of binary expression [-Wdouble-promotion]
    4 |         return 3.14 * a / b;
      |                     ^
test.c:4:25: warning: implicit conversion from ‘float’ to ‘double’ to match other operand of binary expression [-Wdouble-promotion]
    4 |         return 3.14 * a / b;
      |
```
#### `-Wfloat-equal
在float或是double使用錯誤的比較方式
``` c
int func(float *value)
{
        return *value == 0.0f;
}
```
會出現這樣的warning
``` bash
$ gcc test.c -c -Wfloat-equal
test.c: In function ‘func’:
test.c:3:23: warning: comparing floating-point with ‘==’ or ‘!=’ is unsafe [-Wfloat-equal]
    3 |         return *value == 0.0f;
      |                       ^~
```
