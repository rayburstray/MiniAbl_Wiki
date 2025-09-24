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
随后运行过程的动画与图像会保存在/tmp_data目录下.

### 配置文件

项目的配置文件位于**src/config_manager/conf.yaml**下，如果您想要快速运行一个能跑玩的**Demo**，您可以将其修改为如下yaml:
```yaml
# ===========================
# 配置文件
# ===========================

# 环境设置：
env_config:
  type: "minihack" # minigrid, minihack
  task: "MiniHack-KeyRoom-S5-v0"

# 视觉模块设置
vision_config:
  type: "cnn" # none, cnn, vlm, gt
  cnn_config:
    device: "cuda:7"
    num_classes: 7 # 分类器的label数目

# 智能体策略设置
agent_config:
  type: "python" # python, prolog
  name: "python_test_agent" # python/prolog文件名称(不需要加文件后缀与完整前置文件路径)
  vlm_config: # 目前只支持豆包, 如果你用的是vlm才需要填写
    api_key: 'dbxxxx'
    model_name: "doubao-seed-1-6-vision-250815"
    prompt: "给你一张minihack的游戏截图，这是一个比较复古的像素风游戏，地图由网格组成，player的任务是寻找到钥匙，开门，并到达目标点，黑色区域可能需要你走近之后才能看清，棕色的物体可能是门，但是你需要先找到钥匙才能开门，如果没有看到钥匙可能是因为钥匙在黑色的未探索区域中，请注意避让障碍物，例如未开锁的门以及墙壁，如果你发现你上一步执行移动行为之后，图片没有发生变化，那么说明你可能撞墙了，请你描述一下这个图片的内容，同时使用tool输出玩家下一步应该执行的动作"
    tools: {
                    "type": "function",
                    "function": {
                        "name": "act",  # 建议统一用字符串引号（单双引号均可，保持一致性）
                        "description": "用于控制 player 执行特定交互动作，包括四个方向移动、拾取物品、使用钥匙、开启门/宝箱",
                        "parameters": {
                            "type": "object",
                            "properties": {
                                "action": {
                                    "type": "string",
                                    "description": "player 需执行的具体动作，需从以下固定值中选择：\n- north/south/east/west：向对应方向移动（如 north 代表向北）\n- pickup：拾取当前位置的物品（如钥匙、道具）\n- apply：使用已拾取的物品（仅在持有钥匙等可交互道具时有效）\n- open：开启当前位置的门/宝箱（需先通过 apply 使用对应钥匙，否则无法打开）",
                                    "enum": ["north", "south", "east", "west", "pickup", "apply", "open"]  # 核心：限定仅允许的动作值
                                }
                            },
                            "required": ["action"]  # 明确 action 为必选参数，模型必须返回该值
                        }
                    }
                }



  
```