# 第 02 课复习资料：PyTorch 与资源核算

课程版本：Stanford CS336 / Spring 2026。原课日期：2026-04-01。整理日期：2026-10-23。

入口：`main.tex` 是可编辑的完整中文 LaTeX 源文件；`main.pdf` 是由同一源文件编译的阅读版。

当前阅读版共 **16 页**：第 1 页为可点击目录，第 13 页为一页速查，第 14 页为 9 道自测，第 15 页为参考答案，第 16 页为来源与版本说明。已逐页渲染检查。

沿用第 01 课的 `ctexart`、A4 纸张、22 mm 页边距、Fandol 字体、字号、行距、蓝色标题、页眉页脚、表格、代码框与可点击一级章节目录；标题下不添加课程年份和日期行。保留九章结构、一页速查、9 道自测与逐题参考答案，按第二课的内容量安排篇幅。

## 主线

文本已经变成张量，怎样判断模型装不装得下、要算多久、为什么跑得慢？

1. 从第一课的表示问题接到显存容量、总 FLOPs、吞吐率三本账。
2. 从 shape、dtype、device 计算张量大小，理解混合精度的取舍。
3. 用 einops 写清轴的含义，从形状读懂运算。
4. 用同一个小网络串起前向、反向、参数量、`6NT` 与训练时间估算。
5. 结合搬运字节数、算术强度和 roofline 判断计算与带宽瓶颈。
6. 补齐优化器与激活开销，比较梯度累积和激活检查点。
7. 用一页速查、自测题和参考答案完成复习。

首次阅读按正文顺序；快速复习先读“一页速查”；检验掌握程度时先独立完成自测题。

## 编辑与编译

源文件采用 `ctexart`，UTF-8 编码与 Fandol 中文字体，无外部图片或本地原始资料依赖。可用安装了中文支持的 TeX Live / MiKTeX，或支持 XeLaTeX 的在线 LaTeX 编辑器编译；目录和交叉引用需要编译两次。

```powershell
xelatex -interaction=nonstopmode -halt-on-error main.tex
xelatex -interaction=nonstopmode -halt-on-error main.tex
```

也可用 Tectonic 编译，它会自动完成必要的重复运行。PDF 可直接阅读，本课目录仅保留文档，不附带编译器、录像、原始资料下载快照或缓存。

## 来源和边界

核心资料已完整读取并交叉对照：

- [课程主页及 Schedule](https://cs336.stanford.edu/)：Spring 2026 第二课，PyTorch（einops）、资源核算（FLOPs、显存、算术强度）。
- [正式讲义源码](https://github.com/stanford-cs336/lectures/blob/main/lecture_02.py)：本次读取文件的 Git blob SHA（由原始字节独立计算）为 `38171e8cb9656e4086b3f166b3dd5ce8b5731321`。
- [正式在线讲义](https://cs336.stanford.edu/lectures/?trace=lecture_02)：读取完整代码与 955 条执行步骤。
- [录像版在线讲义](https://cs336.stanford.edu/lectures/?trace=lecture_02_recording)：读取完整代码与 992 条执行步骤，包括其中 241 个文本、图片或链接呈现项。
- [facts.py](https://github.com/stanford-cs336/lectures/blob/main/facts.py)、[gpu_util.py](https://github.com/stanford-cs336/lectures/blob/main/gpu_util.py)：核对硬件常量、设备选择与显存测量口径。

执行快照 SHA-256：

```text
正式版：dea0edc07279ec3469c8d9f2e48165d5c0eca362c82b3243b5c33bc829505486
录像版：90a7c53791b2317d945a4126520f518131131ae11e2f9019c863f5ec9d26504e
```

原始源码、部署的正式版执行快照与录像版并非完全同步。特别核对了录像版显存单位的重复乘法、H100/B100 名称、周数与重算复杂度等差异；部署的正式版梯度累积例子也曾设 `B=64` 而 `micro_batch_size=256`，本次主分支已改为 `B/4`。笔记统一按可推导的公式和明确单位书写，检查记录列出具体口径。

配套核对了 einops 教程、PyTorch AMP 与 checkpoint 文档，以及混合精度、FP8、NVFP4、AdaGrad 和 roofline 中与本课直接相关的机制。不是对这些论文的完整综述，也不把后续课程的 Transformer 架构、分布式实现和作业答案扩展为本课要求。

本资料不是录像逐字稿；读取“录像版讲义”不等于观看完整课堂视频。讲义附带的 Transformer FLOPs 延伸文章已读取；Transformer 显存延伸链接 `https://erees.dev/transformer-memory/` 在本次访问时返回 404，未用其不可访问内容作依据。模型案例、训练动态、课程行政链接属于背景入口，不影响第二课主体内容的完整覆盖。

## 覆盖对应

| 官方讲义主体 | 笔记位置 |
| --- | --- |
| motivating_questions | 第 1、4、6 节，回收训练时间和最大模型两个开场问题 |
| tensors_basics / tensors_memory / tensors_on_gpus | 第 2 节，张量、精度、AMP、GPU 与测量口径 |
| tensor_einops | 第 3 节，einsum、reduce、rearrange |
| tensor_operations_flops / benchmark | 第 4 节，矩阵乘法、峰值、MFU、异步计时 |
| arithmetic_intensity / roofline_plots | 第 5 节，全部五类运算、roofline 和批处理 |
| deep_network / gradients_basics / gradients_flops | 第 1、4 节，同一网络、标量梯度和矩阵反向 |
| optimizer / AdaGrad / train_loop | 第 6 节，参数注册、训练流程、状态与显存 |
| gradient_accumulation | 第 6 节，损失加权、更新边界与空间代价 |
| activation_checkpointing | 第 6 节，分段策略、空间和总重算量 |

数值复核、编译、排版和文本检查结果见 `检查记录.md`。
