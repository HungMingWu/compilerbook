接著我們打算將上面的程式碼改成C++20 Module的版本
先把程式碼貼出來
`tutorial.cxx`
``` cpp
#include <cmath>
#include <iostream>
#include <string>
#include "config.h"
import Math;

int main(int argc, char* argv[])
{
        std::cout << "Project Version: " << PROJECT_VERSION_MAJOR << "." << PROJECT_VERSION_MINOR << std::endl;
        std::cout << "Author: " << AUTHOR_NAME << std::endl;

        if (argc < 2) {
                std::cout << "Usage: " << argv[0] << " number" << std::endl;
                return 1;
        }

        // convert input to double
        const double inputValue = atof(argv[1]);

        // calculate square root
        const double outputValue = mathfunctions::sqrt(inputValue);
        std::cout << "The square root of " << inputValue << " is " << outputValue
                << std::endl;
        return 0;
}
`
```
接著是Library的部分
`MathFunctions.cppm`
```  cpp
module;

export module Math;

export namespace mathfunctions
{
        double sqrt(double);
}
```
`src/MathFunctions.cxx`
``` cpp
module Math;
import :detail;

namespace mathfunctions {
        double sqrt(double x) {
                return detail::sqrt(x);
        }
}
```
`src/mysqrt.cppm`
``` cpp
module;
#include <math.h>

module Math:detail;

namespace mathfunctions::detail {
        double sqrt(double x) {
                return ::sqrt(x);
        }
}
```
關於Module的語法跟使用，請參考cppreference，這邊主要講的是CMake該怎麼寫
- Consumer端的CMakeLists.txt照舊
- 需要修改的是`MathFunctions`這邊的CMakeLists.txt
	- 關於安裝的部分，跟之前是一樣的
	- 而不同的地方在於`target_sources`
	- 啟用Module至少是C++20或更新的版本

修改如下
``` bash
target_sources(MathFunctions
  PUBLIC
    FILE_SET primary_interface
    TYPE CXX_MODULES
    FILES
      MathFunctions.cppm
  PRIVATE
    FILE_SET implementaion_units
    TYPE CXX_MODULES
    FILES
      src/mysqrt.cppm
  PRIVATE
    src/MathFunctions.cxx
)

target_compile_features(MathFunctions
  PUBLIC
    cxx_std_20
)
```
這邊的重點
- 我們有兩個CXX_MODULE FILE_SET，只有一個是公開的
- 另外一個是partiition unit，不需要公開給Consumer
- 所有程式碼中有`export module ....`的檔案，都必需是`CXX_MODULE`的存在
