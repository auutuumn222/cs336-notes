# CS336 中文入门与复习笔记

依据 Stanford **CS336: Language Modeling from Scratch（Spring 2026）** 的课程安排，按课次整理中文学习资料，供新手理解概念或观看课程后复习。

内容以清晰的主线、连续例子和必要背景为重点，附关键结论、易错点与自测。它是个人整理的学习笔记，不是 Stanford 官方讲义或课程录像逐字稿。

## 课程资料

| 课次 | 主题 | 阅读版 | 可编辑源文件 |
| --- | --- | --- | --- |
| 01 | 课程概览与分词：从资源预算、文本表示到 BPE | [PDF](第01课/复习资料/main.pdf) | [LaTeX](第01课/复习资料/main.tex) |
| 02 | PyTorch 与资源核算：从张量、FLOPs 到显存与算术强度 | [PDF](第02课/复习资料/main.pdf) | [LaTeX](第02课/复习资料/main.tex) |
| 03 | 架构与超参数：从残差主干、RoPE 到注意力成本与训练稳定性 | [PDF](第03课/复习资料/main.pdf) | [LaTeX](第03课/复习资料/main.tex) |

第一课共 10 页，包含可点击目录、一页速查、9 道自测题及参考答案。首次阅读建议按正文顺序；快速复习可先看第 8 页速查，再做第 9 页自测。

第二课共 16 页，沿用第一课的版式、九章结构、一页速查、9 道自测与参考答案，用同一个小网络贯穿形状、存储、前向、反向和优化器核算，再连接算术强度、梯度累积与激活检查点。快速复习可先看第 13 页速查，再做第 14 页自测；具体资料覆盖范围见本课 README。

第三课共 20 页，继续沿用相同版式和复习结构，用一个小型 Transformer 串起归一化、SwiGLU、RoPE、尺寸配置、MHA / GQA / MQA 与训练稳定性。快速复习可先看第 16 页速查，再做第 17 页自测；第 18 页为答案，第 19–20 页列出资料来源、课件对应及纠正说明。

## 文件说明

- `第01课/复习资料/main.pdf`：可直接阅读的文档。
- `第01课/复习资料/main.tex`：完整中文 LaTeX 源文件。
- `第01课/复习资料/README.md`：本课结构、阅读与编译说明。
- `第01课/复习资料/检查记录.md`：数值复核与排版检查记录。
- `第02课/复习资料/`：第二课同样的 PDF、LaTeX、README 与检查记录。
- `第03课/复习资料/`：第三课同样的 PDF、LaTeX、README 与检查记录。

## 编译

源文件使用 `ctexart`，采用 UTF-8 编码和 Fandol 中文字体，没有外部图片依赖。使用安装了中文支持的 TeX Live / MiKTeX，或支持 XeLaTeX 的在线编辑器。

在源文件目录运行两次，以生成目录和交叉引用：

```sh
xelatex -interaction=nonstopmode -halt-on-error main.tex
xelatex -interaction=nonstopmode -halt-on-error main.tex
```

仓库仅包含文档，不附带编译器、视频、录音、模型或下载缓存。

## 官方来源

- [课程官网及 Schedule](https://cs336.stanford.edu/)
- [官方讲义仓库](https://github.com/stanford-cs336/lectures)
- [第一课官方讲义源码](https://github.com/stanford-cs336/lectures/blob/main/lecture_01.py)
- [第二课官方讲义源码](https://github.com/stanford-cs336/lectures/blob/main/lecture_02.py)
- [第三课官方课件](https://github.com/stanford-cs336/lectures/blob/main/lecture_03.pdf)

官方资料可能继续更新；本仓库按 Spring 2026 课程整理，具体来源及补充解释的边界见各课文末。
