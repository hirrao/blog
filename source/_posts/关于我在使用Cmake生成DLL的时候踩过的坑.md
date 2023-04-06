---
title: 关于我在使用Cmake生成DLL的时候踩过的坑
categories:
 - 编程
 - C++
tags: 
 - C++ 
 - 编程
 - CMAKE
 - Windows
date: 2023-04-06 17:00:00
---

本来老师要求用Visual Studio来做C++的大作业的，但是Visual Studio实在是太卡了，所以，我选择使用Cmake+Viual Studio Code来写这样一个东西，记录一下其中踩过的坑

# 生成DLL

这一步很简单，Cmake默认你的library都是静态存储库lib文件，要指定生成dll文件只需要加上一个SHARED参数即可

加入我想生成一个动态库命名为DLL1，其中源文件保存在Files_DLL1这个变量中，那么我的代码就是

```Cmake
add_library(DLL1 SHARED ${Files_DLL1})
```

这样Cmake就正确生成DLL，在VS中构建会同时生成.lib,.dll,.exp.pdb这四个文件

# 将DLL复制到输出路径

为了整个项目目录的整齐，我选择将库文件全部存储到lib文件夹中，而把可执行文件输出到bin文件夹中，这样就有了一个问题:

生成所有文件后，因为dll文件和exe文件不在同一个文件夹内，导致无法正常进行调试

## 解决方法

### 最简单的方法

最简单的就是每次手动复制过去，或者直接干脆把bin和lib两个文件夹合并，但是这样就违背了我们的初衷，要么增加一步步骤，要么文件结构变混乱，所以不推荐这种做法

## 最终解决办法

### 解决办法1

想要在bin和lib文件夹同时存在文件，可以将lib文件夹内的内容全部复制到bin中，最先想到的应该就是copy命令

```Cmake
file(COPY ${PROJECT_SOURCE_DIR/lib} DESTINATION ${PROJECT_SOURCE_DIR}/bin)
```

但是这样写的话，这段命令会优先于所有的构建进行，这样就导致无法复制任何文件到bin目录中，因为lib文件夹里在copy执行的时候是没有任何文件的，除非连续两次执行这一命令

但是目的是在一次操作中完成这些，所以无法使用这个方法，询问GPT-3.5，看看它是如何解决的

> 您可以在CMake中使用以下代码来生成一个DLL文件，并在生成后将其复制到bin文件夹下：
>
```Cmake
add_library(my_lib SHARED source_files)

#Set output directory of the library to the "lib" folder
set_target_properties(my_lib PROPERTIES
LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib
)
#Add a custom command to copy the library to the "bin" folder
add_custom_command(TARGET my_lib POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy 
        ${CMAKE_BINARY_DIR}/lib/my_lib.dll
        ${CMAKE_BINARY_DIR}/bin/my_lib.dll
)
```
> 这里假设您的DLL文件名为my_lib.dll，源文件在source_files变量中列出。使用add_library命令来创建动态链接库，set_target_properties命令可将输出目录设置为lib文件夹。最后，使用add_custom_command命令添加在生成后自动复制DLL文件到bin文件夹的自定义命令。

GPT设置的路径和我实际的路径不相同，并且copy命令是直接进行复制文件的，需要进行一些改变，改成对文件夹进行操作并修正路径后这段代码变为

```Cmake
set_target_properties(${LibraryName} PROPERTIES

LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib
)

add_custom_command(TARGET ${LibraryName} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
    ${PROJECT_SOURCE_DIR}/lib
    ${PROJECT_SOURCE_DIR}/bin
)
```
这段代码正确执行了

### 解决办法2

还可以在两个文件夹中同时输出两份文件，这样可以用这一段代码

```Cmake
SET_TARGET_PROPERTIES(${LibraryName} PROPERTIES LINKER_LANGUAGE CXX
        RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin
        LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib
        OUTPUT_NAME "${LibraryName}"
        PREFIX "")
```

但是这样操作的话，使用VS构建，生成的是lib文件和exp文件，而并非想要的dll文件和exp文件，这种情况只需要在SET_TARGET_PROPERTIES中加上一句

```Cmake
SUFFIX ".dll"
```

这样完整的代码是

```Cmake
SET_TARGET_PROPERTIES(${LibraryName} PROPERTIES LINKER_LANGUAGE CXX
        RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin
        LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib
        OUTPUT_NAME "${LibraryName}"
        PREFIX ""
        SUFFIX ".dll")
```
这样也能达到想要的效果