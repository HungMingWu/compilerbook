由於三大編譯器已經有內建的static analyzer，這是最簡單的整合方式
``` bash
target_compile_options(<target>
PRIVATE
        $<$<CXX_COMPILER_ID:MSVC>:/analyze>
        $<$<CXX_COMPILER_ID:GNU>:-fanalyzer>
        $<$<CXX_COMPILER_ID:Clang>:--analyze>
)
```
#### CppCheck
CppCheck和ClangTidy也不會困難到哪去，只是要額外安裝工具
``` bash
set(CMAKE_CXX_CPPCHECK cppcheck --enable=all)
```
``` bash
set(CMAKE_CXX_CLANG_TIDY clang-tidy -checks=-*,clang-analyzer-*,bugprone-*,cppcoreguidelines-*)
```
這跟上面的`target_compile_options`不同，這是全域設定，必須放在Root CMakeList下，之後在編譯的時候就會進行靜態分析
