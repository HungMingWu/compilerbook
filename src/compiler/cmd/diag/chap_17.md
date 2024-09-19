使用未初始化的變數時跳出warning
``` c
int main() {
        int v;
        return v;
}
```
這跟介紹的`-ftrivial-auto-var-init`是不一樣的概念
- `-ftrivial-auto-var-init`是編譯器根據規則幫你初始化
- `-Wuninitialized`是編譯器告訴你未被初始化
依照Project的需求來決定

``` bash
$ gcc test.c -c -Wuninitialized
test.c: In function ‘main’:
test.c:3:16: warning: ‘v’ is used uninitialized [-Wuninitialized]
    3 |         return v;
      |                ^
test.c:2:13: note: ‘v’ was declared here
    2 |         int v;
      |             ^
```

