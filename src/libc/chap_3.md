[Cosmopolitan Libc](https://justine.lol/cosmopolitan/index.html)是個用途十分特別的C Runtime，可以讓你達成**Compile once, run everywhere**這種夢想(不過也僅限於跟OS Specific無關的部分，你總不可能在Windows嚇跑epoll)
#### Installation
還真沒什麼好說的
``` bash
$ mkdir -p cosmocc
$ cd cosmocc
$ wget https://cosmo.zip/pub/cosmocc/cosmocc.zip
$ unzip cosmocc.zip
```
#### Hello World
照慣例
``` c
#include <stdio.h>

int main() {
  printf("hello world\n");
}
```
然後在編譯的時候動手腳
``` bash
$ ./bin/cosmocc -o hello hello.c
```
可以看到現在產生的hello
``` bash
$ file hello
hello: DOS/MBR boot sector; partition 1 : ID=0x7f, active, start-CHS (0x0,0,1), end-CHS (0x3ff,255,63), startsector 0, 4294967295 sectors
$ ./hello
hello world
```
將hello改成windows能吃的`hello.exe`一樣能夠執行
#### Complex case (llama2.c)
總不能什麼都用hello world當代表，來編譯一下AI有關的Project
``` bash
$ git clone https://github.com/karpathy/llama2.c
$ cd llama2.c
$ make CC=~/cosmocc/bin/cosmocc
```
