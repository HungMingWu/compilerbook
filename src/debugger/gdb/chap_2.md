這是測試程式，沒有什麼意義，只是用來測試
``` c
#include <inttypes.h>
#include <stddef.h>
#include <stdlib.h>

typedef struct Uuid {
  uint8_t bytes[16];
} Uuid;

typedef struct UuidListNode {
  struct UuidListNode *next;
  Uuid uuid;
} UuidListNode;

static UuidListNode root = {
        .next = NULL,
        .uuid.bytes = { 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07,
                        0x08, 0x09, 0x0a, 0x0b, 0x0c, 0x0d, 0x0e
        }
};

int main()
{
        return 0;
}
```
當不開pretty printer的時候
``` bash
(gdb) print root
$1 = {next = 0x0, uuid = {bytes = "\001\002\003\004\005\006\a\b\t\n\v\f\r\016\000"}}
```
開啟之後
``` bash
(gdb) set print pretty
(gdb) print root
$2 = {
  next = 0x0,
  uuid = {
    bytes = "\001\002\003\004\005\006\a\b\t\n\v\f\r\016\000"
  }
}
```
覺得有比較好，不過能夠依照我們的格式來輸出更好的訊息

#### Custom Pretty Printer
如果知道gdb如何使用Python，大概就結束了
同樣的，API細節不是這邊的重點，這邊只是示範
``` python
from gdb.printing import PrettyPrinter, register_pretty_printer
import gdb
import uuid

class UuidPrettyPrinter(object):
    """Print 'struct Uuid' as 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'"""
    def __init__(self, val):
        self.val = val
    def to_string(self):
        return "TODO: Implement"

class CustomPrettyPrinterLocator(PrettyPrinter):
    """Given a gdb.Value, search for a custom pretty printer"""
    def __init__(self):
        super(CustomPrettyPrinterLocator, self).__init__(
            "my_pretty_printers", []
        )
    def __call__(self, val):
        """Return the custom formatter if the type can be handled"""
        typename = gdb.types.get_basic_type(val.type).tag
        if typename is None:
            typename = val.type.name

        if typename == "Uuid":
            return UuidPrettyPrinter(val)

register_pretty_printer(None, CustomPrettyPrinterLocator(), replace=True)
```
接著一樣使用
``` bash
(gdb) source custom_gdb_extensions.py
(gdb) set print pretty
(gdb) print root
$1 = {
  next = 0x0,
  uuid = TODO: Implement
}
```
