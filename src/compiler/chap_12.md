這邊指的range跟C++20 ranges無關，而是不屬於標準中的extension

在兩處可以發現它們的身影
#### switch
``` c
void test(char c)
{
    switch (c)
    {
        case 'A'...'Z':
            break;
        default:
            break;
    }
}
```
#### Designated Initializers
``` c
int widths[] = { [0 ... 9] = 1, [10 ... 99] = 2, [100] = 3 };
```
連標準都不一定完全支持了，非標準的部分別期望MSVC會支持
