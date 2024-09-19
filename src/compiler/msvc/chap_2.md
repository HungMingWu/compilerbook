雖然MSVC沒有UndefinedBehaviorSanitizer，不過也有自己的一套檢查機制
在編譯的時候加上`/RTCxxx`即可，`/RTCcsu`就將所有的檢查打開，詳細說明請洽官方文件
