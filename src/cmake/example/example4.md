現在我們想要將MathLibrary獨立出來，讓其他CMake Project用`find_package`使用，該
怎麼做
最簡單的方法就是新增`install`指令
``` bash
add_library(MathFunctions)
target_sources(MathFunctions
  PUBLIC
    FILE_SET export_headers
    TYPE HEADERS
    BASE_DIRS include/
    FILES include/MathFunctions.h
  PRIVATE
    src/MathFunctions.cxx
    src/mysqrt.h
    src/mysqrt.cxx
)

target_link_libraries(MathFunctions
  PRIVATE
    m
)

install(TARGETS MathFunctions
  ARCHIVE
  FILE_SET export_headers
)

```
這邊我們指定了
- ARCHIVE： 也就是static library
- FILE_SET：這裡就是我們對外公開的`export_headers`
不過如果我們想要用`find_package`的話，還需要額外的動作
在`MathFunctions`底下的CMakeLists.txt新增
``` bash
install(TARGETS MathFunctions
  EXPORT MathFunctionsTargets
  ARCHIVE
  FILE_SET HEADERS
)

install(EXPORT MathFunctionsTargets
  FILE MathFunctionsTargets.cmake
  DESTINATION lib/cmake/MathFunctions
)
```
不過將
``` bash
add_subdirectory(MathFunctions)
```
改成
``` bash
find_package(MathFunctions)
```
會跳出Warning
``` bash
CMake Warning at CMakeLists.txt:12 (find_package):
  By not providing "FindMathFunctions.cmake" in CMAKE_MODULE_PATH this
  project has asked CMake to find a package configuration file provided by
  "MathFunctions", but CMake did not find one.

  Could not find a package configuration file provided by "MathFunctions"
  with any of the following names:

    MathFunctionsConfig.cmake
    mathfunctions-config.cmake

  Add the installation prefix of "MathFunctions" to CMAKE_PREFIX_PATH or set
  "MathFunctions_DIR" to a directory containing one of the above files.  If
  "MathFunctions" provides a separate development package or SDK, be sure it
  has been installed.
```
因為我們沒有生成`MathFunctionsConfig.cmake`
以下是生成步驟：
#### Step1:
首先要有個Template fil;e `Config.cmake.in`，內容如下
``` bash
@PACKAGE_INIT@

include ( "${CMAKE_CURRENT_LIST_DIR}/MathFunctionsTargets.cmake" )
```
#### Step2:
在原先的CMakeLists.txt中添加這些
``` bash
install(EXPORT MathFunctionsTargets
  FILE MathFunctionsTargets.cmake
  DESTINATION lib/cmake/MathFunctions
)

include(CMakePackageConfigHelpers)
# generate the config file that includes the exports
configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/Config.cmake.in
  "${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfig.cmake"
  INSTALL_DESTINATION "lib/cmake/MathFunctions"
  NO_SET_AND_CHECK_MACRO
  NO_CHECK_REQUIRED_COMPONENTS_MACRO
  )

install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfig.cmake
  DESTINATION lib/cmake/MathFunctions
)
```
之後就能正常使用了，不過ˋ真是冗長
