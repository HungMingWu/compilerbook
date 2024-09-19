假設我們上一步的`MathFunctions`不安裝成System Library，
也不想當作該Project的sub_directory
想要直接用`FetchContent`來用，該怎麼做
修改原先的CMakeLists.txt
``` bash
include(FetchContent)
FetchContent_Declare(MathFunctions
  SOURCE_DIR ${CMAKE_CURRENT_LIST_DIR}/MathFunctions)
FetchContent_MakeAvailable(MathFunctions)
```
之後`MathFunctions`可以獨立成一個git repo，而不需要跟tutorial綁在一起了

