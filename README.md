# note.vim

使用vim的一些笔记

## 目录

<!-- vim-markdown-toc GFM -->

- [practical.vim.2nd笔记](#practicalvim2nd笔记)
- [命令会重复上次的修改](#命令会重复上次的修改)
- [不要自我重复](#不要自我重复)
- [以退为进](#以退为进)
- [执行 重复 回退](#执行-重复-回退)

<!-- vim-markdown-toc -->

---
practical.vim.2nd笔记
---

## 命令会重复上次的修改

- 上次的修改可表示很多东西：单位可以是字符/整行/整个文件
- 普通命令模式下，x dd > p 都是会引起修改的
- 从插入模式(i)开始到esc，这也是一次修改(重新移动光标会重置修改状态)

## 不要自我重复

- 减少无关的移动，将两个动作合并到一个按键
- C = c$ 相当于d$i,删除当前位置到行尾的数据并进入插入模式
- s = cl 相当于xi，删除当前字符，并进入插入模式
- I = ^i 移到内容首部，进入插入模式，不是行首
- A = $a 移动到行尾，进入插入模式
- o = A+CR 相当于在当前行下方插入新行
- O = ko 相当于kA+CR 在当前行的上方插入新行

## 以退为进

- 如果要插入一些文本，特别是某些文本的前后都要插入固定文本，使用s吧
- 可以理解为把部分字符替换成新的字符，使用s，如果是重复的，就用.
- 还可以配合f查找(带;的哪种)，使用;来查找位置，用.来进行替换

## 执行 重复 回退

- 让重复的工作(不管是移动还是执行)都可以重复
- 对应的undo撤销也是非常有必要的

目的|操作|重复|撤销
---|---|---|---
修改|{edit}|.|u
行内查找下一个字符|f{char}/t{char}|;|,
行内查找上一个字符|F{char}/T{char}|;|,
文档内查找下一处匹配项|/pattern回车|n|N
文档内查找上一处匹配项|?pattern回车|n|N
替换|:s/target/replacement|&|u
执行一系列修改|qx{change}q|@x|u
