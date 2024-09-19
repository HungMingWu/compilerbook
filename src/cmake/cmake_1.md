對於`Compilation Database`，就是在編譯的時候，紀錄編譯的選項以及文件名稱和等等其他資料的資料庫，存成json格式，提供給外部工具，如vscode等使用
跟cmake整合的方式也很簡單，如下所示
``` bash
$ cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=1 -S . -B build/
```
這下就會在`build/`下生成一個`compile_commands.json`了

