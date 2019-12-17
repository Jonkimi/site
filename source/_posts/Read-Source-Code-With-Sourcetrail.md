---
title: Read Source Code With Sourcetrail
tags: [Sourcetrail, Compilation Database, Bear]
description: Generate compilation database by Bear, and  sourcetrail 
date: 2019-12-13 13:43:50
---

## Install Sourcetrail

```shell    
brew update && brew cask install sourcetrail
```

## Project setup

1. Create a new project;
2. Add source group and choose source group type `C/C++ from Compilation Database*`;
3. Choose source files and include path.

more details can be found here: https://www.sourcetrail.com/documentation/#CreateaC/C++SourceGroupfromCompilationDatabase


## Compilation Database

1. CMake

    Use the CMAKE_EXPORT_COMPILE_COMMANDS flag. You can run
    
    ```shell
    cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
    ```

   or add the following line to your CMakeLists.txt script:

    ```cmake
    set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
    ```

    The compile_commands.json file will be put into the build directory. 

2. Non-CMake

    Use `Bear`, see {% post_link Language-Server-Protocol %}


## Reference

- https://www.sourcetrail.com
- https://www.jetbrains.com/help/clion/compilation-database.html#compdb_generate
- https://www.sourcetrail.com/documentation/#CreateaC/C++SourceGroupfromCompilationDatabase
