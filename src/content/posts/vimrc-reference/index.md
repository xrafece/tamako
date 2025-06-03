---
title: Vimrc Reference
published: 2025-05-10
description: 'vimrc file reference'
image: 'cover.jpg'
tags: [Vim, Vimrc]
category: 'Vim'
draft: false 
lang: ''
---

## Tabstop, softtabstop and shiftwidth

> [tab setting in vim](https://arisweedler.medium.com/tab-settings-in-vim-1ea0863c5990)

Difference of the value of tabstop, softtabstop and shiftwidth.

In order to determine how to place whitespace into your file upon receiving one of those commands, Vim refers to the values of the following settings.<br>
为了确定如何在收到这些命令之一时将空格放入文件中，Vim 会参考以下设置的值。

1. [tabstop](http://vimdoc.sourceforge.net/htmldoc/options.html#'tabstop') — How many columns wide is a tab character worth? Does a `\t` take up 4 columns? 2 columns? 8 columns? Changing this value will change how tabs are displayed. Must be greater than 0, as tabs having no width is never desired.<br>
   `tabstop` — 一个制表符值多少列宽？ `\t` 占用 4 列吗？ 2 列？ 8 列？更改此值将更改选项卡的显示方式。必须大于 0，因为不希望制表符没有宽度。
2. [shiftwidth](http://vimdoc.sourceforge.net/htmldoc/options.html#'shiftwidth') — Referred to for “levels of indentation”, where a level of indentation is shiftwidth columns of whitespace. That is, the shift-left-right commands, the formatting commands, and the behavior of vim with [cindent](http://vimdoc.sourceforge.net/htmldoc/options.html#'cindent') or [autoindent](http://vimdoc.sourceforge.net/htmldoc/options.html#'autoindent') set is determined by this setting. A shift command says “give me one more (or one less) level of indentation”. Formatting commands are more complicated (`:help gq` if you’re curious, they basically parse your file and determine how many layers of indentation should be there). So if vim decides that a line needs 3 levels of indentation then it’ll place 3 \* shiftwidth columns of whitespace.<br>
   `shiftwidth` — 称为“缩进级别”，其中缩进级别是空格的 shiftwidth 列。也就是说，左右移命令、格式化命令以及设置了 cindent 或 autoindent 的 vim 的行为都是由该设置决定的。移位命令表示“增加（或减少）一层缩进”。格式化命令更复杂（ `:help gq` 如果你好奇的话，它们基本上会解析你的文件并确定应该有多少层缩进）。因此，如果 vim 确定一行需要 3 级缩进，那么它将放置 3\*shiftwidth 的空白列。
3. [expandtab](http://vimdoc.sourceforge.net/htmldoc/options.html#'expandtab') — Unlike the other settings, this one is a Boolean, and quite straightforward. It answers the question: should vim place spaces or tab upon receiving a whitespace command or a `tab` keypress.<br>
   `expandtab` — 与其他设置不同，这是一个布尔值，并且非常简单。它回答了这个问题：vim 在接收到空格命令或 `tab` 按键时是否应该放置空格或制表符。
4. [softtabstop](http://vimdoc.sourceforge.net/htmldoc/options.html#'softtabstop') — Referred to for the `tab` key and `backspace` key. How much whitespace should be inserted when the `tab` key is pressed? And how much whitespace should be removed when the `backspace` key is pressed?<br>
   `softtabstop` — 指 `tab` 键和 `backspace` 键。按下 `tab` 键时应插入多少空格？当按下 `backspace` 键时应删除多少空格？

By default, softtabstop is disabled, meaning that hitting `tab` will add whitespace columns until vim gets to a `tabstop` (either 1 `\t` character or `tabstop` spaces, based on if `expandtab` is set). And hitting `backspace` will remove 1 character. This default behavior is weird when `expandtab` is set, the lack of parallelism feels weird: pressing `tab` will add `tabstop` characters to your file, but `backspace` will only remove 1. People normally want `backspace` to remove a tab’s worth of whitespace when they have `expandtab` set. So if you’re enabling `expandtab` then you should enable `softtabstop`.<br>
默认情况下，softtabstop 被禁用，这意味着点击 `tab` 将添加空白列，直到 vim 到达 `tabstop` （1 个 `\t` 字符或 在设置 `expandtab` 时 `tabstop` 个空格）。点击 `backspace` 将删除 1 个字符。设置 `expandtab` 时，这种默认行为很奇怪，缺乏并行性感觉很奇怪：按 `tab` 会将 `tabstop` 字符添加到文件中，但 `backspace` 时，人们通常希望 `backspace` 删除制表符的空白。因此，如果您启用 `expandtab` 那么您应该启用 `softtabstop` 。

Alright! Not too bad. Let’s just review really quickly:<br>
好吧！还不错。让我们快速回顾一下：

- `tabstop` is effectively how many columns of whitespace a `\t` is worth.<br>
  `tabstop` 实际上是 `\t` 值得多少列空白。
- `shiftwidth` is how many columns of whitespace a “level of indentation” is worth.<br>
  `shiftwidth` 是“缩进级别”值得多少列空白。
- Setting `expandtab` means that you never wanna see a `\t` again in your file.<br>
  设置 `expandtab` 意味着您再也不想在文件中看到 `\t`
- `softtabstop` is how many columns of whitespace a `tab` keypress or a `backspace` keypress is worth.<br>
  `softtabstop` 是 `tab` 按键或 `backspace` 按键值得多少列空白。

Well, that made it sound simple. But it gets kinda nuanced:<br>
嗯，这听起来很简单。但它有点微妙：

- By default (`softtabstop=0`, `noexpandtab`), a `tab` keypress will give you a `\t` character. And a `backspace` keypress will remove 1 character.<br>
  默认情况下（ `softtabstop=0` 、 `noexpandtab` ），按 `tab` 按键将为您提供 `\t` 字符。按下 `backspace` 按键将删除 1 个字符。
- If you set `expandtab`, then a `tab` keypress will give you `tabstop` spaces. A `backspace` keypress will remove 1 character.<br>
  如果您设置 `expandtab` ，则按 `tab` 按键将为您提供 `tabstop` 空格。按下 `backspace` 按键将删除 1 个字符。
- If you set `softtabstop`, then a `tab` keypress will give you `softabstop` spaces. A `backspace` keypress will remove `softabstop` columns of whitespace (or 1 character, if you’re in the middle of a block of text and stuff).<br>
  如果您设置 `softtabstop` ，则按 `tab` 按键将为您提供 `softabstop` 空格。按下 `backspace` 按键将删除 `softabstop` 列空白（或 1 个字符，如果您位于文本块和其他内容的中间）。

If you understand the abstractions that the designers of vim made, then everything is simple! Here’s how I understand it: enabling `softtabstop` says that `backspace` and `tab` should change of *columns of whitespace* instead of *characters*. And orthogonally related, `shiftwidth` is all about levels of indentation.<br>
如果你理解 vim 设计者所做的抽象，那么一切就变得简单了！我的理解是这样的：启用 `softtabstop` 表示 `backspace` 和 `tab` 应该更改空白列而不是字符。与正交相关的是， `shiftwidth` 与缩进级别有关。

## Vimrc file template

```vim
" 显示行号
set number
" 高亮搜索
set hlsearch
" 高亮自动匹配的括号
set showmatch
" set cursorline
" 显示标尺
set ruler
" 显示输入的命令
set showcmd
" 自动缩进
set autoindent
" 缩进时自动对齐缩进长度
set shiftround
" 输入tab时会转换为空格
set expandtab
" 缩进长度为4
set shiftwidth=4
set tabstop=4 softtabstop=4
" 次数暂时不起作用
set smarttab
" 语法高亮
syntax enable

autocmd FileType go setlocal noexpandtab
```

> [Introduction To Vim Customization](https://www.linode.com/docs/guides/introduction-to-vim-customization/)
