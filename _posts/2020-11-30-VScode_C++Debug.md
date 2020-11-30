---
title: VScode Debug C++ Code
date: 2020-11-30
categories:
- Software-configuration
tags:
- VScode
---

其实很多详细的细节已经记录在了[官方的教程中](https://code.visualstudio.com/docs/cpp/config-linux)。这里我只是记录一下方便下次直接使用。

vscode program 创建了一个 .vscode 的文件夹，里面有 launch.json 和 tasks.json 两个文件。这两个文件的功能分别是  
`tasks.json (compiler build settings)`   
`launch.json (debugger settings)`  
这两个功能。在[官方介绍中](https://code.visualstudio.com/docs/cpp/config-linux)还有一个文件  
`c_cpp_properties.json (compiler path and IntelliSense settings)`

还有一个在平时比较常见的就是  setting.json 位于 `~/.config/Code/User/settings.json` 中。  

这个 setting.json 是配置整个VScode的。主要看其他两个 `tasks.json  launch.json`。

### Check Software

**check:**  

`gcc --version`
`gdb --version`

如果没有安装
**install**

`sudo apt-get install gcc`
`sudo apt-get install build-essential gdb`

### Build Program

task.json 是告诉 VScode 要如何编译这个工程。也可以使用默认生成的 task.json 选项 `Terminal > Configure Default Build Task.` 选择 `C/C++: g++ build active file` 会生成类似如下 json 代码。  

**官方例子**

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "type": "shell",
      "label": "g++ build active file",
      "command": "/usr/bin/g++",
      "args": ["-g", "${file}", "-o", "${fileDirname}/${fileBasenameNoExtension}"],
      "options": {
        "cwd": "/usr/bin"
      },
      "problemMatcher": ["$gcc"],
      "group": {
        "kind": "build",
        "isDefault": true
      }
    }
  ]
}
```

这个 ifDefault 官方说就是为了方便设立的，按下 `Ctrl + Shift + B` 下次就可以直接运行了，要是不设置下次还得点 Run Bulid Task。其他参数基本上看看就能明白了。label 里面的值就是个名字，你想改什么就改什么。


### Debug Program

选择 `Run > Add Configuration`  选 `C++ (GDB/LLDB)` 在下拉栏中选 `g++ build and debug active file`.  

也会生成 launch.json 文件用于告诉 VScode 如何 Debug 项目。 **官方例子**  

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "g++ build and debug active file",
      "type": "cppdbg",
      "request": "launch",
      "program": "${fileDirname}/${fileBasenameNoExtension}",
      "args": [],
      "stopAtEntry": false,
      "cwd": "${workspaceFolder}",
      "environment": [],
      "externalConsole": false,
      "MIMode": "gdb",
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing",
          "ignoreFailures": true
        }
      ],
      "preLaunchTask": "g++ build active file",
      "miDebuggerPath": "/usr/bin/gdb"
    }
  ]
}
```

`stopAtEntry` 这个如果被设置为 true 那么会在调试的时候在 main 函数时停住。  



