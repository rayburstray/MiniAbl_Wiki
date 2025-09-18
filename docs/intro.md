---
sidebar_position: 1
---

# 项目简介

:::warning
本项目仍处于早期阶段，目前正在**积极开发中** 。
:::

**MiniABL**是一个使用**ABL**方法完成诸如**Minihack**之类复杂强化学习任务的工具。它不仅支持**Python**编写程序，也支持基于**Prolog**的声明式语言构建的**Agent**。

在具体方法上, MiniABL支持最原始朴素的手写策略完成任务,也支持调用VLM之类的多模态模型。

## 👀 大致流程
**MiniABL**的运行流程如下图所示：
![MiniABL 运行流程](/img/pipeline.png)

可以看到主要是3个**模块**循环运行: `环境模块`、`视觉识别模块`、`Agent模块`。

一般来说，重心会集中在`视觉识别模块`以及`Agent模块`的设计上。至于`环境模块`默认使用**Minihack**的**MiniHack-KeyRoom-S5-v0**环境，如果有更多的需求可以直接更改`配置文件`。

:::info
`配置文件`位于/src/config_manager/conf.yaml
:::

## ✨快速入门
**MiniABL**已经配置了一个可以完整运行的**Demo**，其中`视觉识别`模块是一个已训练好的**小型CNN**，权重已在Repo中，它负责将每个像素小块进行分类，而`Agent模块`通过**Prolog程序**进行推理，归纳出下一步的step并执行。

因此，对于刚入门的用户，您可以选择执行
```python
python main.py
```
随后运行过程的动画与图像会保存在/tmp_data目录下