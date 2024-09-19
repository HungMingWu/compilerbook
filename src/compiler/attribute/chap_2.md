`aligned`最常用的地方是變數宣告，修改`naturally aligned`的規則
#### What is naturally aligned?
> "naturally aligned" means that any item is aligned to at least a multiple of its own size. For example, a 4-byte object is aligned to an address that's a multiple of 4, an 8-byte object is aligned to an address that's a multiple of 8, etc.
所以
``` c
int x;
```
x會被aligned到4的倍數的位址，一般來說這樣最有效率．不過凡事總有例外
``` c
int x [[gnu::aligned(16)]];
```
這樣子就能要求Compilier放到16的倍數的位址了
#### About struct
另外`align`還能修飾struct等數據類型
``` c
#include <stdio.h>
struct origin_struct {
        short f[3];
};
struct [[gnu::aligned(8)]]
aligned_struct {
        short f[3];
};
int main()
{
        printf("%lu %lu\n", sizeof(struct origin_struct), sizeof(struct aligned_struct));
}
```
如果不對struct作修飾的話，原先的`origin_struct`只有6bytes大，修飾過的話則有8bytes (2bytes padding)
這樣做的好處是，假設要在memory中連續存取，8bytes 會比6bytes效率高
#### About MSVC
MSVC也有` __declspec(align(#))`這樣的方式來控制alignment，不過這些都是古老的方式了
``` c
#include <stdio.h>
struct origin_struct {
        short f[3];
};
struct __declspec(align(8))
aligned_struct {
        short f[3];
};
int main()
{
        printf("%lu %lu\n", sizeof(struct origin_struct), sizeof(struct aligned_struct));
}
```
#### From now on
C++11之後，新增了`alignas`關鍵字
所以上面的範例可以這麼改
``` cp
struct alignas(8) aligned_struct {
		short f[3];
};
```
C23之後，雖然也支持alignas，不過不支持修飾struct
只能控制其members alignment
所以以上的範例可以改成
``` c
struct aligned_struct {
		alignas(8) short f[3];
};
```
