ccache是用來加快編譯速度的，原理就是不重複編譯已經編譯過的檔案
如果是一般的project file，這樣直接套用就可以了
``` bash
$ ./configure CC='ccache gcc'
```
#### Integrate to cmake
``` bash
$ cmake -S ... -B ... -DCMAKE_CXX_COMPILER_LAUNCHER=ccache
```
