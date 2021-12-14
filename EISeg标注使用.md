# EISeg使用

## 模型准备

- 在使用EIseg前，请先下载模型参数。EISeg开放了在COCO+LVIS和大规模人像数据上训练的四个标注模型，满足通用场景和人像场景的标注需求。其中模型结构对应EISeg交互工具中的网络选择模块，用户需要根据自己的场景需求选择不同的网络结构和加载参数。

| 模型类型   | 适用场景                   | 模型结构       | 下载地址                                                     |
| ---------- | -------------------------- | -------------- | ------------------------------------------------------------ |
| 高精度模型 | 适用于通用场景的图像标注。 | HRNet18_OCR64  | [hrnet18_ocr64_cocolvis](https://bj.bcebos.com/paddleseg/dygraph/interactive_segmentation/ritm/hrnet18_ocr64_cocolvis.pdparams) |
| 轻量化模型 | 适用于通用场景的图像标注。 | HRNet18s_OCR48 | [hrnet18s_ocr48_cocolvis](https://bj.bcebos.com/paddleseg/dygraph/interactive_segmentation/ritm/hrnet18s_ocr48_cocolvis.pdparams) |
| 高精度模型 | 适用于人像标注场景。       | HRNet18_OCR64  | [hrnet18_ocr64_human](https://bj.bcebos.com/paddleseg/dygraph/interactive_segmentation/ritm/hrnet18_ocr64_human.pdparams) |
| 轻量化模型 | 适用于人像标注场景。       | HRNet18s_OCR48 | [hrnet18s_ocr48_human](https://bj.bcebos.com/paddleseg/dygraph/interactive_segmentation/ritm/hrnet18s_ocr48_human.pdparams) |

- 打开软件后，在对项目进行标注前，需要进行如下设置：

  - 【1】**模型参数加载**

    选择合适的网络，并加载对应的模型参数。目前在EISeg中，网络分为`HRNet18s_OCR48`和`HRNet18_OCR64`，并分别提供了人像和通用两种模型参数。在正确加载模型参数后，右下角状态栏会给予说明。若网络参数与模型参数不符，将会弹出警告，此时加载失败需重新加载。正确加载的模型参数会记录在`近期模型参数`中，可以方便切换，并且下次打开软件时自动加载退出时的模型参数。

  - 【2】**图像加载**

    打开图像/图像文件夹。当看到主界面图像正确加载，`数据列表`正确出现图像路径即可。

  - 【3】**标签添加/加载**

    添加/加载标签。可以通过`添加标签`新建标签，标签分为4列，分别对应像素值、说明、颜色和删除。新建好的标签可以通过`保存标签列表`保存为txt文件，其他合作者可以通过`加载标签列表`将标签导入。通过加载方式导入的标签，重启软件后会自动加载。

  - 【4】**自动保存设置**

    在使用中可以将`自动保存`设置上，设定好文件夹即可，这样在使用时切换图像会自动将完成标注的图像进行保存。当设置完成后即可开始进行标注，默认情况下常用的按键/快捷键如下，如需修改可按`E`弹出快捷键修改。

    | 部分按键/快捷键       | 功能              |
    | --------------------- | ----------------- |
    | 鼠标左键              | 增加正样本点      |
    | 鼠标右键              | 增加负样本点      |
    | 鼠标中键              | 平移图像          |
    | Ctrl+鼠标中键（滚轮） | 缩放图像          |
    | S                     | 切换上一张图      |
    | F                     | 切换下一张图      |
    | Space（空格）         | 完成标注/切换状态 |
    | Ctrl+Z                | 撤销              |
    | Ctrl+Shift+Z          | 清除              |
    | Ctrl+Y                | 重做              |
    | Ctrl+A                | 打开图像          |
    | Shift+A               | 打开文件夹        |
    | E                     | 打开快捷键表      |
    | Backspace（退格）     | 删除多边形        |
    | 鼠标双击（点）        | 删除点            |
    | 鼠标双击（边）        | 添加点            |