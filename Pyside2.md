# Python Qt

- 程序的用户交互界面，英文称之为UI(User interface);
- Qt库：非常强大的图形界面开发库，基于C++语言开发，PySide2、PyQt5可以通过Python语言使用Qt;



## 一个案例

- 现在我们要开发一个程序，让用户输入一段文本包含：员工姓名、薪资、年龄。格式如下：

```python
# 该程序可以把薪资在2万以上、以下的人员名单分别打印出来。
薛蟠     4560 25
薛蝌     4460 25
薛宝钗   35776 23
薛宝琴   14346 18
王夫人   43360 45
王熙凤   24460 25
王子腾   55660 45
王仁     15034 65
尤二姐   5324 24
贾芹     5663 25
贾兰     13443 35
贾芸     4522 25
尤三姐   5905 22
贾珍     54603 35
```

```python
from PySide2.QtWidgets import QApplication, QMainWindow, QPushButton,  QPlainTextEdit

app = QApplication([])    # 【1】初始化操作

# 【2】QMainWindow() 主窗口控件类
window = QMainWindow()
window.resize(500, 400)
window.move(300, 310)
window.setWindowTitle('薪资统计')

# 【3】QPlainTextEdit() 文本框控件类
textEdit = QPlainTextEdit(window)
textEdit.setPlaceholderText("请输入薪资表")
textEdit.move(10,25)
textEdit.resize(300,350)

# 【4】QPushButton()  按钮控件类
button = QPushButton('统计', window)
button.move(380,80)

window.show()

app.exec_()
```

- 在Qt系统中，控件(widget)是**层层嵌套**的，除了最顶层的控件，其他的控件都有父控件。
  - QPlainTextEdit、QPushButton 实例化时，都有一个参数window。即指定它的父控件对象是window对应的QMainWindow 主窗口。
  - 而实例化QMainWindow 主窗口时，却没有指定 父控件， 因为它就是最上层的控件了。
- 控件对象的 move( ) 方法决定了这个控件显示的位置。
  - `window.move(300, 310)` 就决定了 主窗口的 左上角坐标在 `相对屏幕的左上角` 的X横坐标300像素, Y纵坐标310像素这个位置。

- 控件对象的 resize ( )方法决定了这个控件显示的大小。
  - `window.resize(500, 400)` 就决定了 主窗口的 宽度为500像素，高度为400像素。

- 放在主窗口的控件，要能全部显示在界面上， 必须加上下面这行代码

  ```python
  window.show()
  ```

- 最后 ，通过下面这行代码， 进入QApplication的事件处理循环，接收用户的输入事件（），并且分配给相应的对象去处理。

  ```python
  app.exec_()
  ```
  
  



### 界面动作处理（signal和slot）

- 在 Qt 系统中， 当界面上一个控件被操作时，比如 被点击、被输入文本、被鼠标拖拽等， 就会发出 `信号` ，英文叫 `signal` 。就是表明一个事件（比如被点击、被输入文本）发生了。我们可以预先在代码中指定 处理这个 signal 的函数，这个处理 signal 的函数 叫做 `slot` 。

- 比如，我们可以像下面这样定义一个函数

  ```python
  def handleCalc():
      print('统计按钮被点击了')
  ```

  - 然后， 指定 如果 发生了button 按钮被点击 的事情，需要让 `handleCalc` 来处理，像这样

    ```python
    button.clicked.connect(handleCalc)
    ```

    - 用QT的术语来解释上面这行代码，就是：把button被点击(clicked)的信号(signal)， 连接（connect）到了 handleCalc 这样的一个 slot上.**即让handleCalc( )来处理button被点击的操作**.

- ```python
  from PySide2.QtWidgets import QApplication, QMainWindow, QPushButton,  QPlainTextEdit,QMessageBox
  
  def handleCalc():
      info = textEdit.toPlainText()    # toPlainText()方法获取编辑框内的文本内容
  
      # 薪资20000 以上 和 以下 的人员名单
      salary_above_20k = ''
      salary_below_20k = ''
      for line in info.splitlines():
          if not line.strip():
              continue
          parts = line.split(' ')
          # 去掉列表中的空字符串内容
          parts = [p for p in parts if p]
          name,salary,age = parts
          if int(salary) >= 20000:
              salary_above_20k += name + '\n'
          else:
              salary_below_20k += name + '\n'
  
      QMessageBox.about(window,
                  '统计结果',
                  f'''薪资20000 以上的有：\n{salary_above_20k}
                  \n薪资20000 以下的有：\n{salary_below_20k}'''
                  )
  
  app = QApplication([])
  
  window = QMainWindow()
  window.resize(500, 400)
  window.move(300, 300)
  window.setWindowTitle('薪资统计')
  
  textEdit = QPlainTextEdit(window)
  textEdit.setPlaceholderText("请输入薪资表")
  textEdit.move(10,25)
  textEdit.resize(300,350)
  
  button = QPushButton('统计', window)
  button.move(380,80)
  button.clicked.connect(handleCalc)
  
  window.show()
  
  app.exec_()
  ```



## 界面设计师 Qt Designer

- Qt程序界面的一个个窗口、控件，可以通过上述相应的代码创建出来；但是，直接通过代码写出相应的界面，比较困难。可能运行时程序界面呈现的样子并不是我们期望的样子，通过修改代码调整界面上的控件位置，再运行预览，反复多次这样操作，比较**繁琐**。
- 避免上述繁琐的方式，可以通过QT界面设计师**Qt Designer**,通过**拖拖拽拽**便可以直观的创建出程序的大体界面。
- 【运行】“E:\software\anaconda3\envs\PyQt\Lib\site-packages\PySide2”找到该文件夹下的`designer.exe`执行文件。
- 通过**Qt Designer**设计的界面，最终是保存在一个ui文件中。(该ui文件，是一个XML格式的界面定义)



### 动态加载UI文件

- 通过上述**Qt Designer**生成的UI界面定义文件，Python程序便可以从文件中加载UI定义，创建一个相应的窗体对象。

- ```python
  from PySide2.QtWidgets import QApplication, QMessageBox
  from PySide2.QtUiTools import QUiLoader
  
  class Stats:
  
      def __init__(self):
          # 从文件中加载UI定义
  
          # 从 UI 定义中动态 创建一个相应的窗口对象
          # 注意：里面的控件对象也成为窗口对象的属性了
          # 比如 self.ui.button , self.ui.textEdit
          self.ui = QUiLoader().load('./UI/薪资统计.ui')    # UI文件保存的目录地址
  
          self.ui.button.clicked.connect(self.handleCalc)
  
      def handleCalc(self):
          info = self.ui.textEdit.toPlainText()
  
          salary_above_20k = ''
          salary_below_20k = ''
          for line in info.splitlines():
              if not line.strip():
                  continue
              parts = line.split(' ')
  
              parts = [p for p in parts if p]
              name,salary,age = parts
              if int(salary) >= 20000:
                  salary_above_20k += name + '\n'
              else:
                  salary_below_20k += name + '\n'
  
          QMessageBox.about(self.ui,
                      '统计结果',
                      f'''薪资20000 以上的有：\n{salary_above_20k}
                      \n薪资20000 以下的有：\n{salary_below_20k}'''
                      )
  
  app = QApplication([])
  stats = Stats()
  stats.ui.show()
  app.exec_()
  
  ```



### 转化UI文件为Python代码

- 另一种使用UI文件的方式：先把UI文件直接转化为包含界面定义的Python代码文件，然后再程序中使用定义界面的类。

- 1.执行如下的命令 把UI文件直接转化为包含界面定义的Python代码文件

  ```python
  pyside2-uic main.ui > ui_main.py
  ```

  2.然后在你的代码文件中这样使用定义界面的类

  ```python
  from PySide2.QtWidgets import QApplication,QMainWindow
  from ui_main import Ui_MainWindow
  
  class MainWindow(QMainWindow):
  
      def __init__(self):
          super().__init__()
          # 使用ui文件导入定义界面类
          self.ui = Ui_MainWindow()
          # 初始化界面
          self.ui.setupUi(self)
  
          # 使用界面定义的控件，也是从ui里面访问
          self.ui.webview.load('http://www.baidu.com')
  
  app = QApplication([])
  mainw = MainWindow()
  mainw.show()
  app.exec_()
  ```

- 【注】**通常采用动态加载比较方便，因为改动界面后，不需要转化，直接运行，特别方便。**



### 界面布局 Layout

- 前述界面程序有个**问题**，当用鼠标拖拽主窗口边框右下角进行缩放，发现界面里控件一直保持原有大小不变。通常希望的效果是：随着主窗口的缩放，界面里的控件、控件之间的距离也相应的进行缩放。

- 可通过Qt**界面布局Layout**类来实现该功能。

- 常用的Layout布局分为4种：

  - **QHBoxLayout 水平布局**

    把控件从左到右水平横着摆放，如图![img](E:/software/Typora/pictures/qhboxlayout-with-5-children.png)

  - **QVBoxLayout 垂直布局**

    把控件从上到下竖着摆放，如图![img](E:/software/Typora/pictures/qvboxlayout-with-5-children.png)

  - **QGridLayout 表格布局**

    把多个控件 格子状摆放，有的控件可以占据多个格子，如图![img](E:/software/Typora/pictures/qgridlayout-with-5-children.png)

  - **QFormLayout 表单布局**

    像一个只有两列的表格，非常适合填写注册表单这种类型的界面，如图![img](E:/software/Typora/pictures/qformlayout-with-6-children.png)



### 调整控件位置和大小

- 调整Layout中控件的大小比例    `sizePolicy`
- 调整控件间距    
  - 调整控件上下间距：给控件添加layout，然后通过设定layout的上下的padding和margin来调整间距；
  - 调整控件的左右间距：通过添加horizontal space进行控制，也可以通过layout的左右margin。
- 调整控件次序 ？

【界面布局步骤建议】

对界面控件进行布局，白月黑羽的经验是 按照如下步骤操作：

- 先不使用任何Layout，把所有控件 按位置 摆放在界面上
- 然后先从 最内层开始 进行控件的 Layout 设定
- 逐步拓展到外层 进行控件的 Layout设定
- 最后调整 layout中控件的大小比例， 优先使用 Layout的 layoutStrentch 属性来控制



### 从一个窗口跳转到另外一个窗口

- 程序开始的时候显示一个窗口（比如登录窗口），操作后进入到另外一个窗口，**怎么做？**

- 【**实例化另外一个窗口，显示新窗口，关闭老窗口**】

```python
from PySide2 import QtWidgets
import sys

class Window2(QtWidgets.QMainWindow):

    def __init__(self):
        super().__init__()
        self.setWindowTitle('窗口2')

        centralWidget = QtWidgets.QWidget()
        self.setCentralWidget(centralWidget)

        button = QtWidgets.QPushButton('按钮2')

        grid = QtWidgets.QGridLayout(centralWidget)
        grid.addWidget(button)


class MainWindow(QtWidgets.QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle('窗口1')

        centralWidget = QtWidgets.QWidget()
        self.setCentralWidget(centralWidget)

        button = QtWidgets.QPushButton('打开新窗口')
        button.clicked.connect(self.open_new_window)

        grid = QtWidgets.QGridLayout(centralWidget)
        grid.addWidget(button)

    def open_new_window(self):
        # 实例化另外一个窗口
        self.window2 = Window2()
        # 显示新窗口
        self.window2.show()
        # 关闭自己
        self.close()

if __name__ == '__main__':
    app = QtWidgets.QApplication(sys.argv)
    window = MainWindow()
    window.show()
    sys.exit(app.exec_())
```

- 如果经常要在两个窗口来回跳转，可以使用 `hide()` 方法 隐藏窗口， 而不是 `closes()` 方法关闭窗口。 这样还有一个好处：被隐藏的窗口再次显示时，原来的操作内容还保存着，不会消失。



### 弹出模式对话框

- 有的时候，我们需要弹出一个模式对话框输入一些数据，然后回到 原窗口。
- 所谓模式对话框，就是弹出此对话框后， 原窗口就处于不可操作的状态，只有当模式对话框关闭才能继续。

```python
from PySide2 import QtWidgets
import sys

class MyDialog(QtWidgets.QDialog):
    def __init__(self):
        super().__init__()
        self.setWindowTitle('模式对话框')

        self.resize(500, 400)
        self.textEdit = QtWidgets.QPlainTextEdit(self)
        self.textEdit.setPlaceholderText("请输入薪资表")
        self.textEdit.move(10, 25)
        self.textEdit.resize(300, 350)

        self.button = QtWidgets.QPushButton('统计', self)
        self.button.move(380, 80)

class MainWindow(QtWidgets.QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle('主窗口')

        centralWidget = QtWidgets.QWidget()
        self.setCentralWidget(centralWidget)

        button = QtWidgets.QPushButton('打开模式对话框')
        button.clicked.connect(self.open_new_window)

        grid = QtWidgets.QGridLayout(centralWidget)
        grid.addWidget(button)

    def open_new_window(self):
        # 实例化一个对话框类
        self.dlg = MyDialog()        
        # 显示对话框，代码阻塞在这里，
        # 等待对话框关闭后，才能继续往后执行
        self.dlg.exec_()

if __name__ == '__main__':
    app = QtWidgets.QApplication(sys.argv)
    window = MainWindow()
    window.show()
    sys.exit(app.exec_())
```



## 发布程序

