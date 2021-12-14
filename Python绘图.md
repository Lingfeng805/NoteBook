# Seaborn

- `seaborn`同`matplotlib`一样，是python进行数据可视化分析的重要第三方包。`seaborn`是在`matplotlib`基础上进行**更高级**的API封装，使得作图更加容易，图像更加漂亮。
- `matplotlib`更加灵活，可定制化，`seaborn`像更高级的封装，使用方便快捷。（`seaborn`可满足大部分情况下的数据分析需求，但针对一些特殊情况仍然需要用到`matplotlib`.）



从零开始学：

[Seaborn从零开始学习教程（一） 风格选择](https://segmentfault.com/a/1190000014915873)

[Seaborn从零开始学习教程（二） 颜色调控篇](https://segmentfault.com/a/1190000014966210)

数据可视化：

（一）——整体样式与调色板：https://blog.csdn.net/ICERON/article/details/85088582

（二）——分布数据可视化：https://blog.csdn.net/ICERON/article/details/85300451

（三）——探索变量之间的关系：https://blog.csdn.net/ICERON/article/details/88734168

（四）—— 分类数据可视化：https://blog.csdn.net/ICERON/article/details/89158201


## Seaborn学习内容

【1】风格管理

- 绘图风格设置
- 颜色风格设置

【2】绘图方法

- 数据集的分布可视化
- 分类数据可视化
- 线性关系可视化

【3】结构网络

- 数据识别网格绘图

### (一)风格管理----1.绘图风格设置

```python
%matplotlib inline
import numpy as np
import matplotlib as mpl
import matplotlib.pyplot as plt
import seaborn as sns
np.random.seed(sum(map(ord, "aesthetics")))
# 定义一个简单的方程来绘制一些偏置的正弦波，来查看不同图画风格是什么样子？
def sinplot(flip=1):
    x = np.linspace(0, 14, 100)
    for i in range(1, 7):
        plt.plot(x, np.sin(x + i * .5) * (7 - i) * flip)
```

`matplotlib`默认参数下绘制结果如下：

```python
sinplot()
```

![matplotlib](E:/software/Typora/pictures/1.png)

转换为`seaborn`默认绘图，可使用`set()`方法。

```python
sns.set()
sinplot()
```

![seaborn](E:/software/Typora/pictures/1-16383482596451.png)

`seaborn`将`matplotlib`的参数划分为两个独立的组合。第一组设置绘图的外观风格，第二组将绘图的各种元素按比例缩放，以至可以嵌入到不同的背景环境中。

控制这些参数的接口主要有两对方法：

- 控制风格：`axes_style()`   ,   `set_style()`
- 缩放绘图：`plotting_context()`   ,   `set_context()`

第一个方法（`axes_style()`   ，  `plotting_context()` ）会返回一组字典参数；

第二个方法（`set_style()`   ,    `set_context()`）会设置matplotlib的默认参数。

#### Seaborn的五种绘图风格

【`darkgrid`】、【`whitegrid`】、【`dark`】、【`white`】、【`ticks`】  默认为darkgrid

```python
sns.set_style("whitegrid")
data = np.random.normal(size=(20, 6)) + np.arange(6) / 2
sns.boxplot(data=data);
```

![1](E:/software/Typora/pictures/1-16383487949002.png)

```python
sns.set_style("dark")
sinplot()
```

```python
sns.set_style("white")
sinplot()
```

```python
sns.set_style("ticks")
sinplot()
```

#### 移除轴脊柱

【`white`】、【`ticks`】两个风格都可以移除**顶部**和**右侧**的不必要的轴脊柱。通过`matplotlib`参数做不到这一点。可以使用`seaborn`的`despine()`方法来移除它们：

```python
sinplot()
sns.despine()
```

![seaborn](E:/software/Typora/pictures/1-16383493936793.png)

一些绘图也可以针对数据将轴脊柱进行偏置，当然也是通过调用`despine()`方法来完成。而当刻度没有完全覆盖整个轴的范围时，`trim`参数可以用来限制已有脊柱的范围。

```python
data = np.random.normal(size=(20, 6)) + np.arange(6) / 2
f, ax = plt.subplots()
sns.violinplot(data=data)
sns.despine(offset=10, trim=True);
```

可以通过`despine()`控制哪个脊柱将被移除。

```python
data = np.random.normal(size=(20, 6)) + np.arange(6) / 2
sns.set_style("whitegrid")
sns.boxplot(data=data, palette="deep")
sns.despine(left=True)    # 左边轴被删除
```

#### 临时设置绘图风格

可以在一个`with`语句中使用`axes_style()`方法来临时的设置绘图参数，允许使用不同风格的轴来绘图：

```python
with sns.axes_style("darkgrid"):
    plt.subplot(211)
    sinplot()
plt.subplot(212)
sinplot(-1)
```

![seaborn](E:/software/Typora/pictures/1-16383517952364.png)

#### 覆盖seaborn风格元素

定制化`seaborn`风格，可将一个字典参数传递给`axes_style()`和`set_style()`的参数`rc`。只能通过该方法覆盖风格定义中的部分参数。

```python
sns.axes_style()
{'axes.axisbelow': True,
 'axes.edgecolor': '.8',
 'axes.facecolor': 'white',
 'axes.grid': True,
 'axes.labelcolor': '.15',
 'axes.linewidth': 1.0,
 'figure.facecolor': 'white',
 'font.family': [u'sans-serif'],
 'font.sans-serif': [u'Arial',
  u'DejaVu Sans',
  u'Liberation Sans',
  u'Bitstream Vera Sans',
  u'sans-serif'],
 'grid.color': '.8',
 'grid.linestyle': u'-',
 'image.cmap': u'rocket',
 'legend.frameon': False,
 'legend.numpoints': 1,
 'legend.scatterpoints': 1,
 'lines.solid_capstyle': u'round',
 'text.color': '.15',
 'xtick.color': '.15',
 'xtick.direction': u'out',
 'xtick.major.size': 0.0,
 'xtick.minor.size': 0.0,
 'ytick.color': '.15',
 'ytick.direction': u'out',
 'ytick.major.size': 0.0,
 'ytick.minor.size': 0.0}
```

#### 绘图元素比例

首先，通过`set()`重置默认的参数：

```python
sns.set()
```

有四个预置的环境，按大小从小到大排列分别为：`paper`,  `notebook`,  `talk`,  `poster`。其中，`notebook`是默认的。

```python
sns.set_context("paper")
sinplot()
```

![seaborn](E:/software/Typora/pictures/1-16383601630565.png)

```python
sns.set_context("talk")
sinplot()
```

![talk](E:/software/Typora/pictures/Figure_1.png)

```python
sns.set_context("poster")
sinplot()
```

![poster](E:/software/Typora/pictures/Figure_1-16383603006936.png)

可以通过使用这些名字中的一个调用`set_context()`来设置参数，并且你可以通过提供一个字典参数值来覆盖参数。当改变环境时，你也可以独立的去缩放字体元素的大小.

```python
sns.set_context("notebook", font_scale=1.5, rc={"lines.linewidth": 2.5})
sinplot()
```

### 2.颜色风格设置

- 引入所需要的模块

```python
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
sns.set(rc={"figure.figsize": (6, 6)})
np.random.seed(sum(map(ord, "palettes")))
```

#### 建立调色板

`color_palette` 该函数拥有许多方法，可以生产各种颜色。可被任何有`palette`参数的函数在内部进行使用。

- `color_palette`函数可以接受任何`seaborn`或者`matplotlib`颜色表中颜色名称（除了`jet`）,也可接受任何有效的`matplotlib`形式的颜色列表（比如RGB元组，hex颜色代码，或者HTML颜色名称）
- 该函数的返回值总是一个RGB元组组成的列表，无参数调用`color_palette`函数则会返回当前默认的色环的列表。
- `set_palette`函数接受与`color_palette`一样的参数，并会对所有的绘图的默认色环进行设置。

有三种通用的`color_palette`可以使用，【`qualitative`】、【`sequential`】、【`diverging`】

- 1.分类色板【`qualitative`】

  - 对分类数据的显示很有帮助。当区别**不连续的且内在没有顺序关系的数据时**，该方式最好。

  - 当导入`seaborn`时，默认的色环就被改变成一组包含10种颜色的调色板，它使用了标准的`matplotlib`色环，为了让绘图变得更好看一些。

    ```python
    current_palette = sns.color_palette()
    sns.palplot(current_palette)
    ```

  - 有6种不同的默认主题，分别是：**deep**，**muted**，**pastel**，**birght**，**dark**，**colorblind**。

    ```python
    themes = ['deep', 'muted', 'pastel', 'bright', 'dark', 'colorblind']
    for theme in themes:
        current_palette = sns.color_palette(theme)
        sns.palplot(current_palette)
    ```

#### 使用色圈系统

当有超过10种类型的数据要区分时，最简单的方法就是**在一个色圈空间内使用均匀分布的颜色**。

- 最常用的方法就是使用`hls`色空间，它是一种简单的RGB值的转换。

```python
sns.palplot(sns.color_palette("hls", 12))    # 默认的色环有一组包含10种颜色的调色板
```

- 除此之外，还有一个`hls_palette`函数，它可以控制`hls`颜色的亮度和饱和度。

```python
sns.palplot(sns.hls_palette(12, l=.3, s=.8))
```

然而，由于人类视觉系统工作的原因，根据RGB颜色产生的平均视觉强度的颜色，从视觉上看起来并不是相同的强度。如果你观察仔细，就会察觉到，黄色和绿色会更亮一些，而蓝色则相对暗一些。因此，如果你想用`hls`系统达到一致性的效果，就会出现上面的问题。

为了修补这个问题，`seaborn`给`hls`系统提供了一个接口，可以让操作者简单容易的选择均匀分布，且亮度和饱和度看上去明显一致的色调。

```python
sns.palplot(sns.color_palette("husl", 12))
```

同样与之对应的，也有个`husl_palette`函数提供更灵活的操作。

#### 使用分类Color Brewer调色板

