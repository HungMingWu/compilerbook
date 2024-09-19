``` cpp
int main()
{
	int a = 1 + 1;
	return a;
}
```
使用以下方式可以Dump出中間產物的AST  Tree
``` bash
$ clang test.cpp -Xclang -ast-dump -c
TranslationUnitDecl 0x59839dc71298 <<invalid sloc>> <invalid sloc>
|-TypedefDecl 0x59839dc71b48 <<invalid sloc>> <invalid sloc> implicit __int128_t '__int128'
| `-BuiltinType 0x59839dc71860 '__int128'
|-TypedefDecl 0x59839dc71bb8 <<invalid sloc>> <invalid sloc> implicit __uint128_t 'unsigned __int128'
| `-BuiltinType 0x59839dc71880 'unsigned __int128'
|-TypedefDecl 0x59839dc71f40 <<invalid sloc>> <invalid sloc> implicit __NSConstantString '__NSConstantString_tag'
| `-RecordType 0x59839dc71ca0 '__NSConstantString_tag'
|   `-CXXRecord 0x59839dc71c10 '__NSConstantString_tag'
|-TypedefDecl 0x59839dc71fe8 <<invalid sloc>> <invalid sloc> implicit __builtin_ms_va_list 'char *'
| `-PointerType 0x59839dc71fa0 'char *'
|   `-BuiltinType 0x59839dc71340 'char'
|-TypedefDecl 0x59839dcb8ab0 <<invalid sloc>> <invalid sloc> implicit __builtin_va_list '__va_list_tag[1]'
| `-ConstantArrayType 0x59839dcb8a50 '__va_list_tag[1]' 1
|   `-RecordType 0x59839dc720d0 '__va_list_tag'
|     `-CXXRecord 0x59839dc72040 '__va_list_tag'
`-FunctionDecl 0x59839dcb8b68 <test.cpp:1:1, line:5:1> line:1:5 main 'int ()'
  `-CompoundStmt 0x59839dcb8dc8 <line:2:1, line:5:1>
    |-DeclStmt 0x59839dcb8d68 <line:3:2, col:15>
    | `-VarDecl 0x59839dcb8ca0 <col:2, col:14> col:6 used a 'int' cinit
    |   `-BinaryOperator 0x59839dcb8d48 <col:10, col:14> 'int' '+'
    |     |-IntegerLiteral 0x59839dcb8d08 <col:10> 'int' 1
    |     `-IntegerLiteral 0x59839dcb8d28 <col:14> 'int' 1
    `-ReturnStmt 0x59839dcb8db8 <line:4:2, col:9>
      `-ImplicitCastExpr 0x59839dcb8da0 <col:9> 'int' <LValueToRValue>
        `-DeclRefExpr 0x59839dcb8d80 <col:9> 'int' lvalue Var 0x59839dcb8ca0 'a' 'int'
```
也可以將其Dump成json格式
``` cpp
$ clang test.cpp -Xclang -ast-dump=json -c
{
  "id": "0x6246bdd9a268",
  "kind": "TranslationUnitDecl",
  "loc": {},
  "range": {
    "begin": {},
    "end": {}
  },
  "inner": [
    {
      "id": "0x6246bdd9ab18",
      "kind": "TypedefDecl",
      "loc": {},
      "range": {
        "begin": {},
        "end": {}
      },
      "isImplicit": true,
      "name": "__int128_t",
      "type": {
        "qualType": "__int128"
      },
      "inner": [
        {
          "id": "0x6246bdd9a830",
          "kind": "BuiltinType",
          "type": {
            "qualType": "__int128"
          }
        }
      ]
    },
    {
      "id": "0x6246bdd9ab88",
      "kind": "TypedefDecl",
      "loc": {},
      "range": {
        "begin": {},
        "end": {}
      },
      "isImplicit": true,
      "name": "__uint128_t",
      "type": {
        "qualType": "unsigned __int128"
      },
      "inner": [
        {
          "id": "0x6246bdd9a850",
          "kind": "BuiltinType",
          "type": {
            "qualType": "unsigned __int128"
          }
        }
      ]
    },
    {
      "id": "0x6246bdd9af10",
      "kind": "TypedefDecl",
      "loc": {},
      "range": {
        "begin": {},
        "end": {}
      },
      "isImplicit": true,
      "name": "__NSConstantString",
      "type": {
        "qualType": "__NSConstantString_tag"
      },
      "inner": [
        {
          "id": "0x6246bdd9ac70",
          "kind": "RecordType",
          "type": {
            "qualType": "__NSConstantString_tag"
          },
          "decl": {
            "id": "0x6246bdd9abe0",
            "kind": "CXXRecordDecl",
            "name": "__NSConstantString_tag"
          }
        }
      ]
    },
    {
      "id": "0x6246bdd9afb8",
      "kind": "TypedefDecl",
      "loc": {},
      "range": {
        "begin": {},
        "end": {}
      },
      "isImplicit": true,
      "name": "__builtin_ms_va_list",
      "type": {
        "qualType": "char *"
      },
      "inner": [
        {
          "id": "0x6246bdd9af70",
          "kind": "PointerType",
          "type": {
            "qualType": "char *"
          },
          "inner": [
            {
              "id": "0x6246bdd9a310",
              "kind": "BuiltinType",
              "type": {
                "qualType": "char"
              }
            }
          ]
        }
      ]
    },
    {
      "id": "0x6246bdde1bd0",
      "kind": "TypedefDecl",
      "loc": {},
      "range": {
        "begin": {},
        "end": {}
      },
      "isImplicit": true,
      "name": "__builtin_va_list",
      "type": {
        "qualType": "__va_list_tag[1]"
      },
      "inner": [
        {
          "id": "0x6246bdde1b70",
          "kind": "ConstantArrayType",
          "type": {
            "qualType": "__va_list_tag[1]"
          },
          "size": 1,
          "inner": [
            {
              "id": "0x6246bdd9b0a0",
              "kind": "RecordType",
              "type": {
                "qualType": "__va_list_tag"
              },
              "decl": {
                "id": "0x6246bdd9b010",
                "kind": "CXXRecordDecl",
                "name": "__va_list_tag"
              }
            }
          ]
        }
      ]
    },
    {
      "id": "0x6246bdde1c88",
      "kind": "FunctionDecl",
      "loc": {
        "offset": 4,
        "file": "test.cpp",
        "line": 1,
        "col": 5,
        "tokLen": 4
      },
      "range": {
        "begin": {
          "offset": 0,
          "col": 1,
          "tokLen": 3
        },
        "end": {
          "offset": 40,
          "line": 5,
          "col": 1,
          "tokLen": 1
        }
      },
      "name": "main",
      "mangledName": "main",
      "type": {
        "qualType": "int ()"
      },
      "inner": [
        {
          "id": "0x6246bdde1ee8",
          "kind": "CompoundStmt",
          "range": {
            "begin": {
              "offset": 11,
              "line": 2,
              "col": 1,
              "tokLen": 1
            },
            "end": {
              "offset": 40,
              "line": 5,
              "col": 1,
              "tokLen": 1
            }
          },
          "inner": [
            {
              "id": "0x6246bdde1e88",
              "kind": "DeclStmt",
              "range": {
                "begin": {
                  "offset": 14,
                  "line": 3,
                  "col": 2,
                  "tokLen": 3
                },
                "end": {
                  "offset": 27,
                  "col": 15,
                  "tokLen": 1
                }
              },
              "inner": [
                {
                  "id": "0x6246bdde1dc0",
                  "kind": "VarDecl",
                  "loc": {
                    "offset": 18,
                    "col": 6,
                    "tokLen": 1
                  },
                  "range": {
                    "begin": {
                      "offset": 14,
                      "col": 2,
                      "tokLen": 3
                    },
                    "end": {
                      "offset": 26,
                      "col": 14,
                      "tokLen": 1
                    }
                  },
                  "isUsed": true,
                  "name": "a",
                  "type": {
                    "qualType": "int"
                  },
                  "init": "c",
                  "inner": [
                    {
                      "id": "0x6246bdde1e68",
                      "kind": "BinaryOperator",
                      "range": {
                        "begin": {
                          "offset": 22,
                          "col": 10,
                          "tokLen": 1
                        },
                        "end": {
                          "offset": 26,
                          "col": 14,
                          "tokLen": 1
                        }
                      },
                      "type": {
                        "qualType": "int"
                      },
                      "valueCategory": "prvalue",
                      "opcode": "+",
                      "inner": [
                        {
                          "id": "0x6246bdde1e28",
                          "kind": "IntegerLiteral",
                          "range": {
                            "begin": {
                              "offset": 22,
                              "col": 10,
                              "tokLen": 1
                            },
                            "end": {
                              "offset": 22,
                              "col": 10,
                              "tokLen": 1
                            }
                          },
                          "type": {
                            "qualType": "int"
                          },
                          "valueCategory": "prvalue",
                          "value": "1"
                        },
                        {
                          "id": "0x6246bdde1e48",
                          "kind": "IntegerLiteral",
                          "range": {
                            "begin": {
                              "offset": 26,
                              "col": 14,
                              "tokLen": 1
                            },
                            "end": {
                              "offset": 26,
                              "col": 14,
                              "tokLen": 1
                            }
                          },
                          "type": {
                            "qualType": "int"
                          },
                          "valueCategory": "prvalue",
                          "value": "1"
                        }
                      ]
                    }
                  ]
                }
              ]
            },
            {
              "id": "0x6246bdde1ed8",
              "kind": "ReturnStmt",
              "range": {
                "begin": {
                  "offset": 30,
                  "line": 4,
                  "col": 2,
                  "tokLen": 6
                },
                "end": {
                  "offset": 37,
                  "col": 9,
                  "tokLen": 1
                }
              },
              "inner": [
                {
                  "id": "0x6246bdde1ec0",
                  "kind": "ImplicitCastExpr",
                  "range": {
                    "begin": {
                      "offset": 37,
                      "col": 9,
                      "tokLen": 1
                    },
                    "end": {
                      "offset": 37,
                      "col": 9,
                      "tokLen": 1
                    }
                  },
                  "type": {
                    "qualType": "int"
                  },
                  "valueCategory": "prvalue",
                  "castKind": "LValueToRValue",
                  "inner": [
                    {
                      "id": "0x6246bdde1ea0",
                      "kind": "DeclRefExpr",
                      "range": {
                        "begin": {
                          "offset": 37,
                          "col": 9,
                          "tokLen": 1
                        },
                        "end": {
                          "offset": 37,
                          "col": 9,
                          "tokLen": 1
                        }
                      },
                      "type": {
                        "qualType": "int"
                      },
                      "valueCategory": "lvalue",
                      "referencedDecl": {
                        "id": "0x6246bdde1dc0",
                        "kind": "VarDecl",
                        "name": "a",
                        "type": {
                          "qualType": "int"
                        }
                      }
                    }
                  ]
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```
如果例子複雜一點變成這樣
``` cpp
int func()
{
        return 1 + 1;
}
int main()
{
        return func();
}
```
我們可以用ast-filter只看`func`的部分就好
``` bash
$ clang test.cpp -Xclang -ast-dump-filter=func -Xclang -ast-dump
Dumping func:
FunctionDecl 0x5af730dd9208 <test.cpp:1:1, line:4:1> line:1:5 used func 'int ()'
`-CompoundStmt 0x5af730dd9368 <line:2:1, line:4:1>
  `-ReturnStmt 0x5af730dd9358 <line:3:2, col:13>
    `-BinaryOperator 0x5af730dd9338 <col:9, col:13> 'int' '+'
      |-IntegerLiteral 0x5af730dd92f8 <col:9> 'int' 1
      `-IntegerLiteral 0x5af730dd9318 <col:13> 'int' 1
```
注意這邊`-Xclang`必須出現兩次

