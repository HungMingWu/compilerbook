[cpplint - static code checker for C++](https://github.com/cpplint/cpplint)
cpplint適合Project不太複雜的情況下使用

Example:
``` cpp
using namespace std;
class Obj {
                void test() {
                        cout << "test\n";
                }
};
```

最簡單的使用方式
``` bash
$ cpplint obj.cpp
obj.cpp:0:  No copyright message found.  You should have a line: "Copyright [year] <Copyright Owner>"  [legal/copyright] [5]
obj.cpp:1:  Do not use namespace using-directives.  Use using-declarations instead.  [build/namespaces] [5]
obj.cpp:3:  Tab found; better to use spaces  [whitespace/tab] [1]
obj.cpp:4:  Tab found; better to use spaces  [whitespace/tab] [1]
obj.cpp:5:  Tab found; better to use spaces  [whitespace/tab] [1]
Done processing obj.cpp
Total errors found: 5
```
如果有些規則不符合需求的話，用‵`filter`過濾掉
就像這樣
``` bash
cpplint --filter -legal/copyright,-build/namespace obj.cpp
```

####  Complexed example
假設我有一個Project，像上面一個一個檢查file實在太麻煩了
```  bash
$ touch .cpplint_config
```
然後修改`.cpplint_config`裡面的內容
``` bash
filter=-legal/copyright,-build/namespace
```
寫一個Python wrapper
``` python
import os
import subprocess

config_file = '.cpplint_config'

options = []
if os.path.exists(config_file):
    with open(config_file, 'r') as f:
        for line in f:
            option = line.strip().split('#')[0]
            if option:
                options.append('--' + option)

cpp_files = []
for root, dirs, files in os.walk('.'):
    for file in files:
        if file.endswith(('.cpp', '.hpp', '.cc', '.h')):
            cpp_files.append(os.path.join(root, file))

for cpp_file in cpp_files:
    cmd = ['cpplint'] + options + [cpp_file]
    subprocess.run(cmd)

```
接著就用Python來運行了
``` bash
$ python3 cpplint_wrapper.py
```
如果要整合到CMake，方法之一就是新增一個Target，連接Target之間的dependency，這樣在build之前就會自動做lint了
``` bash
cmake_minimum_required(VERSION 3.28)
project(demo)
add_library(${CMAKE_PROJECT_NAME} STATIC)
target_sources(${CMAKE_PROJECT_NAME} PRIVATE src/obj.cpp)
add_custom_target(cpplint
  COMMAND cpplint
          --filter -legal/copyright,-build/namespace
          --recursive ${PROJECT_SOURCE_DIR}/src
  COMMENT "Running cpplint"
)
add_dependencies(${CMAKE_PROJECT_NAME} cpplint)
```

