* launch.json
```json
{

    "version": "0.2.0",

    "configurations": [

        {

            "name": "g++.exe - Build and debug active file",

            "type": "cppdbg",

            "request": "launch",

            "program": "${fileDirname}\\${fileBasenameNoExtension}.exe",

            "args": [],

            "stopAtEntry": false,

            "cwd": "${fileDirname}",

            "environment": [],

            "externalConsole": false,

            "MIMode": "gdb",

            "miDebuggerPath": "D:\\AppData\\mingw64\\bin\\gdb.exe",

            "setupCommands": [

                {

                    "description": "Enable pretty-printing for gdb",

                    "text": "-enable-pretty-printing",

                    "ignoreFailures": true

                }

            ],

            "preLaunchTask": "C/C++: g++.exe 生成活动文件"

        }

    ]

}
```

* tasks.json
```json
{

    "tasks": [

        {

            "type": "cppbuild",

            "label": "C/C++: g++.exe 生成活动文件",

            "command": "D:\\AppData\\mingw64\\bin\\g++.exe",

            "args": [

                "-fdiagnostics-color=always",

                "-g",

                "${file}",

                "-o",

                "${fileDirname}\\${fileBasenameNoExtension}.exe"

            ],

            "options": {

                "cwd": "${fileDirname}"

            },

            "problemMatcher": [

                "$gcc"

            ],

            "group": {

                "kind": "build",

                "isDefault": true

            },

            "detail": "调试器生成的任务。"

        }

    ],

    "version": "2.0.0"

}
```