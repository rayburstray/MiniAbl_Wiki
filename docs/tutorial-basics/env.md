---
sidebar_position: 1
---

# 环境模块

**环境模块**的功能是根据**配置文件**提供相应的**gymnasium环境**以及相应的任务。

目前**MiniABL**主要考虑2个环境：**MiniHack**与**MiniGrid**。

:::warning
**MiniHack**下面有很多任务可供选择，**MiniABL**默认使用**MiniHack-KeyRoom-S5-v0**作为其任务，这是一个找钥匙开门再去目标点的简单小任务，[更多任务可在它的官方网址查看](https://minihack.readthedocs.io/en/latest/envs/index.html)
:::

环境模块的参数配置如下所示：（配置文件位于**src/config_manager/conf.yaml**）
```yaml
# 环境设置：
env_config:
  type: "minihack" # minigrid, minihack
  task: "MiniHack-KeyRoom-S5-v0"
```

只需要指定**type**和**task**为对应环境与任务即可。

:::tip
一般来说，用户不需要怎么修改这一块的代码(位于**src/env_manager/**)，除非想要获取来自原始**Minihack**的**gymnasium环境**的更多信息，例如**符号信息**和**文字信息**等。
:::

:::warning
**minigrid**的包似乎与1.0.0的**gymnasium**包兼容性不佳，导致**gymnasium**无法找到**minigrid**环境，为此，建议将**gymnasium**降级至0.29:
```bash
pip install gymnasium==0.29.0
```
:::