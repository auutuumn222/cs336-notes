# 第 03 课复习资料：架构与超参数

课程版本：Stanford CS336 / Spring 2026。原课日期：2026-04-06。整理日期：2026-10-23。

`main.tex` 是完整中文 LaTeX 源文件；`main.pdf` 是由同一源文件编译的阅读版。

阅读版共 **20 页**：第 1 页为可点击目录，第 16 页为一页速查，第 17 页为 9 道自测，第 18 页为参考答案，第 19–20 页为来源、课件覆盖对应和版本说明。已逐页渲染检查。

沿用第一课的 `ctexart`、A4、22 mm 页边距、Fandol 字体、字号、行距、蓝色标题、页眉页脚、表格、代码框和一级章节目录。标题下不添加课程年份和日期行。保留九章结构、一页速查、9 道自测和逐题答案，按第三课内容量安排篇幅。

## 主线

给定数据、计算预算和推理需求，怎样选择一个有效、经济且稳定的 Transformer？

1. 接上前两课：从 token 与资源核算，走到完整预测链路。
2. 搭好主干：归一化位置、LayerNorm / RMSNorm、SwiGLU、串行与并行残差块。
3. 编入位置：对比位置编码，推导 RoPE 的相对位置性质，说明缓存中的位置约定。
4. 分配预算：FFN 宽度、头维度、深宽比、词表及共享权重的取舍。
5. 控制推理成本：区分参数、KV 容量和累计搬运，理解 MHA / GQA / MQA、窗口及混合注意力。
6. 稳定训练：连接 dropout、weight decay、稳定 softmax、z-loss、QK-norm 与 soft-capping。
7. 通过一页速查、自测和答案完成复习。

连续例子固定 `d=24, L=2, V=64, B=2, n=4, h_q=6, d_h=4`，SwiGLU 宽度为 64。它贯穿形状、FFN 预算、总参数和三种 KV 缓存核算；两维归一化、旋转与 softmax 小例子另行注明假设。

首次阅读按正文顺序；快速复习先看第 16 页；检验掌握程度时先独立完成第 17 页。

## 编辑与编译

源文件采用 UTF-8、`ctexart` 和 Fandol 中文字体，无外部图片或下载资料依赖。可使用安装了中文支持的 TeX Live / MiKTeX，或支持 XeLaTeX 的在线编辑器。运行两次以生成目录和交叉引用：

```powershell
xelatex -interaction=nonstopmode -halt-on-error main.tex
xelatex -interaction=nonstopmode -halt-on-error main.tex
```

也可使用 Tectonic 自动完成必要的重复运行。本目录只保留 PDF、LaTeX、README 和检查记录。

## 官方来源与版本

- [课程主页及 Schedule](https://cs336.stanford.edu/)：第三课 Architectures, hyperparameters，Tatsu Hashimoto。
- [第三课官方课件](https://github.com/stanford-cs336/lectures/blob/main/lecture_03.pdf)：完整读取 67 页文字，并查看各页图表；本次下载文件为 5990936 byte。

课件原始字节的版本指纹：

```text
SHA-256: 20a953541b41a099bc69b8d809139a4ccdb2aefedaa13e50c766245fe4b5167e
Git blob SHA-1: 742ae35a3738a58f176f7b76695d91d278676e1e
```

主分支可能更新；上述指纹标识本次读取的文件。课程安排按 Spring 2026，模型实例按该版本课件理解，不当作当前完整模型清单。

## 覆盖对应

| 官方课件页码 | 主体内容 | 笔记位置 |
| --- | --- | --- |
| 1–9 | Transformer 回顾、模型对照与本课问题 | 第 1 节 |
| 10–19 | Pre / Post-norm、分支归一化、RMSNorm、去偏置 | 第 2 节 |
| 20–29 | 激活、门控 FFN、串行与并行 | 第 2 节 |
| 30–35 | 位置编码与 RoPE | 第 3 节 |
| 36–47、51 | FFN、头维度、深宽比、词表 | 第 4 节 |
| 48–50、52–56 | 正则化、优化动态、数值稳定 | 第 6 节 |
| 57–66 | KV cache、MQA / GQA、稀疏和混合注意力 | 第 5 节 |
| 67 | 共性、差异与总结 | 第 7 节 |

## 配套论文及阅读边界

核对了下列原始资料中与本课直接相关的机制、公式、实验与图表；不是对全部论文的逐页综述：

- 基础与归一化：[Attention Is All You Need](https://arxiv.org/abs/1706.03762)、[On Layer Normalization in the Transformer Architecture](https://arxiv.org/abs/2002.04745)、[Transformers without Tears](https://arxiv.org/abs/1910.05895)、[RMSNorm](https://arxiv.org/abs/1910.07467)、[Data Movement Is All You Need](https://arxiv.org/abs/2007.00072)。
- 门控与位置：[GLU Variants Improve Transformer](https://arxiv.org/abs/2002.05202)、[Do Transformer Modifications Transfer Across Implementations and Applications?](https://arxiv.org/abs/2102.11972)、[RoFormer](https://arxiv.org/abs/2104.09864)。
- 形状与预算：[Scaling Laws for Neural Language Models](https://arxiv.org/abs/2001.08361)、[Scale Efficiently](https://arxiv.org/abs/2109.10686)、[T5](https://arxiv.org/abs/1910.10683)、[PaLM](https://arxiv.org/abs/2204.02311)。
- 推理与稀疏：[Fast Transformer Decoding](https://arxiv.org/abs/1911.02150)、[GQA](https://arxiv.org/abs/2305.13245)、[Sparse Transformers](https://arxiv.org/abs/1904.10509)。
- 优化与稳定：[Why Do We Need Weight Decay in Modern Deep Learning?](https://arxiv.org/abs/2310.04415)、[Gemma 2](https://arxiv.org/abs/2408.00118)、[2 OLMo 2 Furious](https://arxiv.org/abs/2501.00656)。

笔记补全了课件 RMSNorm 中容易遗漏的维度平均，核对了 PaLM 头维度表的比例错误，使用完整的串行残差表达式，并澄清 MQA 的“一个维度”、算术强度的累计口径以及模型名单与具体机制的区别。具体纠正见文末与检查记录。

小型模型、手算、雅可比解释、窗口感受野和自测题属于辅助推导。资料不是课堂录像逐字稿，不声称观看完整录像或精读所有被展示的模型报告。MLA、SSM 等按课件作为后续课程入口，未扩展成第四课专题。模型配置中的经验范围不作为普遍数学约束。
