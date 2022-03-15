# CUDA Python

- Compute Unified Device Architecture  计算统一设备架构

- CUDA并行计算模式：同时应用多个计算资源解决一个计算问题。
  - 涉及多个计算资源或处理器
  - 问题被分解为多个离散的部分，可以同时处理（并行）
  - 每个部分可以由一系列指令完成
  - 每个部分的指令在不同的处理器上执行
- 术语：
  - Host    CPU和内存（host memory）
  - Device  GPU和显存（device memory）
- **处理流程**：
  - 1）把输入数据从CPU内存复制到GPU显存；（先在CPU上进行数据预处理，GPU）
    - CPU Memory  →→ GPU DRAM
  - 2）在执行芯片上缓存数据，加载GPU程序并执行；
  - 3）将计算结果从GPU显存中复制到CPU内存中；



## Numba

- Numba通过及时编译机制(JIT)优化Python代码，Numba可以针对本机的硬件环境进行优化，同时支持CPU和GPU的优化，并且可以和Numpy集成，使Python代码可以在GPU上运行，只需在函数上方加上相关的指令标记，如下所示：

  ```python
  import pyculib.fft
  import numba.cuda
  import numpy as np
  
  @numba.cuda.jit
  def apply_mask(frame, mask):
      i, j = numba.cuda.grid(2)
      frame[i, j] *= mask[i, j]
      
  # ... skipping some array setup here: frame is a 720×1280 numpy array
  
  out = np.empty_like(mask, dtype=np.complex64)
  gpu_temp = numba.cuda.to_device(out)    # make GPU array
  gpu_mask = numba.cuda.to_device(mask)    # make GPU array
  
  pyculib.fft.fft(frame.astype(np.complex64), gpu_temp)    # implied host->device
  apply_mask[blocks, tpb](gpu_temp, gpu_mask)    # all on device
  pyculib.fft.ifft(gpu_temp, out)    # implied device->host
  ```

  CUDA编程模型的关键术语：

  - host ：the CPU
  - device ： the GPU
  - host memory ： the system main memory
  - device memory ： onboard memory on a GPU card
  - kernels : a GPU function launched by the host and executed on the device (CPU上调用GPU上执行 )
  - device function : a GPU function executed on the device which can only be called from the device (i.e. from a kernel or another device function)（只支持在GPU上执行的函数，不能在CPU上执行）

- 【定义Kernel函数】

  ```python
  @cuda.jit('void(int32[:], int32[:])')
  def foo(aryA, aryB):
      ...
  ```

- 【调用Kernel函数】

  ```python
  griddim = 1, 2    # 一个Grid中有多少个Block
  blockdim = 3, 4    # 一个Block中有多少个Thead
  foo[griddim, blockdim](aryA, aryB)    # [ ] 内为定义调用了多少个线程
  ```

  

## GPU的硬件结构

下图所示的是GA100的硬件架构图，它包含了：

- 8192 FP32 CUDA Cores（用于计算的核心）
- 128个SM（SM指stream multiprocessor，即流多处理器，可以方便一块线程之间的协作）
- 每个SM包含64个FP32 CUDA Core，4个第三代Tensor Core



- **Device**

![Device](E:/software/Typora/pictures/hardware.png)

- **SM**

![SM](E:/software/Typora/pictures/sm.png)

- 【注】Nvidia GPU的浮点计算能力（FP64/FP32/FP16）

  - **浮点计数**：利用浮动小数点的方式使用不一样长度的二进制来表示一个数字，与之对应的是定点数。一样的长度下浮点数能表达的数字范围相比定点数更大，但浮点数不能精确表达全部实数，而只能采用更加接近的不一样精度来表达。

    - 单精度的浮点数：采用4个字节（即32位二进制）来表达一个数字；
    - 双精度的浮点数：采用8个字节（即64位二进制）来表达一个数字；

    由于采用不一样位数的浮点数的表达精度不同，因此形成的计算偏差也不同，对于须要处理的数字范围大并且须要精确计算的科学计算来讲，就要求采用双精度浮点数，而对于常见的多媒体和图形处理计算，32位的单精度浮点计算已经足够了，对于要求精度更低的机器学习等一些应用来讲，半精度16位浮点数就能够甚至8位浮点数就已经够用了。

  - 对于浮点计算来讲，CPU能够同时支持不一样精度的浮点运算，但在GPU里针对单精度和双精度就需要各自独立的计算单元，GPU中支持单精度运算的Single Precision ALU称为FP32 core，支持双精度运算的Double Precision ALU称为FP64 core（或称为DP unit）。

  - 【GPU浮点计算理论峰值能力】

    - **理论峰值** = GPU芯片数量 * GPU Boost主频 * 核心数量 * 单时钟周期内能处理的浮点计算次数
    - （GPU里单精度和双精度的浮点计算能力需要分开计算），以Tesla P100为例：
    - 双精度理论峰值 ＝ FP64 Cores ＊ GPU Boost Clock ＊ 2 ＝ 1792 ＊1.48GHz＊2 = 5.3 TFlops
    - 单精度理论峰值 ＝ FP32 cores ＊ GPU Boost Clock ＊ 2 ＝ 3584 ＊ 1.58GHz ＊ 2 ＝  10.6 TFlops

 ## 5.CUDA的线程层次

在计算机科学中，执行线程是可由调度程序独立管理的最小程序指令序列。
在GPU中，可以从多个层次管理线程：

- Thread: sequential execution unit 所有线程执行相同的核函数 并行执行
- Thread Block: a group of threads 执行在一个Streaming Multiprocessor (SM) 同一个Block中的线程可以协作
- Thread Grid: a collection of thread blocks 一个Grid当中的Block可以在多个SM中执行

![线程层次](E:/software/Typora/pictures/thread.png)

## 6.CUDA线程索引

- 我们可以通过cuda.threadIdx，cuda.blockIdx，cuda.blockDim，cuda.gridDim来确定每个线程要处理的数据

  ![](E:/software/Typora/pictures/thread_index2_python.png)

## 7.实际编程

接下来我们来尝试编写第一个CUDA程序。我们来实现一个向量加法的例子，将两个包含1000000个元素的向量相加。
当我们用CPU实现时：

```python
def vecAdd(n, a, b, c):
    for i in range(n):
        c[i] = a[i] + b[i]
# CPU串行计算，一个接一个
```

当我们用GPU实现时：

```python
def add_kernel(x, y, out):
    tx = cuda.threadIdx.x # 当前线程在block中的索引值
    ty = cuda.blockIdx.x  # 当前线程所在block在grid中的索引值

    block_size = cuda.blockDim.x  # 每个block有多少个线程
    grid_size = cuda.gridDim.x    # 每个grid有多少个线程块
    
    start = tx + ty * block_size    # 即上述的int index，“唯一索引值”
    stride = block_size * grid_size
    
    for i in range(start, x.shape[0], stride):
        out[i] = x[i] + y[i]
```

执行下面的代码，来完成向量相加的例子

```python
from numba import cuda, float32
# cuda的核函数，每个调用的线程都要执行该函数
@cuda.jit
def add_kernel(x, y, out):
    tx = cuda.threadIdx.x 
    ty = cuda.blockIdx.x  

    block_size = cuda.blockDim.x  
    grid_size = cuda.gridDim.x    
    
    start = tx + ty * block_size
    stride = block_size * grid_size
    for i in range(start, x.shape[0], stride):
        out[i] = x[i] + y[i]
```

```python
import numpy as np

n = 100000
x = np.arange(n).astype(np.float32)
y = 2 * x
out = np.empty_like(x)

threads_per_block = 128    # 必须小于1024
blocks_per_grid = 30

add_kernel[blocks_per_grid, threads_per_block](x, y, out)
print(out[:20])
```

此时，我们看到在计算向量运算的时候，GPU比CPU有明显的速度优势
**虽然这个例子很简单，但是在我们的实际应用中却经常用到。
比如：我们在对拍好的照片进行美化的时候，需要将照片的亮度调整。那么此时，我们就需要对每一个像素点的数值进行增大或者缩小。如果我们把图片的所有像素值想象成我们上面处理的向量，利用CUDA就可以非常有效的进行加速**

![](E:/software/Typora/pictures/Pixel.jpg)

**如上图所示，我们只需要让每个线程来调整一个像素中数值即可调整整张图片的亮度和对比度**
**接下来执行下面的代码，完成调整图片亮度的例子：**

第一步，完成CUDA核函数

```python
import cv2
import numba
import time
import math

#GPU function
@cuda.jit
def process_gpu(img,rows,cols,channels):
    # 把所有线程看作布满一个二维平面，tx,ty对应唯一线程的索引，即某线程位于平面的第几行第几列。
    tx = cuda.blockIdx.x*cuda.blockDim.x+cuda.threadIdx.x    
    ty = cuda.blockIdx.y*cuda.blockDim.y+cuda.threadIdx.y
    if tx<rows and ty<cols:                             
        for c in range(channels):
            color = img[tx,ty][c]*2.0+30
            if color>255:
                img[tx,ty][c]=255
            elif color<0:
                img[tx,ty][c]=0
            else:
                img[tx,ty][c]=color
```

第二步，实现CPU端处理的代码

```python
#cpu function
def process_cpu(img):
    rows,cols,channels=img.shape
    for i in range(rows):
        for j in range(cols):
            for c in range(3):
                color=img[i,j][c]*2.0+30
                if color>255:
                    img[i,j][c]=255
                elif color<0:
                    img[i,j][c]=0
                else:
                    img[i,j][c]=color
```

第三步，定义main函数，利用opencv读取图片，并将它分别交给CPU和GPU进行处理

```python
def main_image_process():
    #Create an image.
    filename = 'test1.jpg'
    img = cv2.imread(filename)
    img2 = cv2.imread(filename)
    img = cv2.resize(img,(1000,1000),interpolation = cv2.INTER_AREA)
    img2 = cv2.resize(img,(1000,1000),interpolation = cv2.INTER_AREA)
    rows,cols,channels=img.shape
    dst_cpu = img.copy()
    dst_gpu = img.copy()
    start_cpu = time.time()
    process_cpu(img)
    end_cpu = time.time()
    time_cpu = (end_cpu-start_cpu)
    print("CPU process time: "+str(time_cpu))
    ##GPU function
    threadsperblock = (16,16)
    blockspergrid_x = int(math.ceil(rows/threadsperblock[0]))
    blockspergrid_y = int(math.ceil(cols/threadsperblock[1]))
    blockspergrid = (blockspergrid_x,blockspergrid_y)
    start_gpu = time.time()
    # 1）把输入数据从CPU内存复制到GPU显存；（先在CPU上进行数据预处理，GPU）
    dImg = cuda.to_device(img2)    
    cuda.synchronize()
    # 2）在执行芯片上缓存数据，加载GPU程序并执行；
    process_gpu[blockspergrid,threadsperblock](dImg,rows,cols,channels)
    cuda.synchronize()
    end_gpu = time.time()
    # 3）将计算结果从GPU显存中复制到CPU内存中；
    dst_gpu = dImg.copy_to_host()
    time_gpu = (end_gpu-start_gpu)
    print("GPU process time: "+str(time_gpu))
    #save
    cv2.imwrite("result_cpu.png",img)
    cv2.imwrite("result_gpu.png",dst_gpu)
    print("Done.")
   
```

第四步，执行，得到处理结果

```python
main_image_process()
```

我们很清楚的看到，完成同样的事情并得到相同的结果，CPU比GPU用了更多的时间



- 1）数据加载到CPU 内存上；
- 2）当使用GPU处理时，先要将数据从CPU 内存复杂到GPU显存 cuda.to_device(data)
- 3）在执行芯片上缓存数据，加载GPU程序并执行；
- 4）将计算结果从GPU显存中复制到CPU内存中；



# GPU计算性能

- GPU设备**单精度计算能力**的理论峰值计算公式：

  **单精度计算能力的峰值**=**单核单周期计算次数**×**处理核个数**×**主频**

  【例如】以GTX680为例，单核一个时钟周期单精度计算次数为：两次，处理核个数为：1536，主频为：1066MHZ；则其计算能力的峰值P=2×1536×1006MHZ=3090432MHZ

  1MH=1000000HZ，1T为1兆；上述`P=3.09TFLOPS`;即GTX680每秒可以进行超过3兆次的单精度运算。

  双精度的处理核为64个，P=2×64×1006MHZ=128768MHZ=0.13TFLOPS。即GTX680的双精度运算能力为0.13TFLOPS。

- 计算能力换算

  **理论峰值**=**GPU核心数量**×**GPU Boost主频**×**单个时钟周期内能处理的浮点计算次数**

  GPU里单精度和双精度的浮点计算能力需要分开计算，以TeslaP100为例：

  双精度理论峰值 = 1792×1.48GHZ×2=5.3TFLOPS

  单精度理论峰值 = 3584×1.48GHZ×2=10.6TFLOPS

  (GPU Boost主频此处为显存频率)