---
title: Language Server Protocol
tags: [LSP, ccls, Bear]
description: Language Server Protocol
date: 2019-12-11 16:17:59
---

# What

The Language Server Protocol (LSP) defines the protocol used between an editor or IDE and a language server that provides language features like auto complete, go to definition, find all references etc.

The idea behind the Language Server Protocol (LSP) is to standardize the protocol for how such servers and development tools communicate. This way, a single Language Server can be re-used in multiple development tools, which in turn can support multiple languages with minimal effort.

# How

Microsoft currently maintains a parallel list of language server implementations in the core LSP repository.

# Application

use ccls

## Installation

```shell
brew install ccls rapidjson
brew info ccls
```

## Confiuguration

`ccls` for vim, use `vim-plug` vim plugin manager.

```vim
function! BuildTABNine(info)
  " info is a dictionary with 3 fields
  " - name:   name of the plugin
  " - status: 'installed', 'updated', or 'unchanged'
  " - force:  set on PlugInstall! or PlugUpdate!
  if a:info.status == 'installed' || a:info.force
    !./install.py
  endif
endfunction
Plug 'zxqfl/tabnine-vim', { 'do': function('BuildTABNine') }
```
use command `:PlugInstall` Install tabnine-vim

modify `TabNine.toml`

```
command = "ccls"
args = ["--init={\"cacheDirectory\":\"${project_root}/.ccls\"}"]
synchronous_triggers = ["."]
synchronous_timeout_ms = 200
num_server_instances = 2
always_prefer = false
```

### Bear

Install Bear

```shell
git clone https://github.com/rizsotto/Bear.git
mkdir build32 build64
# 32
cd build32
cmake -DCMAKE_C_COMPILER_ARG1="-m32" ../Bear
make install

# 64
cd build64
cmake -DCMAKE_C_COMPILER_ARG1="-m64" ../Bear
make install
```

make sure `libear.so` in `LD_LIBRARY_PATH`

>ERROR: ld.so: object '/lib/x86_64-linux-gnu/libear.so' from LD_PRELOAD cannot be preloaded (wrong ELF class: ELFCLASS64): ignored. 

`libear.so` copy 32-bit library to `/usr/lib/i386-linux-gnu/` then copy 64-bit library to `/usr/lib/x86_64-linux-gnu/`.[^6]
Execute bear this way and get Compilation Database json:
```shell 
bear -l libear.so make_cmd
```

# Reference

- https://microsoft.github.io/language-server-protocol/
- https://microsoft.github.io/language-server-protocol/implementors/servers/
- https://langserver.org/
- https://github.com/MaskRay/ccls
- https://github.com/rizsotto/Bear
- https://github.com/rizsotto/Bear/issues/134#issuecomment-246750848
- https://tabnine.com/
- https://tabnine.com/semantic