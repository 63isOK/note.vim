# note.vim

使用vim的一些笔记

---
practical.vim.2nd笔记
---

## 目录

<!-- vim-markdown-toc GFM -->

- [.命令](#命令)
  - [.命令会重复上次的修改](#命令会重复上次的修改)
  - [不要自我重复](#不要自我重复)
  - [以退为进](#以退为进)
  - [执行 重复 回退](#执行-重复-回退)
  - [查找并手动替换](#查找并手动替换)
  - [.范式](#范式)
- [vim普通模式](#vim普通模式)
  - [停顿时请移开画笔](#停顿时请移开画笔)
  - [把撤销单元切成块](#把撤销单元切成块)
  - [构造可重复的修改](#构造可重复的修改)
  - [用次数做简单的算术运算](#用次数做简单的算术运算)
  - [能够重复就别用次数](#能够重复就别用次数)
  - [双剑合璧，天下无敌](#双剑合璧天下无敌)
- [插入模式](#插入模式)
  - [在插入模式中可即时更正错误](#在插入模式中可即时更正错误)
  - [返回普通模式](#返回普通模式)
  - [不离开插入模式，粘贴寄存器中的文本](#不离开插入模式粘贴寄存器中的文本)
  - [随时随地做运算](#随时随地做运算)
  - [用字符编码插入不常用的字符](#用字符编码插入不常用的字符)
  - [用digraph俩插入不常用的字符](#用digraph俩插入不常用的字符)
  - [用替换模式替换已有的文本](#用替换模式替换已有的文本)
- [可视模式](#可视模式)
  - [深入理解可视模式](#深入理解可视模式)
  - [选择高亮选区](#选择高亮选区)

<!-- vim-markdown-toc -->

## .命令

### .命令会重复上次的修改

- 上次的修改可表示很多东西：单位可以是字符/整行/整个文件
- 普通命令模式下，x dd > p 都是会引起修改的
- 从插入模式(i)开始到esc，这也是一次修改(重新移动光标会重置修改状态)

### 不要自我重复

- 减少无关的移动，将两个动作合并到一个按键
- C = c$ 相当于d$i,删除当前位置到行尾的数据并进入插入模式
- s = cl 相当于xi，删除当前字符，并进入插入模式
- I = ^i 移到内容首部，进入插入模式，不是行首
- A = $a 移动到行尾，进入插入模式
- o = A+CR 相当于在当前行下方插入新行
- O = ko 相当于kA+CR 在当前行的上方插入新行

### 以退为进

- 如果要插入一些文本，特别是某些文本的前后都要插入固定文本，使用s吧
- 可以理解为把部分字符替换成新的字符，使用s，如果是重复的，就用.
- 还可以配合f查找(带;的哪种)，使用;来查找位置，用.来进行替换

### 执行 重复 回退

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

### 查找并手动替换

- :%s/abc/def/g 将全部的abc替换成def
- 手动替换的步骤(偷懒的版本)
  - 查找：可用/?来处理,也可以用`*`来处理，用nN来跳转
  - cw`def`Esc，之后用n配合.来完成手动替换

### .范式

场景|执行一次|重复
---|---|---
行尾加;|A;Esc|j.j.j.
行内+前后加空格|f+s + |;.;.;.
手动替换|`*`cw123Esc|n.n.n.

最佳编辑模式就是`.范式`，就是用一键移动，另一键执行。
上面几个场景中，都是一键移动,执行用.

## vim普通模式

普通模式，也是最自然的模式。
很多普通模式的命令可以在前面加数字，表示命令被执行多少次。

### 停顿时请移开画笔

- 使用vim并不意味着一直在增删代码，更多时候是思考/阅读/代码跳转
- 不进入插入模式，也可以进行复制删除

### 把撤销单元切成块

- 在插入模式下进行换行，也可以用Esc o，好处是更小的撤销颗粒度
- 在插入模式下移动光标(方向键移动)会重置修改状态，会产生新的撤销块
  - 这点同样适用于.

### 构造可重复的修改

- vim对重复操作进行了优化，这点值得利用
- 养成习惯，会提高效率

"this is a test string",光标在g上，怎么删除一个单词

- dbx
- bdw
- daw(使用文本对象，而不是使用动作命令)

都是使用3次按键实现，从可重复性上看：

- dbx，.会重复x
- bdw，.会重复dw，但是光标在行尾，删不了单词
- daw，.会重复daw，完美的可重复性

### 用次数做简单的算术运算

- C-a/C-x分别对数字执行加法和减法，不带数字只加减1
- 光标在数字上或在数字之前都是可行的
- vim会将以0开头的数字解析为八进制
  - 可设置set nrformats= 来指定让vim以10进制解释

### 能够重复就别用次数

- 次数和重复各有优缺点

将"delete more than one word"改为"delete one word":

- d2w 删除两个单词
- 2dw 做两次(删除一个单词)
- dw. 删除一个单词，重复一次

从撤销的颗粒度看，dw.的颗粒度最小。

新需求：一次要删除3个单词;一次要删除4个单词;一次要删除5个单词...

dnw/ndw都需要正确的计数，而dw.方便很多，而且撤销很方便，按u就行。
如果要删除100个单词，按99次.也是扎心，所以`只在必要是使用次数`。

将"i have a couple of questions"改为"i have some more questions"

- c3wsome more ESC
- dw...isome more ESC

这时用次数顺手很多,u可以撤销整个修改。

### 双剑合璧，天下无敌

`操作符+动作命令是vim强大的基石`

eg: ___操作符 + 动作命令 = 操作___

d{motion}可以是dl/daw/dap，分别对应一个字符/一个完整单词/一个段落，
具体的范围有动作命令决定。

c{motion}/y{motion},包括前面的d，都被称为`操作符`。

vim语法只有一个额外规则：一个操作符连续被调用两次，只会作用于当前行。
dd 删除当前行，>>缩进当前行，gUgU/gUU(简化版)当前行大写。

操作符|用途
---|---
c|修改
d|删除
y|复制到寄存器
g~|大小写反转
gu|小写
gU|大写
`>`|增加缩进
`<`|减小缩进
=|自动缩进
!|使用外部程序过滤{motion}范围内的行

通过组合命令来提高威力

- 使用自定义操作符配合标准动作
- 使用标准操作符配合自定义动作

## 插入模式

大部分vim命令都是在非插入模式中执行的。
替换模式是插入模式的一种特例。

### 在插入模式中可即时更正错误

插入模式下如果输入的内容有错误，除了退格键(backspace),还有以下选择：

按键|用途
C-h|删除前一个字符，效果和退格键相同
C-w|删除前一个单词
C-u|删除至行首

### 返回普通模式

插入模式只专注输入，普通模式是大部分时间所处的模式。

Esc/C-[,都是切回普通模式。
C-o是在普通模式和插件模式的组合。

在插入模式下按下C-o，并接命令，执行完命令后会自动进入插入模式。
eg:C-ozz,插入模式下将屏幕滚动到中间。

### 不离开插入模式，粘贴寄存器中的文本

"hello yb, copy to here:"，将"hello yb."插入到行尾。光标在h上。

- yt,ACtrl-r0.

t{char}是动作，y是操作符，"yt,"表示复制hello yb

Ctrl-r0表示在插入模式下的粘贴

`<C-r>{register}` 当寄存器里存的是字符时，非常方便。
不过可能存在额外的缩进和换行，使用`<C-r><C-p>{register}`就没问题了。

### 随时随地做运算

大部分vim寄存器都是存文本的，但表达寄存器是可以执行一段脚本的，并返回结果。
我们可以当成一个计算器来使用。

- Ctrl-r=即可访问表达式寄存器

### 用字符编码插入不常用的字符

- 前提是知道某个字符的编码
- 在插入模式中 Ctrl-v{code}即可
- vim支持的字符编码是3位数(unicode用4个16进制表示，Ctrl-vu{code})
- Ctrl-vu00bf = ¿
- Ctrl-v065 = A
- 如果想知道某个字符的编码，`ga`即可知道

### 用digraph俩插入不常用的字符

- digraph是组合字符
- Ctrl-k{char1}{char2}

这个用来表示普通分数还是非常容易的

### 用替换模式替换已有的文本

- 普通模式下，R命令进入替换模式
- 替换模式下，Esc回到普通模式
- 替换模式会替换已有文本，除此之外，其他和插入模式一模一样
- 在某些显示列宽问题上，使用虚拟替换模式gR触发。
- 单次替换模式和单词虚拟替换模式r{char}/gr{char}

单次替换模式，会在替换一个字符后，回到普通模式

## 可视模式

vim有3种不同的可视模式，分别用于操作字符文本/行文本/块文本。

### 深入理解可视模式

- 可视模式和普通模式很类似，普通模式的命令在可视模式下做的事都是一样的
- 普通模式是先指定操作，后指定范围;可视模式先指定范围，再指定操作

将March改为April

- viw选中March
- c删除，然后输入April

- 在可视模式下，还可切换到选择模式
- 按Ctrl-g可切换可视模式和选择模式
- 选择模式，输入可见字符会删除选中的文本，并进入插入模式

### 选择高亮选区

- v，可以激活面向字符的可视模式
- V，可激活面向行的可视模式
- Ctrl-v，可激活面向列块的可视模式
- gv，重选上次的高亮选区(只要上次选区没有删除，就会正常工作)
- 可视模式按Esc退到普通模式
- 可视模式之间是可以相互覆盖的
- 可视模式下，按o，可切换选区的活动端(貌似这个非常强大)
