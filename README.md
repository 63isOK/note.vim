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
  - [重复执行面向行的可视命令](#重复执行面向行的可视命令)
  - [只要可能，最好用操作符命令，而不是可视命令](#只要可能最好用操作符命令而不是可视命令)
  - [编辑表格使用面向列的可视](#编辑表格使用面向列的可视)
  - [修改列文本](#修改列文本)
  - [在长短不一的高亮块后添加文本](#在长短不一的高亮块后添加文本)
- [命令行模式](#命令行模式)
  - [认识vim的命令行模式](#认识vim的命令行模式)
  - [在一行或多个连续行执行命令](#在一行或多个连续行执行命令)
  - [复制和移动行](#复制和移动行)
  - [在指定范围执行普通模式命令](#在指定范围执行普通模式命令)
  - [重复上次的ex命令](#重复上次的ex命令)
  - [自动补全ex命令](#自动补全ex命令)
  - [把当前单词插入到命令行](#把当前单词插入到命令行)
  - [回溯历史命令](#回溯历史命令)
  - [运行shell命令](#运行shell命令)
- [用缓冲区列表管理打开的文件](#用缓冲区列表管理打开的文件)
  - [用参数列表将缓冲区分组](#用参数列表将缓冲区分组)
  - [管理隐藏缓冲区](#管理隐藏缓冲区)
  - [将工作区切分成窗口](#将工作区切分成窗口)
  - [用标签页来将窗口分组](#用标签页来将窗口分组)

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

### 重复执行面向行的可视命令

- 可视模式下执行完一条命令后，会返回普通模式

将下面的文字按章节缩进,光标在1处

    1
    2
    2.1 缩进一次
    2.2
    3
    3.1.1 缩进两次
    3.1.2
    4

- `jjVj>jj>gv>`, 里面的gv表示重选上次的选区
- `jjVj>jj..`
- 与其手动选择相同范围的内容(gv)，还不如使用.
- 可视模式下,`.`操作的范围和上次高亮选中的文本数量相同(这个例子中是2行)

### 只要可能，最好用操作符命令，而不是可视命令

接着上面的继续分析

(100) (200) (1000)将括号里的数都加1，不考虑光标移动的情况下，使用.范式

- Ctrl-a . . 才是正确结果
- v Ctrl-a . . 的结果是(101) (201) (1010)

因为v模式面向字符，. = 3个数字对应值的增加1

所以：`文本范围结构很难用动作命令确定范围使用可视模式，其他重复性的场景用操作符`

### 编辑表格使用面向列的可视

将下面的数据改为下方的表格模样

    1         2
    awfawef   awf
    we        fe
    waf       wfe

    1      |2
    ---------
    awfawef|awf
    we     |fe
    waf    |wfe

- 光标在12之间的空格处, `Ctrl-vjjjx..gvr|`
- 光标移到12这行，yypVr-

可以看出在处理类似表格的场景，面向列的可视模式非常有用。

### 修改列文本

将下方的目录aa替换成b

    ~/aa/aa.txt
    ~/aa/aa.txt
    ~/aa/aa.txt
    ~/aa/aa.txt

如果光标在第一行的第一个a处： `Ctrl-vjjjecbbEsc`

按完Esc之后,选中的都会变成bb。这其中，c会触发插入模式，时间非常短

### 在长短不一的高亮块后添加文本

在下列3行后添加;

    a=1
    bb=false
    cc=3.1

- A;Escj.j. 这是之前学习过的.范式解决法
- Ctrl-vjj$A;Esc 用$告诉vim，选中块除了是方块，还能是不规则的区域

ps:可视模式中，ia有个数意义(被当成文本对象的组成部分)，
IA依然是选中块的首行行首和尾行行尾,所以可视模式要进入插入模式可选IA。

## 命令行模式

vim的老爸是vi，
vi的老爸是ex，
ex的老爸是ed。

### 认识vim的命令行模式

- : 会进入命令行模式
- 在命令行模式，我们可以输入ex命令/查找模式/表达式
- 按回车CR开始执行
- Esc回到普通模式
- 按/?(查找)或Ctrl-r=(表达式寄存器)都会激活命令行模式

在某些场景，命令行模式比普通模式强大很多，
具体原因是命令行模式影响范围更大(不需要考虑光标位置)，且一次执行可修改多行。

### 在一行或多个连续行执行命令

- ex命令的优点之一：可在范围内的所有行上执行
- 范围可用行号/位置标记/查找模式来指定
- 如果ex命令只是一个数字，vim会跳转到指定行
- :$ 表示跳到文件尾行,普通模式下是G
- :p是:print的缩写
- :3p，vim会先将光标移到第3行，然后显示行内容
- :3d和普通模式下的3Gdd效果相同
- :3,5p 打印第3行到第5行。 :{start},{end}是一个范围
- .,$p 打印当前行到文件尾。 此时的.表示当前行
- :%s/a/b 将每行第一个a替换成b，这个命令具体后面会用单章说明
- 高亮区也可指定范围
- 用模式/偏移都可指定范围
- :.,+3p 打印范围是当前行到下3行
- %表示整个文件，也是1,$的简写，:%p打印整个文件
- 0虚拟行，位于文件第一行上方，在复制移动是有用

### 复制和移动行

- :copy 把一行或多行赋值到另一个地方，:t是简写,:co也是简写
- :move 把一行或多行移动到另一个地方，:m是简写
- :6copy. 把第6行复制到当前行下方, :6t.也是一个意思
- :t6 当前行复制到第6行下方
- :t. 相当于yyp, yyp会使用寄存器，:t.则不会
- :t$ 当前行复制到文件尾
- :t0 当前行复制到文件头
- 对于高亮区，dGp表示移动到文件尾，使用m$也是一样的效果
- 重复上次的ex命令，按@:即可

### 在指定范围执行普通模式命令

- :normal 将普通模式的命令带到了命令行模式
- :范围normal. 表示对于范围内的所有行，对其执行普通模式下的.命令
- :%normal A; 整个文件的每一行行尾都增加一个分号
- ex命令的特点是"范围大，多行执行"，配合normal，可直接利用普通模式命令的优势

### 重复上次的ex命令

- 普通模式下的重复是.
- 命令行模式下的重复是@:
- .不会重复命令行模式下的修改
- @: @@ 都是可以重复任意次的
- 重复的过程中如果执行多了，可用Ctrl-o来撤销执行,部分命令需要用u来撤销

### 自动补全ex命令

- tab可以补全，反向使用Shift-tab
- Ctrl-d可显示补全列表

### 把当前单词插入到命令行

- Ctrl-rCtrl-w ,这个组合键会复制光标下的单词并把她插入到命令行
- `*`就表示/Ctrl-rCtrl-w CR 这是一个查找模式的简写
- 在替换:substitute(简写是:s)中，这个组合命令也可大大提高效率
- Ctrl-rCtrl-a ，这个组合键会复制字串(字串不是行,按空格分割的串)

### 回溯历史命令

vim提供两种回溯命令的方法：

- 方向键
- 调出命令行窗口

方向键情况：命令行已输入的字符会作为匹配项。
命令行模式或/查找模式，都可以利用方向键进行回溯。
Ctrl-p/Ctrp-n也可以完成方向键的功能，只是不能过滤。(可映射)

- q: 在普通模式下调出命令行历史窗口，里面是历史命令
- q/ 在普通模式下调出查历史找窗口，里面是查找命令
- Ctrl-f 在命令行模式下调出命令历史行窗口/查找历史窗口

### 运行shell命令

- !{cmd}，就可以调用外部命令了
- :shell 会启动一个交互式的shell会话,在shell中输入exit返回vim

对于vim内部启用交互式的shell，就不如这样的流程方便了：

- Ctrl-z 将vim挂起
- 在shell中工作，使用jobs查看任务列表
- fg 唤醒一个被挂起的任务

- :read !{cmd} 会将cmd的输出作为输入读到当前缓冲区
- :write !{cmd} 将当前缓冲区的内容指定为cmd命令的输入
  - :write! 文件名 是将缓冲写道文件里，!表示覆盖写
  - :write !cmd 或者 :write ! cmd 都是将当前缓冲作为cmd的输入
- 范围!{cmd} 是将范围缓冲作为cmd的输入，cmd执行的输出会覆盖范围内的内容
- 普通模式下 !{motion}会自动切换到命令行模式，并设置好范围,节省了部分操作

## 用缓冲区列表管理打开的文件

文件存储在磁盘，缓冲区存在内存中，vim编辑的是缓冲区。
保存操作执行后，文件和缓冲区内容一致，vim再次编辑，内容又不一样了，直到下次保存。

vim大部分命令都是针对缓冲区的，针对文件的也是有的(:write :update :saveas)

- :ls 可查看所有缓冲区列表
- :bn(ext)跳到下一个 :bp 跳到上一个
- :bf(irst)跳到第一个 :bl(ast)跳到最后一个
- :b(uffer) N, 跳到第N个缓冲 或是:b 路径唯一标识符 或是:b tab补全或选择
- :bufdo 可对:ls的所有缓冲执行ex命令，不过不推荐，作者更加推荐:argdo
- :bd(elete) 删除缓冲区 后面可接多个N，或是在前面确定范围

### 用参数列表将缓冲区分组

参数列表是vi的产物，缓冲区是vim的产物

- :args 显示参数列表
- :n(ext) :prev 分别对应跳转
- :argdo 在每个参数列表中的文件执行一条ex命令
- 参数列表支持glob模式来指定文件
- 还支持命令填充 :args `cmd` 不过这个命令不支持ls | grep 这种通道格式
- :args `cat xx.file` 如果xx.file记录的文件名，那么可以很方便从众多文件中打开指定的那组
  - 前提是xx.file要提前准备好，在某些场景还是非常有必要的

### 管理隐藏缓冲区

- 缓冲区修改后并未保存，切到其他缓冲区，会有警告
- :bn! 在后面加!表示强制执行，此时之前修改的缓冲区会变成隐藏缓冲区
- 退出vim会话时，还有隐藏缓冲区，会有提示，此时 :edit! 会强制从文件读，然后进行覆盖
- 不想检查隐藏缓冲区而退出vim会话 :qall!
- 要保持所有隐藏缓冲区 :wall

缺省情况下，:bn! 是不能切到其他缓冲区的，这是有好处的，除了下面这种场景：

:argdo :bufdo

所以要启用隐藏缓冲区的功能， vimrc中 set hid = true

### 将工作区切分成窗口

窗口可并排显示多个缓冲区。窗口是缓冲区的显示区域。

窗口的创建

- Ctrl-ws 水平切
- Ctrl-wv 垂直切
- 切完之后，两个窗口显示的内容是一样的，对长文件来说，非常有用
- :edit {文件名} 会将某个缓冲区载入窗口
- Ctrl-ws 和:edit 的简写是:sp(lit){文件名}
- Ctrl-wv 和:edit 的简写是:vsp(lit){文件名}

窗口的切换

- Ctrl-ww 和Ctrl-wCtrl-w是在窗口间循环切换
- Ctrl- h/j/k/l 窗口间自由切换
- Ctrl-wc 关闭活动窗口 :clo(se) 也是同样功能
- Ctrl-wo 只保留活动窗口 :on(ly) 也是同样功能

窗口大小的改变

- Ctrl-w = 所有窗口大小都设置一样
- Ctrl-w _ 最大化活动窗口的高度
- Ctrl-w | 最大化活动窗口的宽度
- n Ctrl-w _ 高度设为n行
- n Ctrl-w | 宽度设为n列

其他命令(窗口移动等)查看文档

### 用标签页来将窗口分组

标签页就是窗口的容器。

- :lcd {path} 设置本窗口的本地工作目录
- :windo lcd {path} 为一个标签页的所有窗口设置本地工作目录
- 好处是：不同的标签页对应不同的工程，互不干扰

标签的打开/关闭

- :tabedit {文件名} 新建标签，不带文件名就创建一个新标签，里面有个空缓冲区
- Ctrl-wT 将当前窗口丢到一个新标签中
- 标签页只有一个窗口时 :close 关闭窗口和标签页
- :tabclose 关闭标签页
- :tabonly 除当前标签页外全部关闭

标签页的切换

- 每个标签页都有一个编号，从1开始
- :tabn(ext) {N} 跳到指定标签页，不带编码，就跳到下一个
- :tabp(revious) 跳到上一个标签页
- 普通模式下的命令 {N}gt 跳到指定标签页，不带编号，跳到下一个
- 普通模式下的命令 gT 跳到上一个标签页

标签页的重排

- :tabmove {N} 如果N为0,表示当前标签页会移到开头
- 如果N不省略，则把当前标签页丢到结尾去
