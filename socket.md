# Socket套接字

## Socket编程思路

一、**服务器端**

- 【1】创建套接字，绑定socket到服务器ip和端口：

  ```python
  socket.socket(socket.AF_INET, socket.SOCK_STREAM)    # 创建socket
  s.bind(host, port)    # 绑定到服务器IP和端口
  ```

- 【2】开始监听

  ```python
  s.listen(n)    # n为排队的数目，至少1，一般设置成5即可
  ```

- 【3】进入循环，不断接受客户端的连接请求

  ```python
  s.accept()    # 返回一个新的客户端socket的客户端的ip地址
  ```

- 【4】接收传来的数据，并发送给对方数据

  ```python
  s.recv(1024)    # 1024代表一次性接收数据的最大长度为1024个字节
  s.sendall()    # 确保发送完整的数据
  ```

- 【5】传输完毕后，关闭套接字

  ```python
  s.close()
  ```

二、**客户端**

- 【1】创建套接字，连接到服务器ip和端口：

  ```python
  socket.socket(socket.AF_INET, socket.SOCK_STREAM)    # 创建socket
  s.connect(host, port)    # 连接到服务器IP和端口
  ```

- 【2】连接后发送数据和接收数据

  ```python
  s.sendall()    
  s.recv()
  ```

- 【3】传输完毕后，关闭套接字

  ```python
  s.close()
  ```

  

![image-20210903103547712](E:/software/Typora/pictures/image-20210903103547712.png)\

