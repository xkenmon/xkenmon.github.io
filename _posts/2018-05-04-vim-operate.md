---
layout: post
title: vim常用操作
tags: [vim,linux,效率]
---

* TOC
{:toc}

## 剪切板

### vim中的剪切板

vim中共有12个剪切板可供使用，可通过 ```:reg``` 查看:
<table>
		<tbody>
				<tr>
						<td>
								名称
						</td>
						<td>
								标识
						</td>
						<td>
								说明
						</td>
				</tr>
				<tr>
						<td>
								无名（unnamed）寄存器
						</td>
						<td>
								""
						</td>
						<td>
								缓存最后一次操作内容；
						</td>
				</tr>
				<tr>
						<td>
								数字（numbered）寄存器
						</td>
						<td>
								"0 - "9
						</td>
						<td>
								缓存最近操作内容，复制与删除有别；
						</td>
				</tr>
				<tr>
						<td>
								行内删除（small delete）寄存器
						</td>
						<td>
								"-
						</td>
						<td>
								缓存行内删除内容；
						</td>
				</tr>
				<tr>
						<td>
								具名（named）寄存器
						</td>
						<td>
								"a - "z或"A - "Z
						</td>
						<td>
								指定时可用；
						</td>
				</tr>
				<tr>
						<td>
								只读（read-only）寄存器
						</td>
						<td>
								":, "., "%, "#
						</td>
						<td>
								分别缓存最近命令、最近插入文本、当前文件名、当前交替文件名；
						</td>
				</tr>
				<tr>
						<td>
								表达式（expression）寄存器
						</td>
						<td>
								"=
						</td>
						<td>
								只读，用于执行表达式命令；
						</td>
				</tr>
				<tr>
						<td>
								选择及拖拽（selection and drop）寄存器
						</td>
						<td>
								"*, "+, "~
						</td>
						<td>
								存取GUI选择文本，可用于与外部应用交互，使用前提为系统剪切板（clipboard）可用；
						</td>
				</tr>
				<tr>
						<td>
								黑洞（black hole）寄存器
						</td>
						<td>
								"_
						</td>
						<td>
								不缓存操作内容（干净删除）；
						</td>
				</tr>
				<tr>
						<td>
								模式寄存器（last search pattern）
						</td>
						<td>
								"/
						</td>
						<td>
								缓存最近的搜索模式。
						</td>
				</tr>
		</tbody>
</table>

### 常用剪切板操作

平常使用的 **y** 就是将内容复制到**""**号剪切板，**p**也是从**""**号剪切板粘贴

系统剪切板中的内容存储在```"+```剪切板中,如果粘贴```"+```剪切板中的内容可使用```"+p```粘贴

### 启用系统剪切板

如果使用**:reg**发现没有**"+**号剪切板，可能是该版本vim不支持剪切板操作

可使用 **vim --version  grep clipboard** 查看vim的信息:

```bash
$ vim --version|grep clipboard
-clipboard         +jumplist          +persistent_undo   +virtualedit
-ebcdic            +mouseshape        +statusline        -xterm_clipboard
```

clipboard前是-说明不支持剪切板操作，如果使用gnome桌面可使用如下指令安装：

```bash
$ sudo apt-get install vim-gnome
```

安装完成后即可使用系统剪切板

## 宏录制

作为编辑器之神，宏录制功能当然是必不可少的了，使用也比较简单

### 录制

具体使用：

第一步：在正常模式下（非insert模式、非visual模式）按下q键盘

第二步：选择a-z或0-9中任意一个作为缓冲器的名字，准备开始录制宏

第三步：正常的操作，此次所有的操作都会被记录在上一步中定义的缓冲器中

第四步：在非insert模式下输入q停止宏的录制

第五步：使用@ + 第二步中定义的缓冲器的名字即可。

recording还可以和查询结合起来使用，例如想把一个文件中含有特定字符串的行注释，可以通过这样的宏来实现。在正常模式下输入``` /search string + enter、shift + ^、i、#、esc、shift + $ ```。

### 播放

先输入重复次数，然后输入@+缓冲器的名字，如执行q缓冲器中的宏100遍可输入：__100@q__

## 查找与替换
>此篇参考自[链接]( https://harttle.land/2016/08/08/vim-search-in-file.html)

### 查找

在normal模式下按下```/```即可进入查找模式，输入要查找的字符串并按下回车。 Vim会跳转到第一个匹配。按下n查找下一个，按下N查找上一个。

Vim查找支持正则表达式，例如```/vim$```匹配行尾的"vim"。 需要查找特殊字符需要转义，例如```/vim\$```匹配"vim$"。
>注意:查找回车应当用\n，而替换为回车应当用\r（相当于``` <CR>```）

### 大小写敏感配置

Vim 默认采用大小写敏感的查找，为了方便我们常常将其配置为大小写不敏感：

```vim
" 设置默认进行大小写不敏感查找
set ignorecase
" 如果有一个大写字母，则切换到大小写敏感查找
set smartcase
```

### 查找当前单词

在normal模式下按下*即可查找光标所在单词（word）， 要求每次出现的前后为空白字符或标点符号。例如当前为foo， 可以匹配foo bar中的foo，但不可匹配foobar中的foo。 这在查找函数名、变量名时非常有用。

按下```g*```即可查找光标所在单词的字符序列，每次出现前后字符无要求。 即foo bar和foobar中的foo均可被匹配到。

### 查找与替换

:s（substitute）命令用来查找和替换字符串。语法如下：

```
:{作用范围}s/{目标}/{替换}/{替换标志}
```

例如```:%s/foo/bar/g```会在全局范围(%)查找foo并替换为bar，所有出现都会被替换（g）。

#### 作用范围

作用范围分为当前行、全文、选区等等。

当前行：

```vim
:s/foo/bar/g
```

全文：

```vim
:%s/foo/bar/g
```

选区，在Visual模式下选择区域后输入:，Vim即可自动补全为 :'<,'>。

```vim
:'<,'>s/foo/bar/g
```

2-11行：

```vim
:5,12s/foo/bar/g
```

当前行.与接下来两行+2：

```vim
:.,+2s/foo/bar/g
```

#### 替换标志

上文中命令结尾的g即是替换标志之一，表示全局global替换（即替换目标的所有出现）。 还有很多其他有用的替换标志：

空替换标志表示只替换从光标位置开始，目标的第一次出现：

```vim
:%s/foo/bar
```

i表示大小写不敏感查找，I表示大小写敏感：

``` vim
:%s/foo/bar/i
# 等效于模式中的\c（不敏感）或\C（敏感）
:%s/foo\c/bar
```

c表示需要确认，例如全局查找"foo"替换为"bar"并且需要确认：

``` vim
:%s/foo/bar/gc
```

回车后Vim会将光标移动到每一次"foo"出现的位置，并提示

``` vim
replace with bar (y/n/a/q/l/^E/^Y)?
```

按下y表示替换，n表示不替换，a表示替换所有，q表示退出查找模式， l表示替换当前位置并退出。^E与^Y是光标移动快捷键
