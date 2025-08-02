gcc已經有實作hardened mode
``` bash
$ gcc test.c -fhardened -o test -O1
```
當使用`-fhardened`時
預設會開啟
- `-ftrivial-auto-var-init` 請參照`Initializes trivial automatic variables`這章
- `-fstack-protector-strong`  請參照`stack-protector`這章
- Predefines some macros 請參照`libstdc++`這章
	- _FORTIFY_SOURCE=3
	- _GLIBCXX_ASSERTIONS

也就是說，使用harden mode，就包含了上述所有的選項
