在gcc/clang的編譯的時候，有兩個跟CPU有關的command
`-m`和`-arch`

`-m`是啟用某些CPU功能或指令集
例如：`-mavx`， `-mfma`，`-msse2`分別代表啟用AVX，FMA，SSE2指令集
而這不會改變Target CPU的架構
而`-arch`正是改變CPU架構的指令
例如：
- `-march=core2`：針對Intel Core 2架構
- `-march=armv8-a`：針對ARMv8-A
- `-march=native`：編譯器自動偵測目前架構
我們還可以透過`-m32`和`-m64`來選擇編譯成32位元或是64位元
#### About MSVC
MSVC沒有類似`-m`那種啟用特定功能或指令集的功能，不過有類似`/ARCH`的功能
例如：
- `/ARCH:SSE2`：針對SSE2指令集
- `/ARCH:AVX2`：針對AVX2指令集
相較之下功能就貧乏了一點
