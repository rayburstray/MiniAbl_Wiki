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


## 基于Prolog的Agent

对于想要使用**Prolog**进行**Agent**构建的用户, 可以在**src/agent_manager/prolog_agent**下创建一个**Prolog**文件.并且保证其中一定出现以下谓词:
```prolog
process_observation(Obs, Action)
```
随后将配置文件的`type`改成prolog, `name`改成刚刚创建的prolog文件名.

然后改成你想要执行的策略即可(一个完整的例子可以参考**src/agent_manager/prolog_agent/test_prolog_agent.pl**)