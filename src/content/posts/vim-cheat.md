---
title: Vim Cheat
published: 2025-05-10
description: 'Use vim in terminal and vscode'
image: ''
tags: [Vim, Linux, VScode, CheatSheet]
category: 'Vim'
draft: false 
lang: ''
---

> [Vim Cheat Sheet (rtorr.com)](https://vim.rtorr.com/lang/zh_cn)
>
> [Cheat Sheet (Learn Vim VsCode Ext)](https://www.barbarianmeetscoding.com/boost-your-coding-fu-with-vscode-and-vim/cheatsheet)

## basic motion

```c
           ↑
     ← h j k l →
         ↓
```

恢复 Normal mode  `<ESC>` 、 `<CTRL-C>` 或 `<CTRL-[>`

Cheat for Windows shortkey

- `<CTRL-C>` 不再复制，而是返回正常模式。我们在 Vim 中用于复制的命令要简洁得多： `y` （用于复制）
- `<CTRL-V>` 不再粘贴，而是将您带入视觉块模式。同样，我们在 Vim 中用于粘贴的命令不太冗长：只需一个 `p` （用于粘贴）。
- `<CTRL-F>` 不再允许您搜索，而是允许您将屏幕向前滚动一页。（front，向前），我们在 Vim 中用于搜索的命令更加触手可及： `/{term-i-am-searching-for}` 。

## Move Fast Word By Word

- `w`      (**w**ord) move forward to the beginning of the next word

- `e`      (**e**nd) move forward to the end of the next word

- `b`      (**b**ack) move backword to the beginning of the previous word

- `ge`     move backword to the end of the previous word

  ---

- `W`      (**W**ord) move forward to the beginning of the next WORD

- `E`      (**E**nd) move forward to the end of the next WORD

- `B`      (**B**ack) move backword to the beginning of the pervious WORD

- `gE`     move backword to the end of the previous WORD

## Move To A Special Character In Line

- `f{character}`  (**f**ind) move forward to the next occurrence of a character in a line

- `t{character}` (un**t**il) move forward to the next occurrence of a character and place cursor just before it in a line

  ---

- `F{character}` (**F**ind) move backword to the next occurrence of a character in a line

- `T{character}` (un**T**il) move backword to the next occurrence of a character and place cursor just before it in a line

  ---

- `;` go to next occurrnece of {character}

- `,` go to pervious occerrence of {character}

## Move Horizontally Extremely

- `0` move to the first character of a line

- `^` move to the first non-blank character of a line

- `$` move to the end of a line

- `g_` move to the non-blank character at the end of a line

## Move Faster Vertically

- `}` jumps entire paragraphs downwards

- `{` jumps entire paragraphs upwards

- `CTRL-D` move **d**own half a page by scorlling the page

- `CTRL-U` move **u**p half a page by scorlling the page

## High Precision Vertical Motions With Search

- `/{pattern}` Search for {pattern} forward. {pattern} is regex

- `?{pattern}` Search for {pattern} backword.

  ---

- `/` repeat last search forward

- `?` repeat last search backword

  ---

- `n` go to next match

- `N` go to pervious match
