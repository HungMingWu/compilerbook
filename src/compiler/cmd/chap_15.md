這個跟其他選項的狀態不太一樣，這功能
- 目前只有clang/MSVC有
- gcc尚未實作

一樣給個範例

``` cpp
#include <map>
int main()
{
        std::map<int, int> v;
        v.emplace(3, 4);
        return 0;
}
```
透過clang編譯
``` bash
$ clang test.cpp -ftime-trace 
```
生成的`test.json`可以透過 `chrome:://tracing`或是[jspeedscope:](https://github.com/jlfwong/speedscope)來分析
不過當你的Project更大時，這方式就不管用了，此時就是`ClangBuildAnalyzer`登場的時候

### ClangBuildAnalyzer

[aras-p/ClangBuildAnalyzer: Clang build analysis tool using -ftime-trace (github.com)](https://github.com/aras-p/ClangBuildAnalyzer)

以現有的 Open Source Project  [PEGTL](https://github.com/taocpp/PEGTL) 舉例，如何將編譯時間分析融入Workflow中
首先，我們先拿到PEGTL的Source Code
``` bash
$ git clone https://github.com/taocpp/PEGTL
$ cd PEGTL
```
接著，在執行CMake前插入`-ftime-trace`到`CXXFLAGS`中
``` cpp
$ CXX=clang CXXFLAGS="-ftime-trace -stdlib=libc++" LDFLAGS="-lc++" cmake -S . -B build
```
之後就是ClangBuilderAnalyzer登場的時候
``` bash
$ ClangBuildAnalyzer --start build
```
此時會在build目錄下產生`ClangBuildAnalyzerSession.txt`
接著回到正常編譯過程
``` bash
$ ClangBuildAnalyzer --stop build/ combined.bin
```
他會讀取build下的所有json檔，處理之後產生一個binary file
最後就是用
``` bash
$ ClangBuildAnalyzer --analyze combined.bin
```
分析整個Project，結果應該像這樣
``` bash
Analyzing build trace from 'combined.bin'...
**** Time summary:
Compilation (334 times):
  Parsing (frontend):        10599.2 s
  Codegen & opts (backend):   1057.1 s

**** Files that took longest to parse (compiler frontend):
179926 ms: build//src/example/pegtl/CMakeFiles/pegtl-example-uri_trace.dir/uri_trace.cpp.o
172013 ms: build//src/test/pegtl/CMakeFiles/pegtl-test-uint64_general.dir/uint64_general.cpp.o
171481 ms: build//src/test/pegtl/CMakeFiles/pegtl-test-rule_try_catch_return_false.dir/rule_try_catch_return_false.cpp.o
161991 ms: build//src/test/pegtl/CMakeFiles/pegtl-test-uint32_general.dir/uint32_general.cpp.o
```
#### About MSVC
這功能不能直接使用，而是需要額外的SDK
關於詳細內容，請參考[Introducing vcperf /timetrace for C++ build time analysis](https://devblogs.microsoft.com/cppblog/introducing-vcperf-timetrace-for-cpp-build-time-analysis/)
使用方式和ClangBuildAnalyzer大同小異

