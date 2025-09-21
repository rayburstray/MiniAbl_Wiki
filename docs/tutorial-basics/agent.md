---
sidebar_position: 3
---

# Agent模块

**Agent模块**用于处理来自**视觉识别模块**的符号，并且输出对应的**动作**(**int**)。**MiniABL**既支持**Python**,也支持**Prolog**

以MiniABL默认的**MiniHack-KeyRoom-S5-v0**环境为例子。**Agent模块**会接收来自**视觉识别模块**提取出来的符号**numpy矩阵**[**21, 79**], 然后执行**Prolog**/**Python**程序进行推理得到最终动作[**int**].


**Agent模块**的参数配置如下所示：（配置文件位于**src/config_manager/conf.yaml**）
```yaml
# 智能体策略设置
agent_config:
  type: "prolog" # python, prolog
  name: "test_prolog_agent" # python/prolog文件名称(不需要加文件后缀与完整前置文件路径)
```

最终用于执行所有的Agent都是继承位于**src/agent_manager/agent_interface.py**的**AgentInterface**.并且重写了其**act**方法:
```python
class AgentInterface(ABC):
    def __init__(self):
        pass

    @abstractmethod
    def act(self, obs: np.ndarray) -> int:
        """
        :param obs: observation(此obs为符号化的而非原始像素，当然若rec为none的话，obs为原始像素)
        :return: action
        """
```

## 基于Python的Agent
对于想要使用**Python**进行**Agent**构建的用户,可以在**src/agent_manager/python_agent**下创建一个**Python**文件,同时创建一个叫做`MyAgent`的类.并设置其继承`PythonAgent`,如下所示:
```python
class MyAgent(PythonAgent):
    def __init__(self, conf:dict):
        pass

    def act(self, obs:np.ndarray)->int:
        pass
```
随后将配置文件的`type`改成python, `name`改成刚刚创建的Python文件名.

然后再改成你想要执行的策略即可(一个完整的例子可以参考**src/agent_manager/python_agent/test_python_agent.py**)


### VLM(Python)

对于想要使用**VLM**作为**Agent**构建的用户，需要确保将**视觉识别模块**设置为**none**，这是因为**VLM**的输入是**原始图像**，不需要经过**符号化处理** 。此外，由于目前支持的**VLM**只有**豆包**，而**豆包**大模型需要通过api才能调用。因此需要注册[字节出品的火山平台的api](https://www.volcengine.com/)并且填入至**配置文件**才可以使用。

此外，调用豆包大模型接口并非**Openai格式**的，而是有其专门的Python包（尽管它们使用起来非常类似），所以在使用***VLM**作为**Agent**前，请确保你的环境中有火山引擎的包，否则请执行以下指令：

```bash
pip install --upgrade volcengine-python-sdk[ark]
pip install --upgrade arkitect
```

VLM在配置文件中的内容如下所示：

```yaml
vlm_config: # 目前只支持豆包
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

## 基于Prolog的Agent

对于想要使用**Prolog**进行**Agent**构建的用户, 可以在**src/agent_manager/prolog_agent**下创建一个**Prolog**文件.并且保证其中一定出现以下谓词:
```prolog
process_observation(Obs, Action)
```
随后将配置文件的`type`改成prolog, `name`改成刚刚创建的prolog文件名.

然后改成你想要执行的策略即可(一个完整的例子可以参考**src/agent_manager/prolog_agent/test_prolog_agent.pl**)