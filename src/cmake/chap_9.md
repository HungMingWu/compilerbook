CMake Presets是將配置、建構和測試專案的複雜命令列選項集合，封裝到一個簡單的 **`CMakePresets.json`** 檔案中。
可以
- 簡化命令列的使用
	- 將`cmake -G "..." -B "..." -DVAR=VALUE ...`的命令簡化成`cmake --preset=...`
- 共享使用參數、可以整合進CI/CD中
以下是一個**`CMakePresets.json`** 的範例
``` json
{
  "version": 6,
  "cmakeMinimumRequired": {
    "major": 3,
    "minor": 23,
    "patch": 0
  },
  "configurePresets": [
    {
      "name": "dev-debug",
      "displayName": "Developer Debug Build",
      "description": "Standard debug build using Ninja.",
      "generator": "Ninja",
      "binaryDir": "${sourceDir}/build/debug",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Debug"
      }
    },
    {
      "name": "prod-release",
      "displayName": "Production Release Build",
      "description": "Optimized release build for production.",
      "generator": "Ninja",
      "binaryDir": "${sourceDir}/build/release",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Release"
      }
    }
  ],
  "buildPresets": [
    {
      "name": "dev-debug",
      "configurePreset": "dev-debug"
    },
    {
      "name": "prod-release",
      "configurePreset": "prod-release"
    }
  ]
}
```
之後我們可以透過 `cmake --list-presets`來列出有多少可用的preset
``` bash
$ cmake --list-presets
Available configure presets:

  "dev-debug"    - Developer Debug Build
  "prod-release" - Production Release Build

```
接著我們就可以透過
``` bash
$ cmake -S . -B build --preset=dev-debug
```
來直接套用`dev-debug`當中的設定了
