---
layout: post
title: "Mac OS X自带Vim的配置"
categories:
-notes
tag:
- Vim
- OS X


---

# Mac OS X自带Vim的配置
据说有三个问题可以在程序员的世界引起战争：

- 最棒的编程语言是什么？
- 最好的操作系统是什么？
- 最便捷的编辑器是什么？

在我不长的编程生涯中，这三个问题一直就影响着我，我不断的进行着选择与被选择。然后，我学习了Python，并认定这就是第一题的答案。再然后我接触了Mac，第二题的答案也显而易见了。所以呢，第三题的答案，在我用了一小段时间 __Vim__ 之后，似乎也差不多出来了。

## 一.Vim的介绍
__Vim__ 是一款Linux下最常用也是最通用的古老编辑器，给人的印象就是黑底白字或者花花绿绿的符号在荧光屏上闪烁。它让程序员获得敲击键盘的快感，也让普通人产生神秘感。但是随着这些年的发展，各大语言都有了对应的且整合维护都完好的IDE，单纯使用 __Vim__ 作为开发工具的程序员越来越少。但是只要是谈及编辑器的选择， __Vim__ 始终是绕不开的话题。
 
__Vim__ 已经被集成在了Mac OS X中，已经无需安装。当然，更多习惯使用Vim作为开发工具的Mac用户可能选择了 __MacVim__ 这款开源且一直保持更新的特别版本，但我这里所记录的所有__Vim__配置及相关插件仅针对系统预置的 __Vim__ （version7.3）。
## 二.Vim的配置
首先，直接打开终端输入命令`vim`就能直接看到系统__Vim__信息，其中 __.vimrc__ 和 __.vim__ 就是 __Vim__ 的配置文件和插件目录，它们都在当前用户的根目录下。

![version](http://ww1.sinaimg.cn/large/5657ca78tw1emqj5oxm99j21c20ts17a.jpg)

使用__Vim__打开__.vimrc__进行配置，若没有则新建一个，下面是我的

	 1 syn on                      "语法支持
	 2
	 3 "common conf {{             通用配置
	 4 set ai                      "自动缩进
	 5 set bs=2                    "在insert模式下用退格键删除
	 6 set showmatch               "代码匹配
	 7 set laststatus=2            "总是显示状态行
	 8 set expandtab               "以下三个配置配合使用，设置tab和缩进空格数
	 9 set shiftwidth=4
	10 set tabstop=4
	11 set cursorline              "为光标所在行加下划线
	12 set number                  "显示行号
	13 set autoread                "文件在Vim之外修改过，自动重新读入
	14
	15 set ignorecase              "检索时忽略大小写
	16 set fileencodings=uft-8,gbk "使用utf-8或gbk打开文件
	17 set hls                     "检索时高亮显示匹配项
	18 set helplang=cn             "帮助系统设置为中文
	19 set foldmethod=syntax       "代码折叠
	20 "}}
	21
	22 "conf for tabs, 为标签页进行的配置，通过ctrl h/l切换标签等
	23 let mapleader = ','
	24 nnoremap <C-l> gt
	25 nnoremap <C-h> gT
	26 nnoremap <leader>t : tabe<CR>
	27
	28 "conf for plugins {{ 插件相关的配置
	29 "状态栏的配置
	30 "powerline{
	31 set guifont=PowerlineSymbols\ for\ Powerline
	32 set nocompatible
	33 set t_Co=256
	34 let g:Powerline_symbols = 'fancy'
	35 "}
	36 "NERDTree配置
	37 "NERDTree{
	38 let NERDTreeShowHidden = 1
	39 let NERDTreeMouseMode = 2
	40 "}
	41 "pathogen是Vim用来管理插件的插件
	42 "pathogen{
	43 call pathogen#infect()
	44 "}
	45
	46 "}}

保存退出，可能会有错误提示，没关系因为插件还没安装。其中`"`是注释的意思，将配置分类使用`{`分隔开来方便查看。这个配置中还包含了三个插件（ __powerline__ 、 __NERDTree__ 和 __pathogen__ ）的配置，后面会介绍到。

配置完成__.vimrc__ 再来看看 __.vim__ 目录：

	.vim
	  |___doc          //存放文档
	  |___plugin       //存放插件
	  |___autoload     //存放随Vim启动自动加载的插件
	  
	  
可能你的 __.vim__ 下面没有任何东西，没关系，先不着急新建。当使用的插件很少且插件本身都设计简单时，按照这样的存放是没有问题的。可是多数时候我们都会安装很多各种不同插件，导致整个目录都很混乱，所以就有了 __pathogen__ 插件。这款插件是用来管理 __Vim__ 中的插件的，它可以使目录结构很简单且使用起来也十分方便。

##三.Vim插件的安装
丰富的插件使得 __Vim__ 早已自成一派，有了自己的完整生态， __Vim__ 的插件有自己的开发语言  __vimscript__ ，同时也为其他语言提供支持。 __Vim__ 的插件都是 __.vim__ 后缀的脚本文件，可以在 __github__ 上方面找到很多。具体的插件分类就不赘述了，可以参考文末的给出的链接或网上搜索。下面首先介绍用来管理插件的插件，然后再介绍几种常用插件。
###pathogen
####1.介绍

前面提到 __.vim__ 的目录结构，但事实上使用 __pathogen__ 管理插件则要调整目录了。

![nerdtree](http://ww4.sinaimg.cn/large/5657ca78tw1emqj7mfyo3j21c20tsgsj.jpg)

图中可以看到，在目录中没有plugin文件夹，取而代之的是bundle文件夹。关于bundle的概念可以网上查阅相关资料，这里理解为一种封装方式，方便 __pathogen__ 管理。插件安装后，要获得插件的doc，通常要在Vim中运行:Helptags来生成在线帮助tags。

####2.安装
下载地址 <https://github.com/tpope/vim-pathogen>

- __.vim__ 下新建 __.autoload__ 和 __.bundle__ 文件夹
`mkdir ~/.vim/.autoload
 ~/.vim/.bundle`
- 下载 __pathogen__ 到 __.autoload__ 目录下。
`git clone git://github.com/tpope/vim-pathogen.git`
- 在 __.vimrc__ 中添加配置信息	
`call pathogen#infect()` 
	
###NERDTree
####1.介绍
NERDTree是一款用来获得树状展示文件列表的插件，上面图中的展示效果就是NERDTree提供的。
####2.安装
下载地址 <https://github.com/scrooloose/nerdtree>

- 进入 __.vim__ 下的 __.bundle__ 目录
- 下载 __NERDTree__ 到 __.bundle__ 下面	
`git clone git://github.com/scrooloose/nerdtree.git`
- 下载安装完成后可以在 __.bundle__ 目录下多出 __.NERDTree__ 文件夹
- 重启 __Vim__ 后运行 `:Helptags`来生成 __NERDTree__ 的在线帮助tags

####3.说明
 __.NERDTree__ 提供了很多操作热键，以及相关的配置，可以通过它提供的文档查看，也可以网上搜索。
 
在 __Vim__ 中输入`:NERDTree`，即可呼出执行Vim命令的当前目录的文件目录，也可以在 __.vimrc__ 中定义快捷键直接调出 __NERDTree__ 。

常用的快捷键：
	
- h j k l移动光标定位
- o 打开关闭文件或者目录，如果是文件的话，光标出现在打开的文件中
- go 效果同上，不过光标保持在文件目录里，类似预览文件内容的功能
- i和s可以水平分割或纵向分割窗口打开文件，前面加g类似go的功能
- t 在标签页中打开
 -T 在后台标签页中打开
- p 到上层目录
- P 到根目录
- K 到同目录第一个节点
- J 到同目录最后一个节点
- m 显示文件系统菜单（添加、删除、移动操作）
- ? 帮助
- q 关闭 
- Ctrl + w + h 光标定位在左侧目录， Ctrl + w + l 光标定位在右侧文件，多次按Ctrl + w 会在两边切换

想了解更多操作方式，可以通过 __?__ 查看详细的帮助信息。

###Command-T
####1.介绍
__Command-T__ 是一个基于 __Ruby__ 和 __C__ 扩展实现的快速文件浏览的插件，类似TextMate的Go to File（Command+T呼出）功能，或Eclipse的Open Resource（Command+Shift+r）功能，可以通过模糊匹配快速定位并打开文件。
![commandt](http://ww2.sinaimg.cn/large/5657ca78tw1emqj9l0ee0j21c20ts7h1.jpg)
####2.安装
下载地址 <https://wincent.com/products/command-t>

- 从下载地址下载最新版本的vba文件，目前最新版本是1.4，所以安装文件是command-t-1.4.vba
- 在 __~/.vim/bundle__ 目录下创建文件夹 __command-t__
- 用 __Vim__ 打开command-t-1.4.vba
- 执行`:UseVimball ~/.vim/bundle/command-t`
- 进入ruby目录下编译C扩展
`cd ~/.vim/bundle/command-t/ruby/command-t`		
`ruby extconf.rb`				
`make`

####3.说明
输入：CommandT进入文件快速定位功能
		
- ctrl+j/k 上下选择文件，选中后回车打开文件
- ctrl+t 以tab方式打开文件
- ctrl+s/v 可以水平或垂直分割窗口打开文件
- ctrl+c 退出该模式

该插件还有个常用命令`:CommandTBuffer`，可以浏览缓冲区的文件，并重新打开。操作方式同上。关于缓冲区，后面会有说明。
###Powerline
####1.介绍
__Powerline__ 是 __Vim__ 的一个非常漂亮的状态栏插件，安装了 __Powerline__ 之后， __Vim__ 底部将会出现一个增强型状态栏，当 __Vim__ 处于NORMAL、INSERT、BLOCK等状态时，状态栏会呈现不同的颜色，同时状态栏还会显示当前编辑文件的格式（uft-8等）、文件类型（java、xml等）和光标位置等，喜欢的就装。
![powerline](http://ww3.sinaimg.cn/large/5657ca78tw1emqjat9od2j21c20tsqbf.jpg)
####2.安装
下载地址 https://github.com/Lokaltog/vim-powerline

- 进入.vim/bundle目录
- 执行`git clone git://github.com/Lokaltog/vim-powerline.git`
- 在.vimrc中设置状态栏主题		

		"powerline{	
 		set guifont=PowerlineSymbols\ for\ Powerline
 		set nocompatible	
 		set t_Co=256	
 		let g:Powerline_symbols = 'fancy'	
 		"}
####3.说明
重启 __.Vim__ 就可以看到底部的状态栏了。

------------
本文内容参考资料：

[谁说Vim不是IDE？（一）](http://www.cnblogs.com/chijianqiang/archive/2012/10/30/2746899.html)

[Vim之NerdTree的帮助](http://www.cnblogs.com/mo-beifeng/archive/2011/09/08/2171018.html)

关于Window与Tab的使用，Buffer缓冲区的说明以及更多插件快捷键等信息都来自以上Blog的指导，多谢。