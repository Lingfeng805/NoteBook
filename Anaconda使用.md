# Anaconda

## 删除创建的环境

```python
conda env remove -n 环境名
```

- 查看安装了哪些包

  ```python
  conda list
  ```

- 查看当前存在哪些虚拟环境

  ```python
  conda env list 
  conda info -e

- 检查更新当前conda

  ```python
  conda update conda
  ```

  conda create -n pytorchtest python=3.6

## 创建虚拟环境

```python
conda create -n 环境名 python=x.x
```

anaconda命令创建python版本为`x.x`，名字为`环境名`的虚拟环境。新创建的**环境名**文件可以在Anaconda安装目录Environments文件下找到。

- 激活或者切换虚拟环境

  ```python
  Linux:  source activate your_env_nam
  Windows: conda activate your_env_name
  ```

- 关闭虚拟环境

  ```python
  windows: conda deactivate 环境名
  Linux: source deactivate
  ```

- 删除虚拟环境

  ```python
  conda remove -n 环境名 --all
  ```

- 恢复默认镜像

  ```python
  conda config --remove-
  ```

  

## labelme

- 使用labelme

  ```python
  # 在Anaconda Powershell Prompt命令行
  conda activate labelme    # 1.激活安装有labelme的虚拟环境
  labelme    # 2.输入labelme打开软件
  
  ```

- 参数介绍

  ```python
  labelme --help    # 显示labelme的使用方法
  # 重要参数
  --output    # 标注文件存放位置；如果给的参数是以.json结尾，则会向该文件写入一个标签。也就意味着如果使用.json指定位置，则只能对一个图像进行注释。如果位置不是以.json结尾，程序将假定它是一个目录。注释将以与在其上进行注释的图像相对应的名称存储在此目录中。
  
  --flags    # 为图像创建分类标签，多分类用逗号隔开。 
  --nosortlabels    # 是否对标签进行排序
  ```

  **举例**

  ```python
  命令行输入
  labelme image1.png --output image1.json --flags 0,1
  # 其中，image1.png是图像的地址，而不是名字。注意区别，因为我现在的路径在图像存放的当前文件夹，所以输入名字就可以直接找到该图像。如果你当前路径不在图像存放的文件夹，你需要给出图像的完整路径，如F:\labelmeImage\image1.png --output image1.json 就是把打标签的结果存放在image1.json这个文件里。因为我是对单一图像打标签，所以是以.json结尾。如果是对一个文件夹进行打标签，那这里就不要以.json结尾，直接输入你想存放的文件夹就行。 --flags: 描述你分类的标签是什么，0,1表示分两类。也可以写成多类，0,1,2,3,4.也可以用其他字符，如 negative，positive, 或者cat, dog。等等~
  ```

  

