# watchdog

- 用于监测文件系统活动

## Quickstart

一个简单的例子：递归的监控当前目录来监测变化（意味着它将遍历任何子目录）。

- 【1】创建一个`watchdog.events.FileSystemEventHandler`的线程类实例；
- 【2】实现一个``watchdog.events.FileSystemEventHandler`的子类；
- 【3】使用附加了事件处理程序的观察者实例来计划监控一些路径；
- 【4】启动观察者线程，等待它生成事件，而不阻塞我们的主线程。

默认情况下，`watchdog.observer.observer`实例不会监视子目录。通过对`watchdog.observer.observer.schedule()`的调用中传递`recursive=True`，可以确保监控整个目录树。

- 以下示例程序将递归地监视当前目录中的文件系统更改，并将它们记录到控制台。

```python
import sys
import time
import logging    # 用于输出运行日志，可设置输出日志的等级、日志保存路径、日志文件回滚等
from watchdog.observers import Observer
from watchdog.events import LoggingEventHandler

if __name__ = "__main__":
    logging.basicConfig(level=logging.INFO,
                       format='%(asctime)s-%(message)s',
                       datefmt='%Y-%m-%d %H:%M:%S')  
    # format指定输出的格式和内容，%(asctime)s:打印日志的时间，%(message)s:打印日志信息
    # datefmt:指定时间格式（2021-09-08 15:28:30）
    path = sys.argv[1] if len(sys.argv) > 1 else '.' # sys.argv:实现从程序外部向程序传递参数；                                  当外部有输入时path=‘输入的第一个路径’，无输入时path=‘.’即当前路径
    event_handler = LoggingEventHandler()
    observer = Observer()
    observer.schedule(event_handler, path, recursive=True)
    observer.start()
    try:
        while True:
            time.sleep(1)    # 延时1s
    except KeyboardInterrupt:
        observer.stop()
    observer.join()
'''
直接运行该'**.py'文件，检测该文件同目录下文件状态，并打印日志信息；
运行'**.py'文件时输入同目录下子目录名称则检测子目录下的文件状态，如'python **.py 123'则监视名为'123'的文件夹内文件状态。
'''
```

## 使用者指南

- API参考资料
  - `watchdog.events`
  - `watchdog.observers.api`
  - `watchdog.observers`
  - `watchdog.observers.polling`
  - `watchdog.utils`
  - `watchdog.utils.dirsnapshot`

### [1] **watchdog.events**

概要：文件系统事件和事件处理程序。

```python
class watchdog.events.FileSystemEvent(src_path)
    Bases: object

    Immutable type that represents a file system event that is triggered when a change       occurs on the monitored file system.
    不可变类型，表示当受监控的文件系统发生更改时触发的文件系统事件

    All FileSystemEvent objects are required to be immutable and hence can be used as         keys in dictionaries or be added to sets.

    event_type = None
    The type of the event as a string.

    is_directory = False
    True if event was emitted for a directory; False otherwise.

    src_path
        Source path of the file system object that triggered this event.
```

