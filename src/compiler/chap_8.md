PGO的全名是**Profile-Guided Optimization**，拿編譯過的executable file運行，拿到真實運行的Profile為基準，引導編譯器做最佳化的方式
以下是測試範例，以及三大編譯器如何使用PGO的方式
``` cpp
#include <array>
#include <chrono>
#include <span>
void bubble_sort (std::span<int> a) {
        int s = 1;
        while (s) {
                s = 0;
                for (size_t i = 1; i < a.size(); i++) {
                        if (a[i] < a[i - 1]) {
                                std::swap(a[i], a[i - 1]);
                                s = 1;
                        }
                }
        }
}
void sort_array() {
        std::array<int, 30000> data;
        int val = data.size();
        for (auto v : data) v = val--;
        bubble_sort(data);
}

int main()
{
        sort_array();
        return 0;
}
```

#### gcc
首先要先編譯一個會產生profile的executable file
``` bash
$ g++ -std=c++20 -march=native -fprofile-generate -o main main.cpp
```
執行它
``` bash
$ ./main
```
此時會產生一個`main.gcda`檔案，接著再將這個檔案餵給gcc就可以了
``` bash
$ g++ -std=c++20 -march=native -fprofile-use -o main main.cpp
```
#### clang
clang的方式和gcc大同小異，不過有些地方不同
``` bash
$ clang++ -std=c++20 -march=native -fprofile-instr-generate -o main main.cpp
```
執行的時候要帶環境變數`LLVM_PROFILE_FILE`進去
``` bash
$ LLVM_PROFILE_FILE="default_%p.profraw" ./main
```
之後會產生一或多個附檔名為profraw的檔案門，接著用`llvm-profdata`合併它們`
``` bash
$ llvm-profdata merge default_*.profraw -o default.profdata
```
同樣的將最終的profdata餵給clang
``` bash
$ clang++ -std=c++20 -march=native -fprofile-instr-use=default.profdata -o main main.cpp
```
#### MSVC
MSVC也是差不多，依樣差在細節
``` powershell
$ cl /O2 /GL /std:c++latest main.cpp /link /LTCG:PGINSTRUMENT
```
這裡的`/GL /link /LTCG:PGINSTRUMENT`是必要的，此時會產生`main.pgd`檔案
同樣可以執行
``` powershell
$ main
$ main
```
美執行一次會產生一個pgc檔出來，可以做多次profile
接著
``` powershell
$ cl /O2 /GL /std:c++latest main.cpp /link /LTCG:PGOPTIMIZE /PGD:main.pgd
```
編譯器將會根據pgd和多次profile的pgc檔作分析而產生最終executable file

### Advance PGO
gcc和clang各自有基於Hardware的Profiling機制
- gcc的叫AutoFDO
- clang的叫HWPGO
由於基於硬體，通用性就沒上面的PGO那麼廣泛了，不過可以根據實際硬體做更為激進的最佳化
列出參考資料
[AutoFDO tutorial](https://gcc.gnu.org/wiki/AutoFDO/Tutorial)
[Enabling HW-based PGO](https://llvm.org/devmtg/2024-04/slides/TechnicalTalks/Xiao-EnablingHW-BasedPGO.pdf)

