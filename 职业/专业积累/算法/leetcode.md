## 环境打造

- VC + leetcode
  - VC 安装插件 leetcode
  - 登录 leetcode 英文版
    - 点击 插件设置上的浏览器图标 Switch EndPoint
    - 选择cookies 登录
      - 从网页 all 请求中复制
  - 修改 leetcode 配置项
    - leetcode.workspaceFolder
    - LeetCode: Switch Default Language

- MinGW 64

  - 安装 MinGW
    - [官网链接](https://osdn.net/projects/mingw/releases/)
    - 点击 windows 图标下载
      - ![image-20201021110342535](..\Assert\Image\image-20201021110342535.png)
    - 运行安装 && 切换文件夹
    - 配置环境变量到 Path
  - 安装 gcc 
    - c 语言 编译器
  - 安装 g++
    - c++ 编译器
  - 安装 gdb
    - 代码调试器

- VC C++ 环境搭建

  - 安装插件 C++ 调试插件

  - 配置 luanch.json 

    - ```
      {
          // 使用 IntelliSense 了解相关属性。 
          // 悬停以查看现有属性的描述。
          // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
          "version": "0.2.0",
          "configurations": [
              {
                  "name": "g++ luanch", // 可自定义
                  "type": "cppdbg",
                  "request": "launch",
                  "program": "${fileDirname}\\${fileBasenameNoExtension}.exe", // 运行文件路径
                  "args": [],
                  "stopAtEntry": false,
                  "cwd": "${workspaceFolder}",
                  "environment": [],
                  "externalConsole": false,
                  "MIMode": "gdb",
                  "miDebuggerPath": "D:\\MinGW\\bin\\gdb.exe",// gdb 路径
                  "setupCommands": [
                      {
                          "description": "Enable pretty-printing for gdb",
                          "text": "-enable-pretty-printing",
                          "ignoreFailures": true
                      }
                  ],
                  "preLaunchTask": "g++" // 先运行 任务 g++ 编译c++程序 ，需在task.json中配置
              },
              {
                  "name": "gcc luanch",
                  "type": "cppdbg",
                  "request": "launch",
                  "program": "${fileDirname}\\${fileBasenameNoExtension}.exe",
                  "args": [],
                  "stopAtEntry": false,
                  "cwd": "${workspaceFolder}",
                  "environment": [],
                  "externalConsole": false,
                  "MIMode": "gdb",
                  "miDebuggerPath": "D:\\MinGW\\bin\\gdb.exe",
                  "setupCommands": [
                      {
                          "description": "Enable pretty-printing for gdb",
                          "text": "-enable-pretty-printing",
                          "ignoreFailures": true
                      }
                  ],
                  "preLaunchTask": "gcc"
              }
          ]
      }
      ```

  - 配置 task.json

    - ```
      {
          // See https://go.microsoft.com/fwlink/?LinkId=733558
          // for the documentation about the tasks.json format
          "version": "2.0.0",
          "tasks": [
              {
                  "label": "g++", // 任务标签，需与 luanch.json 里的 preLaunchTask 对应
                  "type": "shell",
                  "command": "D:\\MinGW\\bin\\g++.exe", // 配置编译c++的命令
                  "args": [
                      "-g",
                      "${file}",
                      "-o",
                      "${fileDirname}\\${fileBasenameNoExtension}.exe"
                  ],
                  "options": {
                      "cwd": "D:\\MinGW\\bin"
                  }
              },
              {
                  "label": "gcc",
                  "type": "shell",
                  "command": "D:\\MinGW\\bin\\gcc.exe",
                  "args": [
                      "-g",
                      "${file}",
                      "-o",
                      "${fileDirname}\\${fileBasenameNoExtension}.exe"
                  ],
                  "options": {
                      "cwd": "D:\\MinGW\\bin"
                  }
              }
          ]
      }
      ```

    - 