假設你不用FILE_SET，而用過往的`target_include_directories`
CMakeLists.txt大概長這樣
``` bash
add_library(MathFunctions)
target_sources(MathFunctions
  PRIVATE
    src/MathFunctions.cxx
    src/mysqrt.cxx
)

target_include_directories(MathFunctions PUBLIC include/)

install(TARGETS MathFunctions
  EXPORT MathFunctionsTargets
  ARCHIVE
)

install(FILES include/MathFunctions.h DESTINATION include)

install(EXPORT MathFunctionsTargets
  FILE MathFunctionsTargets.cmake
  DESTINATION lib/cmake/MathFunctions
)
```
這樣子會出錯，錯誤訊息大概像這樣
``` bash
Target "MathFunctions" INTERFACE_INCLUDE_DIRECTORIES property contains
path:

  "/home/hm/MathFunctions"

which is prefixed in the source directory.
```
此時你需要修改`target_include_directories`的內容
``` bash
target_include_directories(MathFunctions
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)
```
比起FILE_SET的作法，有兩點不同
- 你必須額外install header files
- 以及target_include_directories的內容要改
