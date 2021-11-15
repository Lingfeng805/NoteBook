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



## 界面动作处理（signal和slot）

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



## 动态加载UI文件

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



