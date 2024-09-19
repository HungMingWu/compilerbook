雖然gdb之前就有支持script了，不過pytohn提供的功能更齊全，所以用python當script語言更方便

gdb裡面就能直接跟python作互動了
``` bash
$ gdb --quiet
(gdb) python str = "world"
(gdb) python print(f"hello {str}")
hello world
```
#### Create User defined command
以下是一個什麼事都沒做，用來示範的範例
```  python
import gdb

class Nop(gdb.Command):
    """Do nothing"""
    def __init__(self):
        super(Nop, self).__init__("nop", gdb.COMMAND_USER)

    def invoke(self, arg, from_tty):
        print("nop")

Nop()
```
將它命名成`Nop.py`
接著在gdb使用
``` bash
$ gdb --quiet
For help, type "help".
Type "apropos word" to search for commands related to "word".
(gdb) source Nop.py
(gdb) help user-defined
User-defined commands.
The commands in this class are those defined by the user.
Use the "define" command to define a command.

List of commands:

nop -- This command is not documented.

Type "help" followed by command name for full documentation.
Type "apropos word" to search for commands related to "word".
Type "apropos -v word" for full documentation of commands related to "word".
Command name abbreviations are allowed if unambiguous.
(gdb) source Nop.py
(gdb) help user-defined
User-defined commands.
The commands in this class are those defined by the user.
Use the "define" command to define a command.

List of commands:

nop -- Do nothing

Type "help" followed by command name for full documentation.
Type "apropos word" to search for commands related to "word".
Type "apropos -v word" for full documentation of commands related to "word".
Command name abbreviations are allowed if unambiguous.
(gdb) nop
nop
```
首先先用
``` bash
source Nop.py
```
將我們的Command讀取近來
接著
``` 
help user-defined
```
看到我們新增的command已經列在上面了，最後就執行命令了
``` bash
(gdb) nop
nop
```
至於要建立更完整的功能，清參照Python和gdb使用文件

