---
title: V1
slug: ppdbiq1ogx9r8igg
url: https://www.yuque.com/caogongzi/pu6995/ppdbiq1ogx9r8igg
---

总结我上传的word的批注

<font style="color:rgb(61, 61, 58);background-color:rgba(240, 238, 230, 0.9);">很好，正文段落格式见我现在上传的图片。 吸收它  
</font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);">很好，现在要求你基于上面的所有技术报告格式要求，为我提供LaTeX代码模板，我只需要在模板项目文件中正稿部分添加文字，而不需要关心格式问题</font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);">我打算把LaTeX格式代码放在overleaf里面运行。所以需要考虑到overleaf的一些局限性（例如一些字体，overleaf并不包含），所以你需要适当修改一下你的代码以确保能在overleaf中成功运行你的模板代码</font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);"></font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);"></font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);">很好，能否在此基础上， 再做如下修改： 主文档为main.tex文件（编译这个文件）； 格式都放在format.tex; 正文放在content.tex，在这个里面，只包含—— </font>_<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);">%====================正文部分开始====================</font>_<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);"> 到—— </font>_<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);">%====================正文部分结束====================</font>_<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);"> 之间的内容，其他的都不用管，不用包含。</font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);"></font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);"></font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);">我上传了一些字体，放在fonts文件夹下，具体哪些字体你可以通过图片看到，你可以根据你的经验，将之前的字体（默认字体）中对应部分更换成我上传的字体中的对应字体。然后相应修改format.tex文件</font>

![](https://cdn.nlark.com/yuque/0/2025/png/2408029/1751007648368-a22e3367-30ca-4327-bb74-aabb99be22d9.png)

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);"></font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);">很好，针对这个Overleaf文件夹（内含三个文件：format.tex，main.tex和content.tex），撰写一个使用说明</font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);"></font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);"></font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);">main.tex参照下面格式改写： % main.tex - 主文件（正文） % !TeX encoding = UTF-8 \documentclass[12pt,a4paper,fontset=none]{ctexart} % 引入格式文件 \input{format} \begin{document} % 生成封面 \makecover % 生成目录 \tableofcontents \newpage % 引入正文内容 \input{content} \end{document}</font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);">format.tex参照下面格式改写(当然具体内容你得替换）： % format.tex - 格式设置文件 % !TeX encoding = UTF-8 % 字体设置 \usepackage{fontspec} \usepackage{xeCJK} % 设置中文字体 \setCJKmainfont{SimSun.ttc}[ Path=fonts/, AutoFakeBold=2.17, ItalicFont=Kaiti.ttf ] \setCJKsansfont{SimHei.ttf}[Path=fonts/,AutoFakeBold=2.17] \setCJKmonofont{FangSong.ttf}[Path=fonts/] % 设置字体族 \setCJKfamilyfont{zhsong}{SimSun.ttc}[Path=fonts/,AutoFakeBold=2.17] \setCJKfamilyfont{zhhei}{SimHei.ttf}[Path=fonts/,AutoFakeBold=2.17] \setCJKfamilyfont{zhkai}{Kaiti.ttf}[Path=fonts/] \setCJKfamilyfont{zhfs}{FangSong.ttf}[Path=fonts/] % 定义字体命令 \newcommand{\songti}{\CJKfamily{zhsong}} \newcommand{\heiti}{\CJKfamily{zhhei}} \newcommand{\kaishu}{\CJKfamily{zhkai}} \newcommand{\fangsong}{\CJKfamily{zhfs}} % 设置英文字体 \setmainfont{TimesNewRoman.ttf}[ Path=fonts/, BoldFont=TimesNewRomanBold.ttf, ItalicFont=TimesNewRomanItalic.ttf, BoldItalicFont=TimesNewRomanBoldItalic.ttf ] % 页面设置 \usepackage{geometry} \geometry{ left=3cm, right=2.5cm, top=2.5cm, bottom=2.5cm } % 必要的包 \usepackage{graphicx} \usepackage{array} \usepackage{booktabs} \usepackage{fancyhdr} \usepackage{titlesec} \usepackage{titletoc} \usepackage{hyperref} \usepackage{indentfirst} \usepackage{caption} % 字号定义 \newcommand{\erhao}{\fontsize{22pt}{\baselineskip}\selectfont} \newcommand{\sanhao}{\fontsize{16pt}{\baselineskip}\selectfont} \newcommand{\sihao}{\fontsize{14pt}{\baselineskip}\selectfont} \newcommand{\xiaosi}{\fontsize{12pt}{\baselineskip}\selectfont} % 正文格式设置 \setlength{\parindent}{2em} \setlength{\parskip}{0pt} \linespread{1.2} \raggedbottom % 标题格式设置 \titleformat{\section} {\centering\heiti\sanhao} {\thesection} {1em} {} \titlespacing{\section}{0pt}{0pt}{0pt} \titleformat{\subsection} {\songti\sanhao} {\thesubsection} {1em} {} \titlespacing{\subsection}{0pt}{0pt}{0pt} \titleformat{\subsubsection} {\songti\sihao} {\thesubsubsection} {1em} {} \titlespacing{\subsubsection}{0pt}{0pt}{0pt} % 目录格式设置 \titlecontents{section} [0pt] {\songti\sihao} {\thecontentslabel\quad} {} {\titlerule*[0.5pc]{.}\contentspage}</font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);">\titlecontents{subsection} [2em] {\songti\sihao} {\thecontentslabel\quad} {} {\titlerule</font>_<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);">[0.5pc]{.}\contentspage} % 图表标题格式 \captionsetup{ font={small,singlespacing}, labelfont={bf}, labelsep=quad, skip=6pt } \captionsetup[figure]{name={图}} \captionsetup[table]{name={表}} % 页眉页脚 \pagestyle{fancy} \fancyhf{} \fancyfoot[C]{\thepage} \renewcommand{\headrulewidth}{0pt} % 自定义命令 \newcommand{\coveritem}[1]{ \makebox[4cm][s]{#1}：\underline{\makebox[8cm]{}} } % 封面命令 \newcommand{\makecover}{ \thispagestyle{empty} \begin{center} \vspace</font>_<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);">{2cm} {\songti\erhao\textbf{渔船智能化改造（标准版）}}\\[1.5cm] {\songti\erhao\textbf{技术总结报告}}\\[4cm]</font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);">\begin{flushleft} \hspace{2cm}\coveritem{编制}\\[0.8cm] \hspace{2cm}\coveritem{校对}\\[0.8cm] \hspace{2cm}\coveritem{审核}\\[0.8cm] \hspace{2cm}\coveritem{审批}\\[3cm] \end{flushleft}</font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);">% \includegraphics[width=10cm]{logo.png}\\[2cm]</font>

<font style="color:rgb(20, 20, 19);background-color:rgb(240, 238, 230);">{\songti\sanhao 2025年4月} \end{center} \newpage } % 目录页命令 \renewcommand{\contentsname}{\heiti\sihao 目\quad 录} % 设置默认字体 \AtBeginDocument{ \songti\sihao }</font>


