這是gdb有，而lldb沒有的功能，在使用的時候加`-tui`，能夠看到簡單的UI
``` bash
$ gdb hello -tui
```
![Text UI](https://beej.us/guide/bggdb/hellotui.png)
或者在使用過程當中，使用Ctrl + X + A來回切換TUI或一般模式，不過這UI也只到堪用，在複雜的情況之下無能為力
