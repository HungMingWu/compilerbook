[Static Analysis Results Interchange Format (SARIF)](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2024/p3358r0.html)立志要成為Static Analysis的標準，先不論能不能成功，而目前三大編譯氣的支援情形
	- gcc目前狀態最完整
	- msvcc和clang還處於實驗階段
先從最完整的gcc開始說起

#### gcc
gcc13之後，就已經能夠輸出成SARIF格式了
``` bash
$ gcc -fanalyzer test.c -fdiagnostics-format=sarif-stderr # 輸出到terminal
$ gcc -fanalyzer test.c -fdiagnostics-format=sarif-file # 輸出到file
```
不過我們要同時輸出人所能看懂的格式和機器能看懂的sarift格式，一直到gcc15才完成
``` bash
$ gcc -fanalyzer test.c -fdiagnostics-add-output=sarif
```
gcc15同時也提供了`sarif-replay`，用來分析sarif file
``` bash
$ sarif-replay signal-warning.sarif
```

#### clang
clang一樣使用`-fdiagnostics-format`當輸出
``` bash
$ clang --analyze test.c -fdiagnostics-format=sarif
```
輸出大概長這個樣子
``` bash
clang: warning: diagnostic formatting in SARIF mode is currently unstable [-Wsarif-format-unstable]

{"$schema":"https://docs.oasis-open.org/sarif/sarif/v2.1.0/cos02/schemas/sarif-schema-2.1.0.json","runs":[{"artifacts":[],"columnKind":"unicodeCodePoints","results":[],"tool":{"driver":{"fullName":"","informationUri":"https://clang.llvm.org/docs/UsersManual.html","language":"en-US","name":"clang","rules":[],"version":"21.0.0git"}}}],"version":"2.1.0"}
```

#### MSVC
MSVC產生SARIF有兩種方式，不過這邊指講述用Command Line的方式
``` powershell
，cl /experimental:log mySarifInfo main.cpp
```
由上面可知還只是實驗性的


