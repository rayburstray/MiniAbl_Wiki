---
sidebar_position: 2
---

# 视觉识别模块

**视觉识别模块**的作用是将纯像素输入转化为符号，它的**输入**是**纯像素numpy矩阵**，**输出**是**符号化的numpy矩阵**。

例如，对于默认使用的环境**MiniHack-KeyRoom-S5-v0**来说，它的输入是大小为[**336, 1264, 3**]的numpy数组, 即大小为宽336长1264的rgb图片,而输出则是[**21, 79**]的numpy数组。
:::tip
**Minihack**的每一个格子的像素值为**16x16**
:::

 视觉识别模块的代码都位于**src/rec_manager**下，并且继承自**src/rec_manager/rec_interface.py中的RecInterface类**。用户只需创建一个新类继承**RecInterface**，并重写其中的**recognize**方法即可。

 ```python
class RecInterface(ABC):
    def __init__(self):
        pass

    @abstractmethod
    def recognize(self, obs: np.ndarray) -> np.ndarray:
        pass
 ```
视觉识别模块在配置文件**src/config_manager/conf.yaml**中的部分为
```yaml
# 视觉模块设置
vision_config:
  type: "cnn" # none, cnn
  cnn_config:
    num_classes: 7 # 分类器的label数目
```

目前暂时只支持**none**与**cnn**

 ## none

**none**的意思即不做任何视觉识别方面的处理，直接将原始图像输入送入下一个环节。只需要将**type**设置为**none**就可以了。

设置为none的情况一般是当`Agent模块`为vlm的时候。

## cnn
**cnn**的输入是一个**16*16**的像素小块，而输出则是这个小块所属的类别。需要注意的是，对于不同的任务，类别可能会发生变化，例如对于默认的环境**MiniHack-KeyRoom-S5-v0**，我们只需要定义7个类别：0-虚空，1-墙壁，2-地板，3-门，4-钥匙，5-玩家，6-目标点。因此这个cnn本质上是在做7分类的任务。

:::warning
众所周知，训练一个**cnn**, 需要一个**带标签**的数据集，自动获取并保存图像小块的代码已经内置，但是**打标**这一步，仍然需要人工执行。
如果您的任务仍然是**MiniHack-KeyRoom-S5-v0**，那么您可以跳过这一步，因为这个任务的cnn已经训练好了，权重位于**src/rec_manager/cnn_rec/cnn_model.pth**

但是如果你的任务是一个**全新**的任务，需要自己定义**新的符号以及类别**，那么您可能需要考虑重新训练一个**cnn**来适配这个任务。您需要
- 1.删除原来的**src/rec_manager/cnn_rec/tiles**文件夹、**cnn**权重以及**labels.txt**文件。
- 2.再重新在**配置文件**中设置新的类别数。
- 3.然后重新运行main.py。
- 4.在labels.txt文件打标，第i行表示**src/rec_manager/cnn_rec/tiles**文件夹下第i张图片的**标签**
- 由于可能单次运行无法获取所有的**tile**，并且最初的**cnn**是随机分类的，可能会导致策略异常，因此您可能需要多次执行**步骤3与步骤4**
:::

:::info
由于对于新任务的cnn训练较为繁琐，我们可能未来会推出webui，可以在网页中直接打标，单次运行即可。
:::

## 自定义

如果您想要自己实现一个视觉识别模块，那么您可以参考**src/rec_manager/cnn_rec/rec_factory.py**的代码，将自己实现的继承**RecInterface**的新类填入其中，然后修改**conf.yaml配置文件**即可。