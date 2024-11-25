在編譯Shared Object(Library)的時候，我們希望有些Symbol不希望被外界得知
如以下例子
``` c
int add2(int a, int b)
{
        return a + b;
}

int add1(int v)
{
        return add2(v, 1);
}
```
如果沒有特別要求編譯的話
``` bash
$ gcc add.c -fPIC -shared -o add.so
```
add1和add2都是對外界可見的function
我只想要輸出add1，隱藏add2的存在，或是其他類似的操作，該怎麼做
#### Append attribute
我們將add1的可見性強制改成`default`
``` c
[[gnu::visibility("default")]]
int add1(int v)
{
        return add2(v, 1);
}
```
gcc/clang的visibility只有兩種選項
- default：也就是外部看得到symbol
- hidden：顧名思義，就是看不到了
#### CLI to control visibility
以上面的Case來說，我們強制讓`add1`公開，然後將其他的function通通隱藏起來
``` bash
$ gcc add.c -fPIC -fvisibility=hidden -shared -o add.so
```
這樣就可以了，clang的使用方式就跟gcc一樣

#### About MSVC
Windows的DLL預設就是將所有symbol隱藏起來，你要公開什麼Symbol，需要特別明確地寫出來
一樣有兩種方式
比較麻煩的[Exporting from a DLL Using DEF Files | Microsoft Learn](https://learn.microsoft.com/en-us/cpp/build/exporting-from-a-dll-using-def-files?view=msvc-170)
另外一種是透過`__declspec(dllexport)`，以上的範例就變成
``` c
__declspec(dllexport)
int add1(int v)
{
        return add2(v, 1);
}
```
