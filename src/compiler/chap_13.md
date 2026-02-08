在C/C++中，inline這個關鍵字變得越來越無關緊要了，就算不加上inline，編譯器也有可能在呼叫的部分自行展開，例如
``` c
int add(int a, int b) { return a + b; }
int main()
{
        int a = 2, b = 3;
        return add(a, b);
}
```
生成的程式碼可能就不會有`call add`這樣的assembly code了
那我們該怎麼知道編譯器是否幫我們做了這件事

#### gcc
在編譯的時候加`-fopt-info-inline`，除此之外還要加上是當的最佳化等級才有效過
``` bash
$ gcc -O2 add.c -o add -std=c11 -fopt-info-inline
add.c:5:9: optimized:  Inlining add/0 into main/1.
```
除了`-fopt-info-inline`之外還有其他選項，有必要的話查看reference的連結

#### clang
clang使用了`-Rpass`的方式
``` bash
$ clang -O1 add.c -o add -std=c11 -Rpass=inline
add.c:5:9: remark: 'add' inlined into 'main' with (cost=-40, threshold=337) at callsite main:3:9; [-Rpass=inline]
    5 |         return add(a, b);

```
#### msvc
沒有可靠的方法可以使用，有些未文件化的方式，不過依賴於某些版本的MSVC，不保證永遠可用

#### Reference
- [Developer Options (Using the GNU Compiler Collection (GCC))](https://gcc.gnu.org/onlinedocs/gcc/Developer-Options.html)
- [Clang Compiler User’s Manual](https://clang.llvm.org/docs/UsersManual.html)

