在C++17之後，C23終於也跟進了
當我們可能遇到一些用不到的變數或函數時，例如
``` c
int func(int v)
{
	return 0;
}
```
編譯時可能會出現這樣的訊息
``` bash
test.c:1:14: warning: unused parameter 'v' [-Wunused-parameter]
    1 | int func(int v)
      |    
```
解決方法有好幾種
#### pragma unused
``` c
int func(int v)
{
#pragma unused(v)
        return 0;
}
```
當然，MSVC不支援，這非標準
#### Omit parameter name
在C++可以這樣寫完全沒問題
``` c
int func(int)
{
        return 0;
}
```
不過在之前的C Standard是非標準的，gcc不會吐出任何警告，而clang會
``` bash
test.c:1:13: warning: omitting the parameter name in a function definition is a C23 extension [-Wc23-extensions]
    1 | int func(int)
      | 
```
而MSVC直接吐出
``` powershell
C2055 error - expected formal parameter list, not a type list
```
不過要等到MSVC支持C23還有得等
#### Marcro's solution
```  c
#define UNUSED(x) (void)(x)
int func(int v)
{
        UNUSED(v);
        return 0;
}
```
這方法除了醜之外，沒有其他問題
#### attribute's solution
如同其他attribute的用法，簡單明瞭

``` c
int func([[maybe_unused]] int v)
{
        return 0;
}
```
不過問題還是在於MSVC何時支持C23

