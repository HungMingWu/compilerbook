有些時候，總有Remote Debug的需求，有可能是客戶端，也有可能是嵌入式系統需要
在這個時候，就要有個server端與gdb互動，而這個server的名稱就叫做gdbserver

#### How to use
``` bash
$ gdbserver :20000 test
```
或是
``` bash
$ gdbserver :20000 --attach <pid>
```
然後在gdb這端
``` bash
$ gdb test
(gdb) target remote 192.168.93.128:20000
```
接著就跟一般正常使用gdb差不多了
