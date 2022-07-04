

# \命令名{}

> `\documentclass[UTF8]{ctexart}`
>
> - documentclass指定了文档的类型
>   - article：普通的文章
>   - book
>   - report
>   - beamer:幻灯片
>   - ctexart:支持简体中文和英文的混排
> - [UTF8]：指定文档的编码类型



> `\begin{document}`
>
> **正文**
>
> `\end{document}`
>
> - 位于`\begin{document}`之前的内容称为前言（preamble）
>   - 前言：可以在此处设置文档的格式、页面的尺寸、文档中需要导入的宏包等



## **前言**

> `\title{文章的标题}`
>
> `\author{作者名}`
>
> `\date{文档的修改日期}`
>
> - 可配合使用`\today`命令自动生成当天的日期`\date{\today}`
>
> 需要在文档的正文区添加一个`\maketitle`命令，使得在当前位置生成文档的标题



> 基础的格式化命令
>
> `\textbf{内容}`：**加粗文字**
>
> `\textit{内容}`：*斜体文字*
>
> `underline{内容}`：<u>添加下划线</u>
>
> 重启一个新段落：输入2个换行符`\\`（单纯换行，无缩进）
>
> ​							  输入`\par`(自动换行加缩进)



## 开启新的**章节**

> `\section{这是第一个章节}`
>
> 第一章的正文部分
>
> `\subsection{这是一个二级子章节}`
>
> 二级子章节的正文
>
> `\subsubsection{这是一个三级子章节}`
>
> 三级子章节的正文
>
> `\section{这是第二个章节}`
>
> 第二章的正文部分



## 添加**图片**

> 需要在**前言**部分添加**包**  `\usepackage{graphicx}`
>
> 在正文部分使用`\includegraphics{图片名称}`命令在当前位置添加一张图片
>
> - 将图片嵌套在一个figure环境中
>
>   ```python
>   \begin{figure}[htbp]
>   \centering                                    		## 使得图片居中
>   \includegraphics[scale=0.2]{图片名称}
>   \caption{指定图片的标题}
>   \end{figure}
>   ```
>   
>   - `\begin{figure}......\end{figure}`:是固定用法，只要插入图片，就需要注明图片开始、结束位置。`[htbp]`内是控制参数，控制图片的位置。
>   - `centering`:表示将图片居中；
>   - `\includegraphics{}`:插入一张图片， `{}` 内为图片的名称， `[]` 内是控制参数，控制图片的显示大小；
>   - `\caption{}` ：指定图片的标题；
>   
> - 图片位置控制参数：`[htbp]`
>
>   - `[h]` :表示当前位置（here），将图片放在你设置的当前位置，但是如果这一页的空间不足以放下这个图片，此时图片会转到下一页；
>   - `[t]`: 顶端（top），优先将图片放置在页面的顶部；
>   - `[b]` :底部（bottom）,优先将图片放置在页面底部；
>   - `[p]` :将图片设置为浮动状态，系统会自动排版图片的位置；
>   - `[H]`:固定在当前相对位置。当排不下时不会将文字挤到其他位置。
>
> - 图片大小控制参数：`\includegraphics[scale=0.2]{图片名称}`
>
>   - `[scale]`:表示按原图比例缩放，如 `scale=0.2` 表示将原图缩小 5 倍，如果要放大只需要将 scale 设置为大于 1 即可；
>   - 还可以直接设置图片宽高，比如 `[height = 1cm, width = 2cm]`。
>
> - **并排插入图片**
>
>   - 首先需要在前言区添加宏包：`\usepackage{subfigure}`
>
>     ```java
>     \begin{figure}[htbp]
>     \centering
>     \subfigure
>     {
>         \begin{minipage}[b]{.3\linewidth}
>             \centering
>             \includegraphics[scale=0.1]{girl.eps}
>         \end{minipage}
>     }
>     \subfigure
>     {
>      	\begin{minipage}[b]{.3\linewidth}
>             \centering
>             \includegraphics[scale=0.1]{girl.eps}
>         \end{minipage}
>     }
>     \subfigure
>     {
>      	\begin{minipage}[b]{.3\linewidth}
>             \centering
>             \includegraphics[scale=0.1]{girl.eps}
>         \end{minipage}
>     }
>     \caption{figure title}
>     \end{figure}
>     ```
>
>   - `\subfigure{...}`:新的一张子图；
>
>     - 若想实现图片换行，只需在两个`subfigure`中间加一个空行即可。
>
>       ```java
>       \subfigure[]
>       \subfigure[]
>       ```
>
>   - `{.3\linewidth}`:`.3`表示该图片会占当期行的空间比例，一行想放3张图则每张占30%，需要根据一行放置图片数量来设置；
>
>   - 为每张图片添加单独的描述信息：`\subfigure[描述信息填在此处]`；
>
> - **多行多列排版**
>
>   - 图片分组显示【横排】：
>
>     ![Latex图片分组横排](E:/software/Typora/pictures/image-20220513112500998.png)
>
>     ```java
>     \begin{figure}[htbp]
>     \centering
>     \subfigure[figure 1]
>     {
>         \begin{minipage}[b]{.3\linewidth}
>             \centering
>             \includegraphics[scale=0.3]{Fig/Camphor tree DN.png}
>         \end{minipage}
>         \begin{minipage}[b]{.3\linewidth}
>             \centering
>             \includegraphics[scale=0.3]{Fig/Ceramic tile DN.png}
>         \end{minipage}
>         \begin{minipage}[b]{.3\linewidth}
>             \centering
>             \includegraphics[scale=0.3]{Fig/Ficus virens DN.png}
>         \end{minipage}
>     }
>     \subfigure[figure 2]
>     {
>      	\begin{minipage}[b]{.3\linewidth}
>             \centering
>             \includegraphics[scale=0.3]{Fig/Camphor tree RV.png}
>         \end{minipage}
>         \begin{minipage}[b]{.3\linewidth}
>             \centering
>             \includegraphics[scale=0.3]{Fig/Ceramic tile RV.png}
>         \end{minipage}
>         \begin{minipage}[b]{.3\linewidth}
>             \centering
>             \includegraphics[scale=0.3]{Fig/Ficus virens RV.png}
>         \end{minipage}
>     }
>     \caption{figure title}
>     \end{figure}
>     ```
>
>   - 图片分组显示【竖排】：
>
>     ![LaTex图片分组竖排](E:/software/Typora/pictures/image-20220513113108915.png)
>
>     ```java
>     \begin{figure}[htbp]
>     \centering
>     \subfigure[figure 1]
>     {
>         \begin{minipage}[b]{.3\linewidth}
>             \centering
>             \includegraphics[scale=0.3]{Fig/Camphor tree DN.png}  \\
>     	   \includegraphics[scale=0.3]{Fig/Camphor tree RV.png}
>         \end{minipage}
>     }
>     \subfigure[figure 2]
>     {
>     	\begin{minipage}[b]{.3\linewidth}
>             \centering
>             \includegraphics[scale=0.3]{Fig/Ceramic tile DN.png}  \\
>     	   \includegraphics[scale=0.3]{Fig/Ceramic tile RV.png}
>          \end{minipage}
>     }
>     \subfigure[figure3]
>     {
>     	\begin{minipage}[b]{.3\linewidth}
>             \centering
>             \includegraphics[scale=0.3]{Fig/Ficus virens DN.png}  \\
>             \includegraphics[scale=0.3]{Fig/Ficus virens RV.png}
>          \end{minipage}
>     }
>     \caption{figure title}
>     \end{figure}
>     ```
>
>     - `subfigure` 没有换行功能，所以要实现一个 subfigure 中包含多张垂直排版的图片，可以在 `minipage` 中使用多个 `\includegraphics`，在除了最后一个之外的后面都加上换行符 `\\` 即可。
>
> - 其他细节
>
>   - **图标题格式**：
>
>     - 论文里可能要求图片标题为 `Fig. 1.`，而有的模版生成出来的是 `Figure. 1.`，此时只需要在 `\begin{document}` 后面放上 caption 的格式控制命令：
>
>       ```JAVA
>       \begin{document}\sloppy
>       \captionsetup[figure]{labelfont={bf},name={Fig.},labelsep=period}
>       ```
>
>     - `bf` 表示加粗，`name` 是要显示的名字，`labelsep` 是名称和序号之间的分隔符，`period` 表示用句号分隔，`space` 表示用空格分隔，没有参数就默认使用冒号分隔。
>
>     - 对于表格也同理，修改自己需要的 `name` 即可
>
>       ```JAVA
>       \captionsetup[table]{labelfont={bf},name={Table},labelsep=period}
>       ```
>
>       



## 添加**列表**

> ```python
>## 无序列表
> \begin{itemize}
> \item 列表项1
> \item 列表项2
> \item 列表项3
> \end{itemize}
> 
> ## 有序列表
> \begin{enumerate}
> \item 列表项1
> \item 列表项2
> \item 列表项3
> \end{enumerate}
> ```



## **数学公式**

> `$E=mc^2$`:行内公式可以直接以`$...$`来书写$E=mc^2$
>
> 若想将公式单独一行显示
>
> ```python 
>\begin{equation}
> E=mc^2
> \end{equation}
> 
> ## 可以简写为
> \[
> E=mc^2
> \]
> ```
> 
> [在线LaTeX公式编辑器](https://latex.codecogs.com/eqneditor/editor.php)



## **表格**

> ```python
>\begin{tabular}{c c c}
> 单元格1 & 单元格2 & 单元格3 \\
> 单元格4 & 单元格5 & 单元格6 \\
> 单元格7 & 单元格8 & 单元格9 \\
> \end{tabular}
> ```
> 
> - {c c c} ： 表示表格共有3列，居中对齐
>- {l c c} : 左对齐
> - {r c c} ： 右对齐
> - `&` : 每一列数据需要以`&`分割
> - `\\` ： 每一行数据需要以`\\`分割
> - `{|c|c|c|}` : 加入`|`为表格添加竖直的边框
> - `\hline` : 水平方向的边框
> 
> ```python
>\begin{tabular}{|c|c|c|}
> \hline
> 单元格1 & 单元格2 & 单元格3 \\
> \hline
> 单元格4 & 单元格5 & 单元格6 \\
> \hline
> 单元格7 & 单元格8 & 单元格9 \\
> \hline
> \end{tabular}
> ```
> 
> 给表格添加**标题**（和图片类似），先将整个表格放入一个`table`环境中
>
> ```python
>\begin{table}
> \center											## 将表格居中显示
> \begin{tabular}{|c|c|c|}
> \hline
> 单元格1 & 单元格2 & 单元格3 \\
> \hline
> 单元格4 & 单元格5 & 单元格6 \\
> \hline
> 单元格7 & 单元格8 & 单元格9 \\
> \hline
> \end{tabular}
> \caption{输入表格的标题}							## 指定表格的标题
> \end{table}
> ```



## 下载Latex模板

**可直接跳转至第4步**：[开始按步骤寻找目标模板](https://template-selector.ieee.org/secure/templateSelector/publicationType)

- 【1】`Browse`→`Journals&Magazines`![image-20220524093912258](E:/software/Typora/pictures/image-20220524093912258.png)

- 【2】搜索目标期刊

  ![image-20220524094439376](E:/software/Typora/pictures/image-20220524094439376.png)

- 【3】`Author Center`→`JOURNAL AUTHORS`→`Create Your IEEE Journal Article`→`IEEE Article Templates`

  ![Author Center](E:/software/Typora/pictures/image-20220524094539720.png)

  ![image-20220524094747703](E:/software/Typora/pictures/image-20220524094747703.png)

  ![image-20220524095153632](E:/software/Typora/pictures/image-20220524095153632.png)

- 【4】根据提示选择相应的模板

  ![image-20220524095258201](E:/software/Typora/pictures/image-20220524095258201.png)

# 查阅资料

[一份不太简短的LaTeX介绍](E:\资料文档\一份不太简短的LaTeX介绍.pdf)

- [github](https://github.com/CTeX-org/lshort-zh-cn)
- 