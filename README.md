# vivado_and_lvim_archlinux

This is a the way I manage to use vivado and a vhdl lsp server on lunarcim on my arch linux installation

### DISCLAIMER: This is simply what I've done on my system in order to make everything work. This is not a guide.

## 1-Vivado

version installed: 2017.3

Dependencies:

You'll need a library otherwise the install will freeze
```
yay -S ncurses5-compat-libs
```

Without this command vivado will lauch as a blank with page
```
export _JAVA_AWT_WM_NONREPARENTING=1
```

After running the export command you are ready to lauch the installer (from the same terminal).

In order to use laucher for executing vivado I suggest creating a simple bash file 

```
# !/bin/bash

export _JAVA_AWT_WM_NONREPARENTING=1
{vivado install folder}/Xilinx/Vivado/2017.3/bin/vivado
```
done

## 2-LunarVim

There are 2 lsp server that I've tried

### ghdl-ls

Install ghdl from source (using pacman didn't work for me)

```
gcc-ada # required for compiling
git clone https://github.com/ghdl/ghdl.git
cd ghdl/
./configure --prefix=/usr/local
make
sudo make install
```
Install ghdl-ls
```
pip install git+https://github.com/ghdl/ghdl.git@$(ghdl version hash)
```
If everything has gone according to plan the output of
```
ghdl-ls --disp-config
```
should look something like this:

```
command line prefix (--PREFIX): (not set)
environment prefix (GHDL_PREFIX): (not set)
exec prefix (from program name): /usr/local

library prefix: /usr/local/lib/ghdl
library directory: /usr/local/lib/ghdl
python:
sys.platform: linux, os.name: posix
3.10.7 (main, Sep  6 2022, 21:22:27) [GCC 12.2.0]
```

Finally add to your lvim config.lua
```
require'lspconfig'.ghdl_ls.setup{}
```

done

### hdl_checker

Install it
```
pip3 install hdl-checker --upgrade
```
Add to your lvim config 

```
if not require'lspconfig.configs'.hdl_checker then
  require'lspconfig.configs'.hdl_checker = {
    default_config = {
    cmd = {"hdl_checker", "--lsp", };
    filetypes = {"vhdl", "verilog", "systemverilog"};
      root_dir = function(fname)
        -- will look for the .hdl_checker.config file in parent directory, a
        -- .git directory, or else use the current directory, in that order.
        local util = require'lspconfig'.util
        return util.root_pattern('.hdl_checker.config')(fname) or util.find_git_ancestor(fname) or util.path.dirname(fname)
      end;
      settings = {};
    };
  }
end
```

done

## Autoindent

Set default tab to 4 spaces

Add to lvim config
```
-- 4 spaces tab
local set = vim.opt -- set options
set.tabstop = 4
set.softtabstop = 4
set.shiftwidth = 4
```

Download vhdl.vim from https://www.vim.org/scripts/script.php?script_id=1450

add it to /usr/share/vim/vim90/indent/
```
sudo cp ~/Downloads/vhdl.vim /usr/share/vim/vim90/indent/
```

done









