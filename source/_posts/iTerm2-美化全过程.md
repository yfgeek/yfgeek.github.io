---
title: iTerm2 美化全过程
tags: 'Mac'
id: 2
updated: '2016-08-05 21:14:29'
date: 2016-07-28 09:49:20
---


本文介绍了iTerm2的美化过程，包括安装oh my zsh，powerline，以及对vim的美化。
# 1.首先下载 iTerm 2
# 2.打开iTerm 2
# 3.输入下面指令安装oh-my-zsh
`curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh`
# 4.接下来安装Powerline
在官网有教程，我们只需要执行官网第一条安装指令就行
如果你的终端能够正常执行pip指令，那么直接执行下面的指令可以完成安装

`pip install powerline-status`

如果没有，则先执行安装pip指令

`sudo easy_install pip`

# 5.下载、安装库字体库
1）将工程下载下来后cd到`install.sh`文件所在目录
2）执行指令安装字体库

执行`./install.sh`指令安装所有Powerline字体

安装完成后提示所有字体均已下载到`/Users/superdanny/Library/Fonts`路径下
`All Powerline fonts installed to /Users/superdanny/Library/Fonts`

# 6. 设置iTerm 2的Regular Font 和 Non-ASCII Font

安装完字体库之后，把iTerm 2的设置里的Profile中的Text 选项卡中里的Regular Font和Non-ASCII Font的字体都设置成 Powerline的字体，我这里设置的字体是12pt Meslo LG S DZ Regular for Powerline

### 7. 配色方案
1）安装配色方案
进入刚刚下载的工程的solarized/iterm2-colors-solarized 下双击 Solarized Dark.itermcolors 和 Solarized Light.itermcolors 两个文件就可以把配置文件导入到 iTerm2 里

2）配置配色方案
通过load presets选择刚刚安装的配色主题即可

### 8. 使用agnoster主题
1）下载agnoster主题
到下载的工程里面运行install文件,主题将安装到`~/.oh-my-zsh/themes`目录下

2）设置该主题
进入~/.zshrc打开.zshrc文件，然后将`ZSH_THEME`后面的字段改为`agnoster`。`ZSH_THEME="agnoster"`（agnoster即为要设置的主题）

### 9. 增加指令高亮效果——zsh-syntax-highlighting
指令高亮效果作用是当用户输入正确命令时指令会绿色高亮，错误时命令红色高亮

1）cd到.zshrc所在目录

2）执行指令将工程克隆到当前目录

`git clone git://github.com/zsh-users/zsh-syntax-highlighting.git`

3）打开.zshrc文件，在最后添加下面内容

`source XXX/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh`

保存文件。

注意：xxx代表`.zshrc`所在目录

4）`cd ~/.oh-my-zsh/custom/plugins`

5）再次打开`.zshrc`文件，在最后面添加下面内容`plugins=(zsh-syntax-highlighting)`保存文件。

**启动iTerm 2 默认使用dash改用zsh解决方法：**
`chsh -s /bin/zsh`
### Vim

`$ cd solarized
$ cd vim-colors-solarized/colors
$ mkdir -p ~/.vim/colors
$ cp solarized.vim ~/.vim/colors/
`
`$ vi ~/.vimrc
syntax enable
set background=dark
colorscheme solarized`

### ls

Mac OS X 是基于 FreeBSD 的，所以一些工具 ls, top 等都是 BSD 那一套，ls 不是 GNU ls，所以即使 Terminal/iTerm2 配置了颜色，但是在 Mac 上敲入 ls 命令也不会显示高亮，可以通过安装 coreutils 来解决（`brew install coreutils`），不过如果对 ls 颜色不挑剔的话有个简单办法就是在 .bash_profile 里输出 CLICOLOR=1：

`$ vi ~/.bash_profile
export CLICOLOR=1`
