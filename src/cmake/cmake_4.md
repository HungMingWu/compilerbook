Modern CMake是以Target為導向，以Target為主的開發模式
Target是以下幾種幾種的抽象概念
- Executable target (透過 add_executable)
- Library target (透過add_library)
- Custom target (透過add_custom_target)
以下是一個簡單的Target
``` bash
add_executable(tutorial tutorial.cxx)
```
之後我們就能在Target
