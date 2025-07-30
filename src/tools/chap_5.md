有時候我們為了增加速度或者減少記憶體使用量，需要分析struct的layout，所以我們需要工具

`pahole`在unix系統就是很方便的工具之一
以下是範例程式，程式碼本身沒什麼特別的
``` c
struct sample {
        char a[2];
        long l;
        int i;
        void *p;
        short s;
};

void test(struct sample*)
{
}
```
不過編譯的時候記得帶debug symbol
``` bash
$ gcc -c test.c -g -o test.o
```
然後就能使用pahole來觀看struct layout了
``` bash
$ pahole test.o
struct sample {
        char                       a[2];                 /*     0     2 */

        /* XXX 6 bytes hole, try to pack */

        long int                   l;                    /*     8     8 */
        int                        i;                    /*    16     4 */

        /* XXX 4 bytes hole, try to pack */

        void *                     p;                    /*    24     8 */
        short int                  s;                    /*    32     2 */

        /* size: 40, cachelines: 1, members: 5 */
        /* sum members: 24, holes: 2, sum holes: 10 */
        /* padding: 6 */
        /* last cacheline: 40 bytes */
};
```
更複雜的使用參照Reference Link
至於Visual Studio，Command Line沒有提供類似的功能，不過IDE有，需要的話使用IDE吧

#### Reference
- [Poke-a-hole and friends](https://lwn.net/Articles/335942/)
- [Size, Alignment, and Memory Layout Insights for C++ Classes, Structs, and Unions](https://devblogs.microsoft.com/visualstudio/size-alignment-and-memory-layout-insights-for-c-classes-structs-and-unions/)
