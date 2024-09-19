從一個簡單範例開始
``` cpp
#include <cmath>
#include <iostream>
#include <string>

int main(int argc, char* argv[])
{
        if (argc < 2) {
                std::cout << "Usage: " << argv[0] << " number" << std::endl;
                return 1;
        }

        // convert input to double
        const double inputValue = atof(argv[1]);

        // calculate square root
        const double outputValue = sqrt(inputValue);
        std::cout << "The square root of " << inputValue << " is " << outputValue
                << std::endl;
        return 0;
}

```

而這是最簡單的CMakeLists.txt內容
``` bash
cmake_minimum_required(VERSION 3.28)
add_executable(tutorial)
target_sources(tutorial
  PRIVATE
    tutorial.cxx
)
```
