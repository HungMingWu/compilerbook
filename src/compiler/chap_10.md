這個Extension是非C語言標準
雖然是Microsoft C Extensions，不過gcc/clang亦有實作
在C語言中，已經可以使用`Anonymous Structs / Unions`了
``` c
#include <stdio.h>
struct Point {
    int id;
    struct {
        int x;
        int y;
    };
};
int main()
{
        struct Point p;
        printf("%d %d\n", p.x, p.y);
}
```
可是如果我定義了一個新structures，希望共用內部的(x, y) structure，現階段只能這樣做
``` c
struct Point2 {
    struct {
        int x;
        int y;
    };
};
```
將原先的內容複製貼上一次
如果我們寫成這樣
``` c
struct Pair {
    int x;
    int y;
};
struct Point {
    int id;
    struct Pair pair;
};
struct Point2 {
    struct Pair pair;
};
```
存取x和y的方式就變得比較麻煩
``` c
printf("%d %d\n", p.pair.x, p.pair.y);
```
這就是這個extension所改進的方向
重寫我們的程式碼
``` c
#include <stdio.h>
struct Pair {
    int x;
    int y;
};
struct Point {
    int id;
    struct Pair;
};
struct Point2 {
    struct Pair;
};
int main()
{
        struct Point p;
        printf("%d %d\n", p.x, p.y);
}
```
gcc/clang編譯的時候帶上`-fms-extensions`就可以了

