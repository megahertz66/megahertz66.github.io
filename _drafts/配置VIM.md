# 配置 VIM

---------

-----------------

## 一. 安装插件管理工具

vim-plug 是一个 vim 的插件管理插件(A minimalist Vim plugin manager)，安装使用简单：

首先下载 plug.vim 文件，[下载地址]([GitHub - junegunn/vim-plug: Minimalist Vim Plugin Manager](https://github.com/junegunn/vim-plug))。

并将文件放在 windows 中的 `~/vimfiles/autoload` 或 unix 中的 `~/.vim/autoload` 文件夹内。

**安装插件：**

-------

安装插件，只需要将插件写在 .vimrc 内，然后在 vim 中使用 `:PlugInstall` 命令即可：

```
call plug#begin('~/.vim/plugged')

Plug 'vim-airline/vim-airline'

call plug#end()
```

**确保插件要放在 begin 和 end 之间**

重新打开 vim 使用命令 `:PlugInstall`

Finishing ... Done! 表示安装完成

**删除插件:**

--------------

删除插件，只需要将写在 .vimrc 配置文件内的插件删除，重启 vim 并执行命令 `:PlugClean` 即可：

```
call plug#begin('~/.vim/plugged')

call plug#end()
```

保存在 vim 中使用 `:PlugClean`:

**插件推荐：**

-----

[知乎的插件推荐])(https://zhuanlan.zhihu.com/p/24742679?refer=hack-vim#:~:text=Vim%20%E5%AE%9E%E7%94%A8%E6%8F%92%E4%BB%B6%E6%8E%A8%E8%8D%90%EF%BC%882017%EF%BC%89%201%20%E6%8F%92%E4%BB%B6%E7%AE%A1%E7%90%86%E5%99%A8%202%20%E6%96%87%E4%BB%B6%EF%BC%8C%E4%BB%A3%E7%A0%81%E6%90%9C%E7%B4%A2%E5%B7%A5%E5%85%B7%203%20%E8%87%AA%E5%8A%A8%E8%A1%A5%E5%85%A8.,Git%20%2C%20Svn%20%29%208%20%E6%94%B9%E5%96%84%E7%94%9F%E6%B4%BB.%20%E7%8E%A9Vim%E5%BE%88%E4%B9%85%E7%9A%84%E8%80%81%E6%B2%B9%E6%9D%A1%E5%BA%94%E8%AF%A5%E9%83%BD%E7%9F%A5%E9%81%93%EF%BC%8C%E5%85%88%E4%B8%8D%E8%A3%85%E9%80%BC%E6%88%91%E4%BB%AC%20)

1. 代码补全 [neocomplete]([GitHub - Shougo/neocomplete.vim: Next generation completion framework after neocomplcache](https://github.com/Shougo/neocomplete.vim))

2. 代码检查 [ALE]([GitHub - dense-analysis/ale: Check syntax in Vim asynchronously and fix files, with Language Server Protocol (LSP) support](https://github.com/dense-analysis/ale))

3. [arilrine]([GitHub - vim-airline/vim-airline: lean &amp; mean status/tabline for vim that&#39;s light as air](https://github.com/vim-airline/vim-airline))

## Vim 8 中 C/C++ 符号索引：GTags

**gutentags安装方法：**

------

使用插件管理器[vim-plug](http://link.zhihu.com/?target=https%3A//vim.ink/vim-plug.html)安装vim-gutentags插件。

在配置文件 `~/.vimrc` 中增加配置项 `Plug 'ludovicchabant/vim-gutentags'` 后再在vim命令行模式下执行命令 `:PlugInstall` 即可完成vim-gutentags插件的安装。

## 下载 Gtags

[Getting GLOBAL 下载地址](https://www.gnu.org/software/global/download.html)

**使用说明：**

---------
