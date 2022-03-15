# PyTorch

```python
PyTorch由4个主要包装组成：
1.Torch：类似于Numpy的通用数组库，可以在将张量类型转换为（torch.cuda.TensorFloat）并在GPU上进行计算。
2.torch.autograd：用于构建计算图形并自动获取渐变的包
3.torch.nn：具有共同层和成本函数的神经网络库
4.torch.optim：具有通用优化算法（如SGD，Adam等）的优化包
```



##  `torch.utils.data`模块

对于 `torch.utils.data` 而言，重点是其 `Dataset`, `Sampler`, `DataLoader` 模块，辅以 `collate`, `fetch`, `pin_memory` 等组件对特定功能予以支持。

【1】**Dataset**

**Dataset** 负责对 raw data source 封装，将其封装成 Python 可识别的数据结构，其必须提供提取数据个体的接口。

Dataset 共有 Map-style datasets 和 Iterable-style datasets 两种

1.1 Map-style dataset

```
torch.utils.data.Dataset
```

它是一种通过实现 `__getitem__()` 和 `__len()__` 来获取数据的 Dataset，它表示从（可能是非整数）索引/关键字到数据样本的映射。访问时，这样的数据集用 `dataset[idx]` 访问 `idx` 对应的数据。

通常我们使用 Map-style 类型的 dataset 居多，其数据接口定义如下：

```python
class Dataset(Generic[T_co]):
    # Generic is an Abstract base class for generic types.

    def __getitem__(self, index) -> T_co:
        raise NotImplementedError

    def __add__(self, other: 'Dataset[T_co]') -> 'ConcatDataset[T_co]':
        return ConcatDataset([self, other])
```

PyTorch 中所有定义的 Dataset 都是其子类。

对于一般计算机视觉任务，我们通常会在其中进行一些 resize, crop, flip 等预处理的操作

值得一提的是，PyTorch 源码中并没有提供默认的 `__len__()` 方法实现，原因是 `return NotImplemented` 或者 `raise NotImplementedError()` 之类的默认实现都会存在各自的问题，这点在其源码中也有注释加以体现。

1.2 Iterable-style dataset

```
torch.utils.data.IterableDataset
```

它是一种实现 `__iter__()` 来获取数据的 Dataset，这种类型的数据集特别适用于以下情况：随机读取代价很大甚至不大可能，且 batch size 取决于获取的数据。其接口定义如下：

```python
class IterableDataset(Dataset[T_co]):

    def __iter__(self) -> Iterator[T_co]:
        raise NotImplementedError

    def __add__(self, other: Dataset[T_co]):
        return ChainDataset([self, other])
```

**特别地**，当 `DataLoader` 的 `num_workers > 0` 时， 每个 worker 都将具有数据对象的不同样本。因此需要独立地对每个副本进行配置，以防止每个 worker 产生的数据不重复。同时，数据加载顺序**完全由用户定义**的可迭代样式控制。这允许更容易地实现块读取和动态批次大小（例如，通过每次产生一个批次的样本）

1.3 其他Dataset

除了 Map-style dataset 和 Iterable-style dataset 以外，PyTorch 也在此基础上提供了其他类型的 Dataset 子类

- `torch.utils.data.ConcatDataset`: 用于连接多个 `ConcatDataset` 数据集
- `torch.utils.data.ChainDataset` : 用于连接多个 `IterableDataset` 数据集，在 `IterableDataset` 的 `__add__()` 方法中被调用
- `torch.utils.data.Subset`: 用于获取指定一个索引序列对应的子数据集

```python
class Subset(Dataset[T_co]):

    dataset: Dataset[T_co]
    indices: Sequence[int]

    def __init__(self, dataset: Dataset[T_co], indices: Sequence[int]) -> None:
        self.dataset = dataset
        self.indices = indices

    def __getitem__(self, idx):
        return self.dataset[self.indices[idx]]

    def __len__(self):
        return len(self.indices)
```

- `torch.utils.data.TensorDataset`: 用于获取封装成 tensor 的数据集，每一个样本都通过索引张量来获得。

```python
class TensorDataset(Dataset):
    def __init__(self, *tensor):
        assert all(tensors[0].size(0) == tensor.size(0) for tensor in tensors)
        self.tensors = tensors

    def __getitem__(self, index):
        return tuple(tensor[index] for tensor in tensors

    def __len__(self):
        return self.tensors[0].size(0)
```

【2】**Sampler**

`torch.utils.data.Sampler` 负责提供一种遍历数据集所有元素索引的方式。可支持用户自定义，也可以用 PyTorch 提供的，基类接口定义如下：

```python
lass Sampler(Generic[T_co]):
    r"""Base class for all Samplers.

    Every Sampler subclass has to provide an :meth:`__iter__` method, providing a
    way to iterate over indices of dataset elements, and a :meth:`__len__` method
    that returns the length of the returned iterators.

    .. note:: The :meth:`__len__` method isn't strictly required by
              :class:`~torch.utils.data.DataLoader`, but is expected in any
              calculation involving the length of a :class:`~torch.utils.data.DataLoader`.
    """

    def __init__(self, data_source: Optional[Sized]) -> None:
        pass

    def __iter__(self) -> Iterator[T_co]:
        raise NotImplementedError
```

**特别地**，`__len__()` 方法不是必要的，但是当 DataLoader 需要计算 `len()` 的时候必须定义，这点在其源码中也有注释加以体现。

同样，PyTorch 也在此基础上提供了其他类型的 Sampler 子类

- `torch.utils.data.SequentialSampler` : 顺序采样样本，始终按照同一个顺序
- `torch.utils.data.RandomSampler`: 可指定有无放回地，进行随机采样样本元素
- `torch.utils.data.SubsetRandomSampler`: 无放回地按照给定的索引列表采样样本元素
- `torch.utils.data.WeightedRandomSampler`: 按照给定的概率来采样样本。样本元素来自 `[0,…,len(weights)-1]` ， 给定概率（权重）
- `torch.utils.data.BatchSampler`: 在一个batch中封装一个其他的采样器, 返回一个 batch 大小的 index 索引
- `torch.utils.data.DistributedSample`: 将数据加载限制为数据集子集的采样器。与 `torch.nn.parallel.DistributedDataParallel` 结合使用。 在这种情况下，每个进程都可以将 `DistributedSampler` 实例作为 `DataLoader` 采样器传递

【3】**DataLoader**

`torch.utils.data.DataLoader` 是 PyTorch 数据加载的核心，负责加载数据，同时支持 Map-style 和 Iterable-style Dataset，支持单进程/多进程，还可以设置 loading order, batch size, pin memory 等加载参数。其接口定义如下：

```python
DataLoader(dataset, batch_size=1, shuffle=False, sampler=None,
           batch_sampler=None, num_workers=0, collate_fn=None,
           pin_memory=False, drop_last=False, timeout=0,
           worker_init_fn=None, *, prefetch_factor=2,
           persistent_workers=False)
```

对于每个参数的含义，以下给出一个表格进行对应介绍：

| attribute          | meaning                                                      | default value | type              |
| ------------------ | ------------------------------------------------------------ | ------------- | ----------------- |
| dataset            | 加载数据的数据集                                             |               | Dataset           |
| batch_size         | 每个 batch 加载多少个样本                                    | 1             | int               |
| shuffle            | 设置为 True 时，调用 RandomSampler 进行随机索引              | False         | bool              |
| sampler            | 定义从数据集中提取样本的策略 如果指定了, shuffle 参数必须为 False，(否则会和 RandomSampler 互斥) | None          | Sampler, Iterable |
| batch_sampler      | 和 sampler 类似，但是一般传入 BatchSampler，每次返回一个 batch 大小的索引 其和 batch_size, shuffle 等参数是互斥的 | None          | Sampler, Iterable |
| num_workers        | 要用于数据加载的子进程数，0 表示将在主进程中加载数据         | 0             | int               |
| collate_fn         | 在将 Map-style datase t 取出的数据整合成 batch 时使用，合并样本列表以形成一个 batch | None          | callable          |
| pin_memory         | 如果为 True，则 DataLoader 在将张量返回之前将其复制到 CUDA 固定的内存中 | False         | bool              |
| drop_last          | 设置为 True 删除最后一个不完整的批次，如果该数据集大小不能被该批次大小整除。 如果 False 并且数据集的大小不能被批次大小整除，那么最后一批将较小 | False         | bool              |
| timeout            | 如果为正，则为从 worker 收集 batch 的超时值，应始终为非负数 超过这个时间还没读取到数据的话就会报错 | 0             | numeric           |
| worker_init_fn     | 如果不为 None，它将会被每个 worker 子进程调用， 以 worker id ([0, num_workers - 1] 内的整形) 为输入 | None          | callable          |
| prefetch_factor    | 每个 worker 提前加载 的 sample 数量                          | 2             | int               |
| persistent_workers | 如果为 True，dataloader 将不会终止 worker 进程，直到 dataset 迭代完成 | False         | bool              |

从参数定义中，我们可以看到 DataLoader 主要支持以下几个功能

- 支持加载 `map-style` 和 `iterable-style` 的 dataset，主要涉及到的参数是 `dataset`
- 自定义数据加载顺序，主要涉及到的参数有 `shuffle`, `sampler`, `batch_sampler`, `collate_fn`
- 自动把数据整理成batch序列，主要涉及到的参数有 `batch_size`, `batch_sampler`, `collate_fn`, `drop_last`
- 单进程和多进程的数据加载，主要涉及到的参数有 `num_workers`, `worker_init_fn`
- 自动进行锁页内存读取 (memory pinning)，主要涉及到的参数 `pin_memory`
- 支持数据预加载

### 3.1 三者关系 (Dataset, Sampler, Dataloader)

通过以上介绍的三者工作内容不难推出其内在关系：

1. 设置 Dataset，将数据 data source 包装成 Dataset 类，暴露提取接口。
2. 设置 Sampler，决定采样方式。我们是能从 Dataset 中提取元素了，还是需要设置 Sampler 告诉程序提取 Dataset 的策略。
3. 将设置好的 Dataset 和 Sampler 传入 DataLoader，同时可以设置 `shuffle`, `batch_size` 等参数。使用 DataLoader 对象可以方便快捷地在数据集上遍历。

总结来说，即 Dataloader 负责总的调度，命令 Sampler 定义遍历索引的方式，然后用索引去 Dataset 中提取元素。于是就实现了对给定数据集的遍历。

### 3.2 批处理

### 3.2.1 自动批处理（默认）

`DataLoader` 支持通过参数`batch_size`, `drop_last`, `batch_sampler`，自动地把取出的数据整理 (collate) 成批次样本 (batch)

`batch_size` 和 `drop_last` 参数用于指定 DataLoader 如何获取 dataset 的 key。特别地，对于 map-style 类型的 dataset，用户可以选择指定 `batch_sample`参数，一次就生成一个 keys list

在使用 sampler 产生的 indices 获取采样到的数据时，DataLoader 使用 `collate_fn` 参数将样本列表整理成 batch。抽象这个过程，其表示方式大致如下

```python
# For Map-style
for indices in batch_sampler:
    yield collate_fn([dataset[i] for i in indices])

# For Iterable-style
dataset_iter = iter(dataset)
for indices in batch_sampler:
    yield collate_fn([next(dataset_iter) for _ in indices])
```

### 3.2.2 关闭自动批处理

当用户想用 dataset 代码手动处理 batch，或仅加载单个 sample data 时，可将 `batch_size` 和 `batch_sampler` 设为 `None`, 将关闭自动批处理。此时，由 `Dataset` 产生的 sample 将会直接被 `collate_fn` 处理。抽象这个过程，其表示方式大致如下：

```python
# For Map-style
for index in sampler:
    yield collate_fn(dataset[index])

# For Iterable-style
for data in iter(dataset):
    yield collate_fn(data)
```

### 3.2.3 collate_fn

当关闭自动批处理 (automatic batching) 时，`collate_fn` 作用于单个数据样本，只是在 PyTorch 张量中转换 NumPy 数组。

当开启自动批处理 (automatic batching) 时，`collate_fn` 作用于数据样本列表，将输入样本整理为一个 batch，一般做下面 3 件事情

- 添加新的批次维度（一般是第一维）
- 它会自动将 NumPy 数组和 Python 数值转换为 PyTorch 张量
- 它保留数据结构，例如，如果每个样本都是 `dict`，则输出具有相同键集但批处理过的张量作为值的字典（或list，当不能转换的时候）。`list`, `tuples`, `namedtuples` 同样适用

自定义 `collate_fn` 可用于自定义排序规则，例如，将顺序数据填充到批处理的最大长度，添加对自定义数据类型的支持等。

### 3.3 多进程处理 (multi-process)

为了避免在加载数据时阻塞计算代码，PyTorch 提供了一个简单的开关，只需将参数设置 `num_workers` 为正整数即可执行多进程数据加载，设置为 0 时执行单线程数据加载。

## 4. 单进程

在单进程模式下，`DataLoader` 初始化的进程和取数据的进程是一样的 。因此，数据加载可能会阻止计算。

但是，当用于在进程之间共享数据的资源（例如共享内存，文件描述符）有限时，或者当整个数据集很小并且可以完全加载到内存中时，此模式可能是首选。

此外，单进程加载通常显示更多可读的错误跟踪，因此**对于调试很有用**。

## 5. 多进程

在多进程模式下，每次 `DataLoader` 创建 iterator 时（例如，当调用时`enumerate(dataloader)`），都会创建 `num_workers` 工作进程。`dataset`, `collate_fn`, `worker_init_fn` 都会被传到每个worker中，每个worker都用独立的进程。

对于 map-style 数据，主线程会用 Sampler 产生 indice，并将它们送到 worker 里。因此，shuffle是在主线程做的

对于 iterable-style 数据，因为每个 worker 都有相同的 data 复制样本，并在各个进程里进行不同的操作，以防止每个进程输出的数据是重复的，所以一般用 `torch.utils.data.get_worker_info()` 来进行辅助处理。

这里，`torch.utils.data.get_worker_info()` 返回worker进程的一些信息（`id`, `dataset`, `num_workers`, `seed`），如果在主线程跑的话返回None

**注意**，通常不建议在多进程加载中返回CUDA张量，因为在使用CUDA和在多处理中共享CUDA张量时存在许多微妙之处（文档中提出：只要接收过程保留张量的副本，就需要发送过程来保留原始张量）。建议采用 `pin_memory=True` ，以将数据快速传输到支持CUDA的GPU。简而言之，**不建议在使用多线程的情况下返回CUDA的tensor**。

## 6 锁页内存 (Memory Pinning)

这里首先解释一下锁页内存的概念。

主机中的内存，有两种存在方式，一是锁页，二是不锁页，锁页内存存放的内容在任何情况下都不会与主机的虚拟内存进行交换（注：虚拟内存就是硬盘），而不锁页内存在主机内存不足时，数据会存放在虚拟内存中。主机到GPU副本源自固定（页面锁定）内存时，速度要快得多。CPU张量和存储暴露了一种 `pin_memory()` 方法，该方法返回对象的副本，并将数据放在固定的区域中。

而**显卡中的显存全部是锁页内存！当计算机的内存充足的时候，可以设置 `pin_memory=True`。**设置 `pin_memory=True`，则意味着生成的 Tensor 数据最开始是属于内存中的锁页内存，这样将内存的Tensor转义到GPU的显存就会更快一些。同时，由于 `pin_memory` 的作用是将张量返回之前将其复制到 CUDA 固定的内存中，所以只有在 CUDA 环境支持下才有用。

PyTorch 原生的 `pin_memory` 方法如下，其支持大部分 python 数据类型的处理：

```python
def pin_memory(data):
    if isinstance(data, torch.Tensor):
        return data.pin_memory()
    elif isinstance(data, string_classes):
        return data
    elif isinstance(data, container_abcs.Mapping):
        return {k: pin_memory(sample) for k, sample in data.items()}
    elif isinstance(data, tuple) and hasattr(data, '_fields'):  # namedtuple
        return type(data)(*(pin_memory(sample) for sample in data))
    elif isinstance(data, container_abcs.Sequence):
        return [pin_memory(sample) for sample in data]
    elif hasattr(data, "pin_memory"):
        return data.pin_memory()
    else:
        return data
```

默认情况下，如果固定逻辑看到一个属于自定义类型 (custom type) 的batch（如果有一个 `collate_fn` 返回自定义批处理类型的批处理，则会发生），或者如果该批处理的每个元素都是 custom type，则固定逻辑将无法识别它们，它将返回该批处理（或那些元素）而无需固定内存。要为自定义批处理或数据类型启用内存固定，需 `pin_memory()` 在自定义类型上定义一个方法。如下

```python
class SimpleCustomBatch:
    # 自定义一个类，该类不能被PyTorch原生的pin_memory方法所支持

    def __init__(self, data):
        transposed_data = list(zip(*data))
        self.inp = torch.stack(transposed_data[0], 0)
        self.tgt = torch.stack(transposed_data[1], 0)

    # custom memory pinning method on custom type
    def pin_memory(self):
        self.inp = self.inp.pin_memory()
        self.tgt = self.tgt.pin_memory()
        return self

def collate_wrapper(batch):
    return SimpleCustomBatch(batch)

inps = torch.arange(10 * 5, dtype=torch.float32).view(10, 5)
tgts = torch.arange(10 * 5, dtype=torch.float32).view(10, 5)
dataset = TensorDataset(inps, tgts)

loader = DataLoader(dataset, batch_size=2, collate_fn=collate_wrapper,
                    pin_memory=True)

for batch_ndx, sample in enumerate(loader):
    print(sample.inp.is_pinned())  # True
    print(sample.tgt.is_pinned())  # True
```

## 7 预取 (prefetch)

DataLoader 通过指定 `prefetch_factor` （默认为 2）来进行数据的预取。

```python
class _MultiProcessingDataLoaderIter(_BaseDataLoaderIter):
    def __init__(self, loader):
        ...
        self._reset(loader, first_iter=True)

    def _reset(self, loader, first_iter=False):
        ...
        # prime the prefetch loop
        for _ in range(self._prefetch_factor * self._num_workers):
            self._try_put_index()
```

通过源码可以看到，prefetch 功能仅适用于 多进程 加载中（下面会由多进程 dataloader 的代码分析）

## 8 代码详解

让我们来看看具体的代码调用流程：

```python
for data, label in train_loader:
    ......
```

for 循环会调用 dataloader 的 `__iter__(self)` 方法，以此获得迭代器来遍历 dataset

```python
class DataLoader(Generic[T_co]):
    ...
    def __iter__(self) -> '_BaseDataLoaderIter':

        if self.persistent_workers and self.num_workers > 0:
            if self._iterator is None:
                self._iterator = self._get_iterator()
            else:
                self._iterator._reset(self)
            return self._iterator
        else:
            return self._get_iterator()
```

在 `__iter__(self)` 方法中，dataloader 调用了 `self._get_iterator()` 方法，根据 `num_worker` 获得迭代器，并指示进行单进程还是多进程

```python
class DataLoader(Generic[T_co]):
    ...
    def _get_iterator(self) -> '_BaseDataLoaderIter':
        if self.num_workers == 0:
            return _SingleProcessDataLoaderIter(self)
        else:
            self.check_worker_number_rationality()
            return _MultiProcessingDataLoaderIter(self)
```

为了描述清晰，我们只考虑单进程的代码。下面是 `class _SingleProcessDataLoaderIter(_BaseDataLoaderIter)` ，以及其父类 `class _BaseDataLoaderIter(object):` 的重点代码片段：

```python
class _BaseDataLoaderIter(object):
    def __init__(self, loader: DataLoader) -> None:
        # 初始化赋值一些 DataLoader 参数，
        # 以及用户输入合法性进行校验
        self._dataset = loader.dataset
        self._dataset_kind = loader._dataset_kind
        self._index_sampler = loader._index_sampler
        ...

    def __iter__(self) -> '_BaseDataLoaderIter':
        return self

    def _reset(self, loader, first_iter=False):
        self._sampler_iter = iter(self._index_sampler)
        self._num_yielded = 0
        self._IterableDataset_len_called = loader._IterableDataset_len_called

    def _next_index(self):
        return next(self._sampler_iter)  # may raise StopIteration

    def _next_data(self):
        raise NotImplementedError

    def __next__(self) -> Any:
        with torch.autograd.profiler.record_function(self._profile_name):
            if self._sampler_iter is None:
                self._reset()
            data = self._next_data() # 重点代码行，通过此获取数据
            self._num_yielded += 1
            ...
            return data

    next = __next__  # Python 2 compatibility

    def __len__(self) -> int:
        return len(self._index_sampler) # len(_BaseDataLoaderIter) == len(self._index_sampler)

    def __getstate__(self):
        raise NotImplementedError("{} cannot be pickled", self.__class__.__name__)
```

`_BaseDataLoaderIter` 是所有 `DataLoaderIter` 的父类。dataloader获得了迭代器之后，for 循环需要调用 `__next__()` 来获得下一个对象，从而实现遍历。通过 `__next__` 方法调用 `_next_data()` 获取数据

```python
class _SingleProcessDataLoaderIter(_BaseDataLoaderIter):
    def __init__(self, loader):
        super(_SingleProcessDataLoaderIter, self).__init__(loader)
        assert self._timeout == 0
        assert self._num_workers == 0

        self._dataset_fetcher = _DatasetKind.create_fetcher(
            self._dataset_kind, self._dataset, self._auto_collation, self._collate_fn, self._drop_last)

    def _next_data(self):
        index = self._next_index()  # may raise StopIteration
        data = self._dataset_fetcher.fetch(index)  # may raise StopIteration
        if self._pin_memory:
            data = _utils.pin_memory.pin_memory(data)
        return data
```

从 `_SingleProcessDataLoaderIter` 的初始化参数可以看到，其在父类 `_BaseDataLoaderIter` 的基础上定义了 `_dataset_fetcher`, 并传入 `_dataset`, `_auto_collation`, `_collate_fn` 等参数，用于定义获取数据的方式。其具体实现会在稍后解释。

在 `_next_data()` 被调用后，其需要 `next_index()` 获取 index，并通过获得的 index 传入 `_dataset_fetcher` 中获取对应样本

```python
class DataLoader(Generic[T_co]):
    ...
    @property
    def _auto_collation(self):
        return self.batch_sampler is not None

    @property
    def _index_sampler(self):
        if self._auto_collation:
            return self.batch_sampler
        else:
            return self.sampler

class _BaseDataLoaderIter(object):
    ...
    def _reset(self, loader, first_iter=False):
        self._sampler_iter = iter(self._index_sampler)
        ...

    def _next_index(self):
        # sampler_iter 来自于 index_sampler
        return next(self._sampler_iter)  # may raise StopIteration
```

从这里看出，dataloader 提供了 sampler (可以是batch_sampler 或者是其他 sampler 子类)，然后 `_SingleProcessDataLoaderIter` 迭代sampler获得索引

下面我们来看看 fetcher，fetcher 需要 index 来获取元素，并同时支持 Map-style dataset（对应 `_MapDatasetFetcher`）和 Iterable-style dataset（对应 `_IterableDatasetFetcher`），使其在Dataloader内能使用相同的接口 fetch，代码更加简洁。

- 对于 Map-style：直接输入索引 index，作为 map 的 key，获得对应的样本（即 value）

```python
class _MapDatasetFetcher(_BaseDatasetFetcher):
    def __init__(self, dataset, auto_collation, collate_fn, drop_last):
        super(_MapDatasetFetcher, self).__init__(dataset, auto_collation, collate_fn, drop_last)

    def fetch(self, possibly_batched_index):
        if self.auto_collation:
            # 有batch_sampler，_auto_collation就为True，
            # 就优先使用batch_sampler，对应在fetcher中传入的就是一个batch的索引
            data = [self.dataset[idx] for idx in possibly_batched_index]
        else:
            data = self.dataset[possibly_batched_index]
        return self.collate_fn(data)
```

- 对于 Iterable-style: `__init__` 方法内设置了 dataset 初始的迭代器，fetch 方法内获取元素，index 其实已经没有多大作用了

```python
class _IterableDatasetFetcher(_BaseDatasetFetcher):
    def __init__(self, dataset, auto_collation, collate_fn, drop_last):
        super(_IterableDatasetFetcher, self).__init__(dataset, auto_collation, collate_fn, drop_last)
        self.dataset_iter = iter(dataset)

    def fetch(self, possibly_batched_index):
        if self.auto_collation:
            # 对于batch_sampler（即auto_collation==True）
            # 直接使用往后遍历并提取len(possibly_batched_index)个样本（即1个batch的样本）
            data = []
            for _ in possibly_batched_index:
                try:
                    data.append(next(self.dataset_iter))
                except StopIteration:
                    break
            if len(data) == 0 or (self.drop_last and len(data) < len(possibly_batched_index)):
                raise StopIteration
        else:
            # 对于sampler，直接往后遍历并提取1个样本
            data = next(self.dataset_iter)
        return self.collate_fn(data)
```

最后，我们通过索引传入 fetcher，fetch 得到想要的样本

因此，**整个过程调用关系总结** 如下：

`loader.__iter__` --> `self._get_iterator()` --> `class _SingleProcessDataLoaderIter` --> `class _BaseDataLoaderIter` --> `__next__()` --> `self._next_data()` --> `self._next_index()` -->`next(self._sampler_iter)` 即 `next(iter(self._index_sampler))` --> 获得 index --> `self._dataset_fetcher.fetch(index)` --> 获得 data

对于多进程而言，借用 PyTorch 内源码的注释，其运行流程解释如下

```python
# Our data model looks like this (queues are indicated with curly brackets):
#
#                main process                              ||
#                     |                                    ||
#               {index_queue}                              ||
#                     |                                    ||
#              worker processes                            ||     DATA
#                     |                                    ||
#            {worker_result_queue}                         ||     FLOW
#                     |                                    ||
#      pin_memory_thread of main process                   ||   DIRECTION
#                     |                                    ||
#               {data_queue}                               ||
#                     |                                    ||
#                data output                               \/
#
# P.S. `worker_result_queue` and `pin_memory_thread` part may be omitted if
#      `pin_memory=False`.
```

首先 dataloader 基于 multiprocessing 产生多进程，每个子进程的输入输出通过两个主要的队列 (`multiprocessing.Queue()` 类) 产生，分别为：

- `index_queue`: 每个子进程的队列中需要处理的任务的下标
- `_worker_result_queue`: 返回时处理完任务的下标
- `data_queue`: 表明经过 pin_memory 处理后的数据队列

并且有以下这些比较重要的 flag 参数来协调各个 worker 之间的工作：

- `_send_idx`: 发送索引，用来记录这次要放 index_queue 中 batch 的 idx
- `_rcvd_idx`: 接受索引，记录要从 data_queue 中取出的 batch 的 idx
- `_task_info`: 存储将要产生的 data 信息的 dict，key为 task idx（由 0 开始的整形索引），value 为 `(worker_id,)` 或 `(worker_id, data)`，分别对应数据 未取 和 已取 的情况
- `_tasks_outstanding`: 整形，代表已经准备好的 task/batch 的数量（可能有些正在准备中）

每个 worker 一次产生一个 batch 的数据，返回 batch 数据前放入下一个批次要处理的数据下标，对应构造函数子进程初始化如下

```python
class _MultiProcessingDataLoaderIter(_BaseDataLoaderIter):
    def __init__(self, loader):
        super(_MultiProcessingDataLoaderIter, self).__init__(loader)
        ...
        self._worker_result_queue = multiprocessing_context.Queue()  # 把该worker取出的数放入该队列，用于进程间通信
        ...
        self._workers_done_event = multiprocessing_context.Event()

        self._index_queues = []
        self._workers = []
        for i in range(self._num_workers):
            index_queue = multiprocessing_context.Queue()  # 索引队列，每个子进程一个队列放要处理的下标
            index_queue.cancel_join_thread()
            # _worker_loop 的作用是：从index_queue中取索引，然后通过collate_fn处理数据，
            # 然后再将处理好的 batch 数据放到 data_queue 中。（发送到队列中的idx是self.send_idx）
            w = multiprocessing_context.Process(
                target=_utils.worker._worker_loop,  # 每个worker子进程循环执行的函数，主要将数据以(idx, data)的方式传入_worker_result_queue中
                args=(self._dataset_kind, self._dataset, index_queue, 
                      self._worker_result_queue, self._workers_done_event,
                      self._auto_collation, self._collate_fn, self._drop_last,
                      self._base_seed + i, self._worker_init_fn, i, self._num_workers,
                      self._persistent_workers))
            w.daemon = True
            w.start()
            self._index_queues.append(index_queue)
            self._workers.append(w)

        if self._pin_memory:
            self._pin_memory_thread_done_event = threading.Event()

            self._data_queue = queue.Queue()  # 用于存取出的数据进行 pin_memory 操作后的结果
            pin_memory_thread = threading.Thread(
                target=_utils.pin_memory._pin_memory_loop,
                args=(self._worker_result_queue, self._data_queue,
                      torch.cuda.current_device(),
                      self._pin_memory_thread_done_event))
            pin_memory_thread.daemon = True
            pin_memory_thread.start()
            # Similar to workers (see comment above), we only register
            # pin_memory_thread once it is started.
            self._pin_memory_thread = pin_memory_thread
        else:
            self._data_queue = self._worker_result_queue
        ...
        self._reset(loader, first_iter=True)

    def _reset(self, loader, first_iter=False):
        super()._reset(loader, first_iter)
        self._send_idx = 0  # idx of the next task to be sent to workers，发送索引，用来记录这次要放 index_queue 中 batch 的 idx
        self._rcvd_idx = 0  # idx of the next task to be returned in __next__，接受索引，记录要从 data_queue 中取出的 batch 的 idx

        # information about data not yet yielded, i.e., tasks w/ indices in range [rcvd_idx, send_idx).
        # map: task idx => - (worker_id,)        if data isn't fetched (outstanding)
        #                  \ (worker_id, data)   if data is already fetched (out-of-order)
        self._task_info = {}

        # _tasks_outstanding 指示当前已经准备好的 task/batch 的数量（可能有些正在准备中）
        # 初始值为 0, 在 self._try_put_index() 中 +1,在 self._next_data 中-1
        self._tasks_outstanding = 0  # always equal to count(v for v in task_info.values() if len(v) == 1)

        # this indicates status that a worker still has work to do *for this epoch*.
        self._workers_status = [True for i in range(self._num_workers)] 
        # We resume the prefetching in case it was enabled
        if not first_iter:
            for idx in range(self._num_workers):
                self._index_queues[idx].put(_utils.worker._ResumeIteration())
            resume_iteration_cnt = self._num_workers
            while resume_iteration_cnt > 0:
                data = self._get_data()
                if isinstance(data, _utils.worker._ResumeIteration):
                    resume_iteration_cnt -= 1
        ...
        # 初始化的时候，就将 2*num_workers 个 (batch_idx, sampler_indices) 放到 index_queue 中
        for _ in range(self._prefetch_factor * self._num_workers):
            self._try_put_index() # 进行预取
```

dataloader 初始化的时候，每个 worker 的 index_queue 默认会放入**两个** batch 的 index，从 `index_queue` 中取出要处理的下标

```python
def _try_put_index(self):
        # self._prefetch_factor 默认为 2
        assert self._tasks_outstanding < self._prefetch_factor * self._num_workers

        try:
            index = self._next_index()
        except StopIteration:
            return
        for _ in range(self._num_workers):  # find the next active worker, if any
            worker_queue_idx = next(self._worker_queue_idx_cycle)
            if self._workers_status[worker_queue_idx]:
                break
        else:
            # not found (i.e., didn't break)
            return

        self._index_queues[worker_queue_idx].put((self._send_idx, index)) # 放入 任务下标 和 数据下标
        self._task_info[self._send_idx] = (worker_queue_idx,)
        # _tasks_outstanding + 1，表明预备好的batch个数+1
        self._tasks_outstanding += 1
        # send_idx 发送索引, 记录从sample_iter中发送索引到index_queue的次数
        self._send_idx += 1
```

调用 `_next_data` 方法进行数据读取，其中 `_process_data` 用于返回数据

```python
def _next_data(self):
        while True:

            while self._rcvd_idx < self._send_idx: # 确保待处理的任务(待取的batch)下标 > 处理完毕要返回的任务(已经取完的batch)下标
                info = self._task_info[self._rcvd_idx]
                worker_id = info[0]
                if len(info) == 2 or self._workers_status[worker_id]:  # has data or is still active
                    break
                del self._task_info[self._rcvd_idx]
                self._rcvd_idx += 1
            else:
                # no valid `self._rcvd_idx` is found (i.e., didn't break)
                if not self._persistent_workers:
                    self._shutdown_workers()
                raise StopIteration

            # Now `self._rcvd_idx` is the batch index we want to fetch

            # Check if the next sample has already been generated
            if len(self._task_info[self._rcvd_idx]) == 2:
                data = self._task_info.pop(self._rcvd_idx)[1]
                return self._process_data(data)

            assert not self._shutdown and self._tasks_outstanding > 0
            idx, data = self._get_data() # 调用 self._try_get_data() 从 self._data_queue 中取数
            self._tasks_outstanding -= 1  # 表明预备好的batch个数需要减1
            if self._dataset_kind == _DatasetKind.Iterable:
                # Check for _IterableDatasetStopIteration
                if isinstance(data, _utils.worker._IterableDatasetStopIteration):
                    if self._persistent_workers:
                        self._workers_status[data.worker_id] = False
                    else:
                        self._mark_worker_as_unavailable(data.worker_id)
                    self._try_put_index()
                    continue

            if idx != self._rcvd_idx:
                # store out-of-order samples
                self._task_info[idx] += (data,)
            else:
                del self._task_info[idx]
                return self._process_data(data) # 返回数据

    def _process_data(self, data):
        self._rcvd_idx += 1
        self._try_put_index() # 同上，主要放入队列索引 以及 更新flag
        if isinstance(data, ExceptionWrapper):
            data.reraise()
        return data
```

这样，多线程的 dataloader 就能通过多个 worker 的协作来共同完成数据的加载。

## 参考

- [https://pytorch.org/docs/stable/data.html](https://link.zhihu.com/?target=https%3A//pytorch.org/docs/stable/data.html)
- https://www.zhihu.com/search?type=content&q=dataloader
- [https://www.dazhuanlan.com/2019/12/05/5de8104ce9491/](https://link.zhihu.com/?target=https%3A//www.dazhuanlan.com/2019/12/05/5de8104ce9491/)
- [https://blog.csdn.net/g11d111/article/details/81504637](https://link.zhihu.com/?target=https%3A//blog.csdn.net/g11d111/article/details/81504637)



快速链接：

[OpenMMLab：PyTorch 源码解读系列](https://zhuanlan.zhihu.com/p/328674159)

[OpenMMLab：PyTorch 源码解读之 torch.autograd：梯度计算详解](https://zhuanlan.zhihu.com/p/321449610)

[OpenMMLab：PyTorch 源码解读之 BN & SyncBN：BN 与 多卡同步 BN 详解](https://zhuanlan.zhihu.com/p/337732517)

[OpenMMLab：PyTorch 源码解读之 torch.utils.data：解析数据处理全流程](https://zhuanlan.zhihu.com/p/337850513)

[OpenMMLab：PyTorch 源码解读之 nn.Module：核心网络模块接口详解](https://zhuanlan.zhihu.com/p/340453841)

[OpenMMLab：PyTorch 源码解读之 DP & DDP：模型并行和分布式训练解析](https://zhuanlan.zhihu.com/p/343951042)

[OpenMMLab：PyTorch 源码解读之 torch.optim：优化算法接口详解](https://zhuanlan.zhihu.com/p/346205754)

[OpenMMLab：PyTorch 源码解读之 torch.cuda.amp: 自动混合精度详解](https://zhuanlan.zhihu.com/p/348554267)

[OpenMMLab：PyTorch 源码解读之 cpp_extension：揭秘 C++/CUDA 算子实现和调用全流程](https://zhuanlan.zhihu.com/p/348555597)



## Variable 变量

即可以变化的量，区别于int变量，Variable是一种可以变化的变量，这正好就符合了**反向传播**，参数更新的属性。

在pytorch中，Variable就是一个存放会变化值的地理位置，里面的值会不停的发生变化，就像装鸡蛋的篮子，鸡蛋数(值)会不断发生变化。鸡蛋=pytorch中的tensor。**pytorch都是tensor计算的，而tensor里面的参数都是Variable的形式**

```python
import torch
from torch.autograd import Variable # torch 中 Variable 模块
tensor = torch.FloatTensor([[1,2],[3,4]])
# 把鸡蛋放到篮子里, requires_grad是参不参与误差反向传播, 要不要计算梯度
variable = Variable(tensor, requires_grad=True)
 
print(tensor)
"""
 1 2
 3 4
[torch.FloatTensor of size 2x2]
"""
 
print(variable)
"""
Variable containing:
 1 2
 3 4
[torch.FloatTensor of size 2x2]
"""
# 注：tensor不能反向传播，variable可以反向传播。
```

- Variable求梯度

Variable计算时，它会逐渐地生成计算图。这个图就是将所有的计算节点都连接起来，最后进行误差反向传递的时候，一次性将所有Variable里面的梯度都计算出来，而tensor就没有这个能力。

```python
v_out.backward() # 模拟 v_out 的误差反向传递

print(variable.grad) # 初始 Variable 的梯度
'''
 0.5000 1.0000
 1.5000 2.0000
'''
```

- 获取Variable里面的数据

直接print(Variable) 只会输出Variable形式的数据，在很多时候是用不了的。所以需要转换一下，将其变成tensor形式。

```python
print(variable)  # Variable 形式
"""
Variable containing:
 1 2
 3 4
[torch.FloatTensor of size 2x2]
"""
 
print(variable.data) # 将variable形式转为tensor 形式
"""
 1 2
 3 4
[torch.FloatTensor of size 2x2]
"""
 
print(variable.data.numpy()) # numpy 形式
"""
[[ 1. 2.]
 [ 3. 4.]]
"""
```

- torch.squeeze( ) 对数据的维度进行压缩

  去掉维数为1的维度，如一个一行三列（1,3）的数去掉第一个维数为1的维度之后变成了(3）行。

  - squeeze(a)	将a中所有为1的维度删掉。不为1的维度没有影响。
  - squeeze(a, N)   去掉a中指定的维数为1的维度。

- torch.unsqueeze( ) 对数据维度进行扩充

  给指定位置加上维数为1的维度，比如原本有个三行的数据（3），在0的位置加了一维就变成一行三列（1,3）。

  - a.unsqueeze(N) 就是在a中指定位置N加上一个维数为1的维度。
  - b=torch.unsqueeze(a，N) a就是在a中指定位置N加上一个维数为1的维度。

```python
```

## torch.nn.Module

相当于对网络某种层的封装，包括网络结构以及网络参数和一些操作；

torch.nn.Module是所有神经网络单元的基类。

在__ init __构造函数中申明各个层的定义，在forward中实现层之间的连接关系，实际上就是前向传播的过程。

- 如何定义自己的网络？

  【1】需要继承nn.Module类，并实现forward方法。继承nn.Module类之后，在构造函数中要调用Module的构造函数，super(Linear, self).__ init__ ()

  【2】一般把网络中具有可学习参数的层放在构造函数__ init__( )中。

  【3】不具有可学习参数的层(如ReLU)可放在构造函数中，也可不放在构造函数中(而在forward中使用nn.functional来代替)。可学习参数放在构造函数中，并且通过nn.Parameter( )使参数以parameters(一种tensor，默认是自动求导)的形式存在Module中，并且通过parameters( )或者named_parameters( )以迭代器的方式返回可学习参数。

  【4】只要在nn.Module中定义了forward函数，backward函数就会被自动实现(利用Autograd)。而且一般不是显示的调用forward(layer.forward),而是layer(input),会自执行forward( )。

  【5】在forward中可以使用任何Variable支持的函数，毕竟在整个pytorch构建的图中，是Variable在流动。还可以使用if, for, print, log 等python语法。

```python
import torch
import torch.nn.functional as F
# method1--搭建网络
class Net(torch.nn.Module):
    def __init__(self, n_feature, n_hidden, n_output):
        super(Net, self).__init__()
        # 把网络中具有可学习参数的层放入构造函数————init__（）中————隐藏层、输出层显然有可学习的参数
        self.hidden = torch.nn.Linear(n_feature, n_hidden)
        self.predict = torch.nn.Linear(n_hidden, n_output)
        pass
    def forward(self):
        x= F.relu(self.hidden(x))
        x = self.predict(x)
        return x
        pass
# 实例化
net1 = Net(2, 10, 2)

# method2--快速搭建网络
net2 = torch.nn.Sequential(
    torch.nn.Linear(2, 10),
    # 激励层
    torch.nn.ReLU(),
    torch.nn.Linear(10, 2),
)
```

## torch.nn.sequential

一个顺序容器。神经网络模块将按照在传入构造器中的顺序依次被添加到计算图中执行。同时，以神经网络模块为元素的有序字典也可以作为传入参数。

```python
# Example of using Sequential
    model = nn.Sequential(
        nn.Conv2d(1,20,5),
        nn.ReLU(),
        nn.Conv2d(20,64,5),
        nn.ReLU()
    )

# Example of using Sequential with OrderedDict
    model = nn.Sequential(OrderedDict([
        ('conv1', nn.Conv2d(1,20,5)),
        ('relu1', nn.ReLU()),
        ('conv2', nn.Conv2d(20,64,5)),
        ('relu2', nn.ReLU())
    ]))

```

## 保存和提取神经网络

```python
def save():
    # save net1
    net1 = torch.nn.Sequential(
        torch.nn.Linear(1, 10),
        torch.nn.ReLU(),
        torch.nn.Linear(10, 1)
    )
    optimizer = torch.optim.SGD(net1.parameters(), lr=0.5)    # 训练优化所有参数
    loss_func = torch.nn.MSELoss()
    
    for t in range(100):    # 训练100次
        prediction = net1(x)
        loss = loss_func(prediction, y)
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()
    
    torch.save(net1, 'net.pkl')    # 保存整个网络
    torch.save(net1.state_dict(), 'net_params.pkl')    # 保存网络的所有参数，速度更快。
```

```python
def restore_net():
    net2 = torch.load('net.pkl')
    prediction = net2(x)
    # plot result
    plt.subplot(132)
    plt.title('Net2')
    plt.scatter(x.data.numpy(), y.data.numpy())
    plt.plot(x.data.numpy(), prediction.data.numpy(), 'r-', lw=5)
    pass

def restore_params():
    # 提取参数模式下，首先要建立一个与原网络一样结构的网络，然后再把保存的参数复制到新的网络中。
    net3 = torch.nn.Sequential(
        torch.nn.Linear(1, 10),
        torch.nn.ReLU(),
        torch.nn.Linear(10, 1)
    )
    net3.load_state_dict(torch.load('net_params.pkl'))
    prediction = net3(x)
    
    # plot result
    plt.subplot(133)
    plt.title('Net3')
    plt.scatter(x.data.numpy(), y.data.numpy())
    plt.plot(x.data.numpy(), prediction.data.numpy(), 'r-', lw=5)
    plt.show()
    pass

# save net1
save()
# restore 整个网络
restore_net()
# restore网络的所有参数
restore_params()
```

```python
# demo完整代码
import torch
from torch.autograd import Variable
import matplotlib.pyplot as plt
import matplotlib.pyplot as plt
import os
os.environ['KMP_DUPLICATE_LIB_OK'] = 'TRUE'

x = torch.unsqueeze(torch.linspace(-1, 1, 100), dim=1)
y = x.pow(2) + 0.2*torch.rand(x.size())

x, y = Variable(x), Variable(y)

# save net1
def save():
    # 使用顺序容器Sequential产生网络；
    net1 = torch.nn.Sequential(
        torch.nn.Linear(1, 10),
        torch.nn.ReLU(),
        torch.nn.Linear(10, 1)
    )
    # 构建优化器
    optimizer = torch.optim.SGD(net1.parameters(), lr=0.2)  # 训练优化所有参数
    loss_func = torch.nn.MSELoss()

    for t in range(100):  # 训练100次
        prediction = net1(x)
        loss = loss_func(prediction, y)
        optimizer.zero_grad()    # 梯度清零
        loss.backward()    # 反向传播求梯度
        optimizer.step()    # 更新权重参数

    plt.subplot(131)
    plt.title('Net1')
    plt.scatter(x.data.numpy(), y.data.numpy())
    plt.plot(x.data.numpy(), prediction.data.numpy(), 'r-', lw=5)

    torch.save(net1, 'net.pkl')  # 保存整个网络
    torch.save(net1.state_dict(), 'net_params.pkl')  # 保存网络的所有参数，速度更快。


def restore_net():
    net2 = torch.load('net.pkl')
    prediction = net2(x)
    # plot result
    plt.subplot(132)
    plt.title('Net2')
    plt.scatter(x.data.numpy(), y.data.numpy())
    plt.plot(x.data.numpy(), prediction.data.numpy(), 'r-', lw=5)
    pass


def restore_params():
    # 提取参数模式下，首先要建立一个与原网络一样结构的网络，然后再把保存的参数复制到新的网络中。
    net3 = torch.nn.Sequential(
        torch.nn.Linear(1, 10),
        torch.nn.ReLU(),
        torch.nn.Linear(10, 1)
    )
    net3.load_state_dict(torch.load('net_params.pkl'))
    prediction = net3(x)

    # plot result
    plt.subplot(133)
    plt.title('Net3')
    plt.scatter(x.data.numpy(), y.data.numpy())
    plt.plot(x.data.numpy(), prediction.data.numpy(), 'r-', lw=5)
    plt.show()
    pass


# save net1
save()
# restore 整个网络
restore_net()
# restore网络的所有参数
restore_params()

```



## torch.optim.SGD

神经网络**优化器**，优化神经网络。`torch.optim`是实现各种优化算法的**包**；

- 如何使用optimizer?

  要使用torch.optim,必须构造一个optimizer对象，该对象能保存当前的参数状态并且基于计算梯度更新参数。

- 构建一个优化器

  要构造一个optimizer，必须给它一个包含参数(必须是Variable对象)进行优化。然后，可以指定optimizer的参数选项，比如学习率、权重衰减等。

  ```python
  optimizer = optim.SGD(model.parameters(), lr=0.01, momentum=0.9)
  optimizer = optim.Adam([var1, var2], lr=0.0001)
  ```

- 优化器的差别与选择

  - **Stochastic Gradient Descent (SGD）**  随机梯度下降

    SGD是最基础的优化方法，普通的训练方法, 需要重复不断的把整套数据放入神经网络NN中训练, 这样消耗的计算资源会很大.当我们使用SGD会把数据拆分后再分批不断放入 NN 中计算. 每次使用批数据, 虽然不能反映整体数据的情况, 不过却很大程度上加速了 NN 的训练过程, 而且也不会丢失太多准确率.

  - **Momentum**  动量

    Momentum 传统的参数 W 的更新是把原始的 W 累加上一个负的学习率(learning rate) 乘以校正值 (dx). 此方法比较曲折。我们把这个人从平地上放到了一个斜坡上, 只要他往下坡的方向走一点点, 由于向下的惯性, 他不自觉地就一直往下走, 走的弯路也变少了. 这就是 Momentum 参数更新。

  - **AdaGrad**

    AdaGrad 优化学习率，使得每一个参数更新都会有自己与众不同的学习率。与momentum类似，不过不是给喝醉酒的人安排另一个下坡, 而是给他一双不好走路的鞋子, 使得他一摇晃着走路就脚疼, 鞋子成为了走弯路的阻力, 逼着他往前直着走.

  - **RMSProp**

    RMSProp 有了 momentum 的惯性原则 , 加上 adagrad 的对错误方向的阻力, 我们就能合并成这样. 让 RMSProp同时具备他们两种方法的优势. 不过细心的同学们肯定看出来了, 似乎在 RMSProp 中少了些什么. 原来是我们还没把 Momentum合并完全, RMSProp 还缺少了 momentum 中的 这一部分. 所以, 我们在 Adam 方法中补上了这种想法.

  - **Adam**

    Adam 计算m 时有 momentum 下坡的属性, 计算 v 时有 adagrad 阻力的属性, 然后再更新参数时 把 m 和 V 都考虑进去. 实验证明, 大多数时候, 使用 adam 都能又快又好的达到目标, 迅速收敛. 所以说, 在加速神经网络训练的时候, 一个下坡, 一双破鞋子, 功不可没.

  ```python
  # SGD 就是随机梯度下降
  opt_SGD = torch.optim.SGD(net_SGD.parameters(), lr=LR)
  # momentum 动量加速,在SGD函数里指定momentum的值即可
  opt_Momentum = torch.optim.SGD(net_Momentum.parameters(), lr=LR, momentum=0.8)
  # RMSprop 指定参数alpha
  opt_RMSprop = torch.optim.RMSprop(net_RMSprop.parameters(), lr=LR, alpha=0.9)
  # Adam 参数betas=(0.9, 0.99)
  opt_Adam = torch.optim.Adam(net_Adam.parameters(), lr=LR, betas=(0.9, 0.99))
  ```

- 如何优化一个神经网络？

  SGD 是最普通的优化器, 也可以说没有加速效果, 而 Momentum 是 SGD 的改良版, 它加入了动量原则. 后面的 RMSprop 又是 Momentum 的升级版. 而 Adam 又是 RMSprop 的升级版. 不过从这个结果中我们看到, Adam 的效果似乎比 RMSprop 要差一点. 所以说并不是越先进的优化器, 结果越佳. 我们在自己的试验中可以尝试不同的优化器, 找到那个最适合你数据/网络的优化器。

  [参数解释]https://blog.csdn.net/qq_34690929/article/details/79932416

## torch常用损失函数

深度神经网络输出的结果与标注结果进行对比，计算出损失，根据损失进行优化。那么输出结果、损失函数、优化方法就需要进行正确的选择。

- pytorch损失函数的基本用法

  ```python
  criterion = LossCriterion(参数)
  loss = criterion(x, y)
  ```

- 【1】L1范数损失**L1Loss**

  `torch.nn.L1loss( )`    计算input，target两者之差的绝对值，两者要具有相同的形状。**很少使用**

- 【2】Mean Square Error Loss 均方误差损失**MSELoss**

  `torch.nn.MSELoss( )`    计算的是input，target两者的均方差，两者要具有相同的形状。**针对数值不大的回归问题**

- 【3】**Cross Entropy Loss**交叉熵损失

  `torch.nn.CrossEntropyLoss( )`    input的形状是(N, C),target的形状是(N, )

  ```python
  loss = torch.nn.CrossEntropyLoss()
  input = torch.randn(3, 5, requires_grad=True)
  target = torch.empty(3, dtype=torch.long).random_(5)
  output = loss(input, target)
  output.backward()
  ```

  当训练有 C 个类别的分类问题时很有效. 可选参数 weight必须是一个1维 Tensor, 权重将被分配给各个类别. 对于不平衡的训练集非常有效。在多分类任务中，经常采用 softmax激活函数+交叉熵损失函数，因为交叉熵描述了两个概率分布的差异，然而神经网络输出的是向量，并不是概率分布的形式。所以需要 softmax激活函数将一个向量进行“归一化”成概率分布的形式，再采用交叉熵损失函数计算 loss。
  

## PyTorch使用GPU加速

- 检测是否可以使用GPU？

  ```python
  use_gpu = torch.cuda.is_available( )
  # 当返回值是True，表示可以使用GPU；返回False表示不可用；
  ```

- 【1】构建网络时，把**网络、损失函数**转换到GPU上

  ```python
  model = get_model( )
  loss_f = torch.nn.CrossEntropyLoss( )
  if (use_gpu):
      model = model.cuda()
      loss_f = loss_f.cuda()
  ```

- 【2】训练网络时，把**数据**转换到GPU上

  ```python
  if (use_gpu):
      x, y = x.cuda(), y.cuda()
  ```

- 【3】取出数据时，需要从GPU转换到CPU上进行操作

  ```python
  if (use_gpu):
      loss = loss.cpu()
      acc = acc.cpu()
  ```

  

## GPU显存爆炸？

```python
RuntimeError: CUDA out of memory. Tried to allocate 15.48 GiB (GPU 0; 11.00 GiB total capacity; 1.39 GiB already allocated; 7.99 GiB free; 1.42 GiB reserved in total by PyTorch)
```

**`out of memory`**

【1】显存装不下众多的模型**权重**、**中间变量**，导致程序崩溃；——及时清空中间变量，优化代码，减少batch等，均能减少显存溢出的风险。

- 如何计算所使用的显存？(计算设计的模型以及中间变量所占显存的大小)

  在平常的训练中，经常使用的数据类型一般为:(1)float32 单精度浮点型；(2)int32 整型；

  一个8bit的整型变量占空间1B，float32—4B, int32—4B；

  如:一幅RGB图片，长宽为500×500，数据类型为单精度浮点型，那该图片所占的显存大小=500×500×3×4B=3Mb

  **占用显存较多空间的并不是输入图像，而是神经网络中的中间变量以及使用optimizer算法时产生的巨量的中间参数**

  一个模型占用的显存分为：**模型自身的参数(params)**、**模型计算产生的中间变量(memory)**。

  模型中哪些层会占用显存？有参数的层即会占用显存，一般的卷积层都会占用显存，而激励层ReLU没有参数就不会占用显存。

  占用显存的层：

  - 卷积层，通常的conv2d
  - 全连接层，Linear层
  - BatchNorm层
  - Embedding层

  不占用显存的层：

  - 激活层ReLU等
  - 池化层
  - Dropout层

  计算方式：

  - Conv2d(Cin, Cout, K):参数数目：Cin×Cout×K×K
  - Linear(M, N):参数数目：M×N
  - BatcgNorm(N):参数数目：2N
  - Embedding(N, W):参数数目：N×W

- **如何优化**？

  除了算法层的优化，最基本的优化有：

  - 减少输入图像的尺寸
  - 减少batch，减少每次的输入图像数量
  - 多使用下采样，池化层
  - 一些神经网络可以进行小优化，利用relu层中设置inplace
  - 选择显存更大的GPU
  - 从深度学习框架上面进行优化

  ```python
  # 传统训练
  for i,(feature,target) in enumerate(train_loader):
      outputs = model(feature)  # 前向传播
      loss = criterion(outputs,target)  # 计算损失
      optimizer.zero_grad()   # 清空梯度
      loss.backward()  # 计算梯度
      optimizer.step()  # 反向传播， 更新网络参数
  ```

  ```python
  # 加入梯度累计之后
  for i,(features,target) in enumerate(train_loader):
      outputs = model(images)  # 前向传播
      loss = criterion(outputs,target)  # 计算损失
      loss = loss/accumulation_steps   # 可选，如果损失要在训练样本上取平均
  
      loss.backward()  # 计算梯度
      if((i+1)%accumulation_steps)==0:
          optimizer.step()        # 反向传播，更新网络参数
          optimizer.zero_grad()   # 清空梯度
  ```

  比较来看， 我们发现，梯度累加本质上就是累加 `accumulation_steps` 个 `batchsize/accumulationsteps` 的梯度， 再根据累加的梯度来更新网络参数，以达到真实梯度类似`batch_size` 的效果。在使用时，需要注意适当的扩大学习率。

  更详细来说， 我们假设 `batch size = 4 `， `accumulation steps = 8` ， 梯度积累首先在前向传播的时候以 `batch_size=4` 来计算梯度，但是不更新参数，将梯度积累下来，直到我们计算了 `accumulation steps` 个 batch， 我们再更新参数。其实本质上就等价于：真正的 batch_size = batch_size * accumulation_steps梯度积累能很大程度上缓解GPU显存不足的问题，推荐使用。

## 批数据训练

```python
import torch
import torch.utils.data as Data    # 此处Data就是进行小批训练的途径（一个模块）


BATCH_SIZE = 5    # 1小批数据设置成5个

x = torch.linspace(1, 10, 10)
y = torch.linspace(10, 1, 10)
# z = torch.linspace(11, 20, 10)
print('x=', x, '\n','y=', y)
print("="*80)
torch_dataset = Data.TensorDataset(x, y)
print(torch_dataset[:])
print("=" * 80)
loader =Data.DataLoader(
    dataset = torch_dataset,
    batch_size = BATCH_SIZE,
    shuffle = True,    # shuffle是否打乱顺序，True打乱
	)

for epoch in range(3):
    for step, (batch_x, batch_y) in enumerate(loader):
        # training...
        print('Epoch:', epoch, '| Step:', step, '| batch x:',
              batch_x.numpy(), '| batch y:', batch_y.numpy())
```

Dataset是一个包装类，用来将数据包装为Dataset类，然后传入DataLoader中，再使用DataLoader这个类来更加快捷的对数据进行操作。

【1】先用TensorDataset来将数据包装成Dataset类；

````python
torch_dataset = Data.TensorDataset(x, y)
````

【2】继承Dataset类，写一个将数据处理成DataLoader类；

```python
loader = Data.DataLoader(dataset = torch_dataset, 
                        batch_size = BATCH_SIZE,
                        shuffle = True,
                        )
```





## **Batchnorm**

[参考博文](https://blog.csdn.net/qq_25737169/article/details/79048516)

- 深度网络中经常用到的**加速神经网络训练**，**加速收敛速度及稳定性**的算法；



## Dropout

[参考博文](https://www.jianshu.com/p/ef2a7a78aa83)

- 解决神经网络训练过程中的过拟合问题，因为当训练集的数量不多而网络参数相对多时，训练样本误差带来的影响很大，拟合过程中强行逼近到了误差值。因此在每一层神经网络中引入dropout，实现过程：在每次更新参数的过程中，随机的剔除掉部分神经元，不对其参数进行更新。



# Torch.nn

## `nn.Linear()`

- `nn.Linear()`:用于设置网络中的全连接层，需要注意的是全连接层的输入与输出都是二维张量;形状通常为`[batch_size, size]`,不同于卷积层要求输入输出是思维张量。

  其用法与形参说明如下：

  `CLASS torch.nn.Linear(in_features, out_features,bias=True)`

  - `in_features` ：输入的二维张量的大小，即输入的`[batch_size, size]`中的size。
  - `out_features` ：输出的二维张量的大小，即输出的二维张量的形状为`[batch_size，output_size]`，当然，它也代表了该全连接层的神经元个数。
  - 从输入输出的张量的shape角度来理解，相当于一个输入为`[batch_size,in_features]`的张量变换成了`[batch_size, out_features]`的输出张量。

  【用法示例】

  ```python
  import torch
  from torch import nn
  
  
  # in_features由输入张量的形状决定，out_features则决定了输出张量的形状
  connected_layer = nn.Linear(in_features = 64*64*3, out_features = 1)
  
  # 假定输入的图像形状为[64,64,3]----1张3个channel尺寸为64×64的图片
  input = torch.randn(1,3,64,64)
  
  # 将四维张量转换为二维张量之后，才能作为全连接层的输入
  input = input.view(1,64*64*3)  # 还有种写法“input = input.view(1, -1)”
  print(input.shape)    # [1, 12288]
  
  output = connected_layer(input) # 调用全连接层
  print(output.shape)    # [1, 1]
  ```

## `nn.Conv3d()`

- `nn.Conv3d()`:

  其用法与形参说明如下：

  `CLASS torch.nn.Conv3d(in_channels, out_channels, kernel_size, stride=1, padding=0, dilation=1, groups=1, bias=True, padding_mode='zeros', device=None, dtype=None)`

  简化理解为：输入size为`(N, Cin,D,H,W)`;  输出size为`(N,Cout,Dout,Hout,Wout)`;

  ![关系式](E:/software/Typora/pictures/image-20220302102457453.png)

  - `N`:即`batch_size`,以此训练的样本数；
  - `Cin`:输入图像的通道数；如RGB图像为3；
  - `D`:深度；这个参数是在二维卷积中没有的，也是能提取到时序信息的关键，但是也很好理解，就是用于提取时序特征的帧数
  - `H,W`:输入图像的高和宽；

  输出参数：

  - 输出的参数中需要值得提的就是Dout, 这个参数就是提取的时序信息的维度，具体的大小是由卷积核的大小确定的；

  【用法示例】

  ```python
  import torch
  import torch.nn as nn
  from torch import autograd
  
  # kernel_size的第一个维度的值是每次处理的图像帧数，后面是卷积核的大小
  m = nn.Conv3d(3, 3, (3, 7, 7), stride=1, padding=0)
  input = autograd.Variable(torch.randn(1, 3, 7, 60, 40))
  output = m(input)
  print(output.size())    # 输出是 torch.Size([1, 3, 5, 54, 34])
  ```



# 构建CNN网络

## LetNet-5

- 最经典的CNN网络之一，由卷积层、激活层、池化层、全连接层组成。

  ![img](E:/software/Typora/pictures/webp.webp)

- 【定义CNN网络】

  ```python
  import torch
  import torch.nn as nn
  import torch.nn.functional as F
  '''
  CNN计算
  卷积层输入/输出关系：
  H = (H - F +2 * P) / S + 1
  W = (W - F +2 * P) / S + 1
  池化层输入/输出关系：
  H = (H-F)/S+1
  W = (W-F)/S+1
  
  LetNet-5
  input:32*32*3
  
  out_conv1 = (32-5+2*0)/1+1 = 28
  max_pool1 = (28-2)/2+1 = 14
  out_conv2 = (14-5+2*0)/1+1 = 10
  max_pool2 = (10-2)/2+1 = 5
  '''
  
  class Net(nn.Module):
      def __init__(self):
          super(Net, self).__init__()
          # conv1层，输入的灰度图，即in_channels=1；out_channels=6说明使用了6个卷积核
          # kernel_size=5卷积核大小为5*5
          self.conv1 = nn.Conv2d(in_channels=1,out_channels=6,kernel_size=5)
          # conv2层：此层的in_channels等于上一层的out_channels
          # 此层的out_channels=16说明使用了16个卷积核
          self.conv2 = nn.Conv2d(in_channels=6,out_channels=16, kernel_size=5)
          # 全连接层fc1，因为32x32图像输入到fc1层时候，feature map为： 5x5x16
          # 因此，fc1层的输入特征维度为：16*5*5，因为上一层conv2的out_channels=16
          # out_features=84,输出维度为84，代表该层为84个神经元
          self.fc1 = nn.Linear(16*5*5,120)
          self.fc2 = nn.Linear(in_features=120,out_features=84)
          self.fc3 = nn.Linear(in_features=84,out_features=10)
          pass
      
      def forward(self, x):
          # Max pooling over a (2, 2) window
          # intput-->[CONV-->ReLU]×N---->[POOL]×M---->[FC-->ReLU]×K---->FC
          x = F.max_pool2d(F.relu(self.conv1(x)), (2,2))
          x = F.max_pool2d(F.relu(self.conv2(x)), 2)
          # 特征图转换为一个１维的向量
          x = x.view(-1, self.num_flat_features(x))
          x = F.relu(self.fc1(x))
          x = F.relu(self.fc2(x))
          x = self.fc3(x)
          return x
      
      def num_flat_features(self, x):
          # x--[16,5,5]，则此处size=25
          size = x.size()[1:]    # all dimensions except the batch dimension
          num_features = 1
          for s in size:
              num_features *= s
          return num_features
      
      
  net = Net()
  print(net)
  ```

- 【网络的参数】

  网络可学习参数：`w`:权重、`b`：偏置；

  ```python
  # The learnable parameters of a model are returned by net.parameters()
  params = list(net.parameters())
  ```

- 【测试一个样例】

  `LetNet-5` 输入样本为：`32x32x1`的图像

  `torch.randn(1, 1, 32, 32)` 输出一个1(`nSample`)x1(`channels`)x32(`width`)x32(`width`)的Tensor

  ```python
  # Try a random 32x32 input
  input = torch.randn(1, 1, 32, 32)
  out = net(input)
  print(out)
  ```

# pytorch网络结构可视化

使用pytorch定义网络结构之后，为了直观起见，需要可视化网络结构，以图的形式显示出来。可采用`netron`

- 【1】安装`netron`包：

  打开Anaconda Prompt进入自己的pytorch环境，运行代码安装netron依赖包；

  ```python
  conda activate mynetron
  # 在新的虚拟环境中安装pytorch
  conda install pytorch torchvision torchaudio cudatoolkit=10.2 
  # 在虚拟环境中安装netron
  pip install netron
  ```

- 【2】查看网络结构

  ```python
  # 针对有网络模型，但还没有训练保存 .pth 文件的情况
  import netron
  import torch.onnx
  from torch.autograd import Variable
  from torchvision.models import resnet18  # 以 resnet18 为例
  
  myNet = resnet18()  # 实例化 resnet18
  x = torch.randn(16, 3, 40, 40)  # 随机生成一个输入
  modelData = "./demo.pth"  # 定义模型数据保存的路径
  # modelData = "./demo.onnx"  # 有人说应该是 onnx 文件，但我尝试 pth 是可以的 
  torch.onnx.export(myNet, x, modelData)  # 将 pytorch 模型以 onnx 格式导出并保存
  netron.start(modelData)  # 输出网络结构
  
  #  针对已经存在网络模型 .pth 文件的情况
  import netron
  
  modelData = "./demo.pth"  # 定义模型数据保存的路径
  netron.start(modelData)  # 输出网络结构
  ```

  

# torch.nn.Module类

`torch.nn.Module`类是所有神经网络的基类。

- 【神经网络构架的基本格式】

  ```python
  import torch.nn as nn
  import torch.nn.functional as F
  
  class Model(nn.Module):
      def __init__(self):
          super(Model, self).__init__()
          self.conv1 = nn.Conv2d(1, 20, 5)
          self.conv2 = nn.Conv2d(20, 20, 5)
          pass
      
      def forward(self, x):
          x = F.relu(self.conv1(x))
          return F.relu(self.conv2(x))
  ```

- 【`torch.nn.Module`类的主要方法】

  - 【1】`add_module(name, module)`

    功能：给当前的module添加一个子module；

    ——name:子module名；

    ——module：子module；

  - 【2】`applay(fn)`

    功能：对所有子module使用fn;

    ——fn(sub_module)

  - 【3】`forward(*input)`

    功能：神经网络的前向计算。所有子类必须实现该方法。

  - 【4】`parameters()`

    功能：返回moudle parameters的迭代器；

  # torch.nn.Sequential类

  `torch.nn.Sequential`是一个顺序容器container.**根据传入的构造方法依次添加module**。也可以直接传入一个有序字典OrderedDict。

  ```python
  # Example of using Sequential
  model = nn.Sequential(
            nn.Conv2d(1,20,5),
            nn.ReLU(),
            nn.Conv2d(20,64,5),
            nn.ReLU()
          )
  ```

  ```python
  # Example of using Sequential with OrderedDict
  model = nn.Sequential(OrderedDict([
            ('conv1', nn.Conv2d(1,20,5)),
            ('relu1', nn.ReLU()),
            ('conv2', nn.Conv2d(20,64,5)),
            ('relu2', nn.ReLU())
          ]))
  ```

# 实现/构建神经网络的不同方式

【方法一】

- 简单方式

  ```python
  class Net1(nn.Module):
  
      def __init__(self):
          super(Net1, self).__init__()
  
          self.conv1 = nn.Conv2d(in_channels=3,
                                 out_channels=32,
                                 kernel_size=3,
                                 stride=1,
                                 padding=1)
          # why 32*32*3
          self.fc1 = nn.Linear(in_features=32 * 3 * 3,
                               out_features=128)
          self.fc2 = nn.Linear(in_features=128,
                               out_features=10)
  
      def forward(self, x):
          x = F.max_pool2d(F.relu(self.conv1(x)), 2)
          x = x.view(x.size(0), -1)
          x = F.relu(self.fc1(x))
          x = self.fc2(x)
          return x
  
  
  print("CNN model_1:")
  model_1 = Net1()
  print(model_1)
  ```

​	【方法二】

- 使用`torch.nn.Sequential`

  ```python
  class Net2(nn.Module):
      def __init__(self):
          super(Net2, self).__init__()
          self.conv = nn.Sequential(
              nn.Conv2d(in_channels=3,
                        out_channels=32,
                        kernel_size=3,
                        stride=1,
                        padding=1),
              nn.ReLU(),
              nn.MaxPool2d(kernel_size=2)
          )
  
          self.fc = nn.Sequential(
              nn.Linear(in_features=32 * 3 * 3,
                        out_features=128),
              nn.ReLU(),
              nn.Linear(in_features=128,
                        out_features=10)
          )
  
      def forward(self, x):
          conv_out = self.conv(x)
          res = conv_out.view(conv_out.size(0), -1)
          out = self.fc(res)
          return out
  
  
  print('CNN model_2:')
  print(Net2())
  ```

【方法三】

- 使用`torch.nn.Sequential`，传入有序字典；

  特点：可以指定每一层的名称。

  ```python
  '''
  使用字典OrderedDict形式
  '''
  class Net4(nn.Module):
  
      def __init__(self):
          super(Net4, self).__init__()
          self.conv = nn.Sequential(
              OrderedDict(
                  [
                      ('conv1', nn.Conv2d(in_channels=3,
                                          out_channels=32,
                                          kernel_size=3,
                                          stride=1,
                                          padding=1)),
                      ('relu1', nn.ReLU()),
                      ('pool1', nn.MaxPool2d(kernel_size=2))
  
                  ]
              )
          )
  
          self.fc = nn.Sequential(
              OrderedDict(
                  [
                      ('fc1', nn.Linear(in_features=32 * 3 * 3,
                                        out_features=128)),
  
                      ('relu2', nn.ReLU()),
  
                      ('fc2', nn.Linear(in_features=128,
                                        out_features=10))
                  ]
              )
          )
  
      def forward(self, x):
          conv_out = self.conv(x)
          res = conv_out.view(conv_out.size(0), -1)
          out = self.fc(res)
          return out
  
  
  print('CNN model_4:')
  print(Net4())
  
  ```

【方法四】

- 使用`add_module`方法，添加一个子module.采用此方式也可以指定每一层的名称。

- ```python
  '''
  通过 add_module()添加
  '''
  class Net3(nn.Module):
      def __init__(self):
          super(Net3, self).__init__()
          self.conv = nn.Sequential()
          self.conv.add_module(name='conv1',
                               module=nn.Conv2d(in_channels=3,
                                                out_channels=32,
                                                kernel_size=1,
                                                stride=1))
          self.conv.add_module(name='relu1', module=nn.ReLU())
          self.conv.add_module(name='pool1', module=nn.MaxPool2d(kernel_size=2))
  
          self.fc = nn.Sequential()
          self.fc.add_module('fc1', module=nn.Linear(in_features=32 * 3 * 3,
                                                     out_features=128))
          self.fc.add_module('relu2', module=nn.ReLU())
          self.fc.add_module('fc2', module=nn.Linear(in_features=128,
                                                     out_features=10))
  
      def forward(self, x):
          conv_out = self.conv(x)
          res = conv_out.view(conv_out.size(0), -1)
          out = self.fc(x)
          return out
  
  
  print('CNN model_3:')
  print(Net3())
  ```

# 测试网络

- 传入一个`Tensor`;(大小：1×3×6×6)

  ```python
  x = torch.randn(1, 3, 6, 6)
  model = Net4()
  out = model(x)
  print(out)
  ```

  







# HybridSN模型

![HybridSN](E:/software/Typora/pictures/image-20220308172558816.png)

- 【`3D-CNN`部分】

  - Conv1: `(1,30,25,25)`, 8个`7×3×3`的卷积核 ==> `(8,24,23,23)`
  
    - 输入1个`30×25×25`的3D数据块，经过8个`7×3×3`的卷积核 ,输出8个`24×23×23`的特征；（stride=1，padding=0）
  
      ```python
      # CNN计算
      # 卷积层输入/输出关系：
      # H = (H - F +2 * P) / S + 1   输入25--> 输出23
      # W = (W - F +2 * P) / S + 1
      # 同理，B = (B - F +2 * P) / S + 1    输入30 --> 输出24
      ```
      
    
  - Conv2: `(8,24,23,23)`, 16个`5×3×3`的卷积核 ==> `(16,20,21,21)`
  
    - 输入8个`24×23×23`的3D数据块，经过16个`5×3×3`的卷积核 ,输出16个`20×21×21`的特征；（stride=1，padding=0）
  
      ```python
      # 输入23 --> 输出21
      # 输入24 --> 输出20
      ```
  
  - Conv3:  `(16,20,21,21)`, 32个`3×3×3`的卷积核 ==> `(32,18,19,19)`
  
    - 输入16个`20×21×21`的3D数据块，经过32个`3×3×3`的卷积核 ,输出32个`18×19×19`的特征；（stride=1，padding=0）
  
      ```python
      # 输入21 --> 输出19
      # 输入20 --> 输出18
      ```
  
- 【`2D-CNN`部分】

  - 需要将前面`3D-CNN`部分的输出结果作为此层的输入，所以需要将`3D-CNN`的输出结果`reshape`，得到`(32*18,19,19)`即`(576,19,19)`。
  
  - Conv4: `(576,19,19)`, 64个`3×3`的卷积核 ==> `(64,17,17)`
  
    - 输入576个`19×19`的2D数据，经过64个`3×3`的卷积核，输出64个`17×17`的特征；（stride=1，padding=0）
  
      ```python
      # 输入19 --> 输出17
      ```
  
- 【将2D-CNN输出结果进行`flatten`操作】64×17×17=18496维向量；

- 【全连接层】都使用比例为0.4的`Dropout`。

  - FC1: 输入`18496`,输出`256`;	(节点数256)
  - FC2: 输入`256`,输出`128`;    (节点数128)
  - FC3: 输入`128`,输出`n_classes`;    (节点数n_classes)
  
- 【网络模型】

  ```python
  # 自定义：数据集类别数
  class_num = 16
  class HybridSN(nn.Module):  
    def __init__(self, in_channels=1, out_channels=class_num):
      super(HybridSN, self).__init__()
      self.conv3d_features = nn.Sequential(
          nn.Conv3d(in_channels,out_channels=8,kernel_size=(7,3,3)),
          nn.ReLU(),
          nn.Conv3d(in_channels=8,out_channels=16,kernel_size=(5,3,3)),
          nn.ReLU(),
          nn.Conv3d(in_channels=16,out_channels=32,kernel_size=(3,3,3)),
          nn.ReLU()
      )
  
      self.conv2d_features = nn.Sequential(
          nn.Conv2d(in_channels=32 * 18, out_channels=64, kernel_size=(3,3)),
          nn.ReLU()
      )
  
      self.classifier = nn.Sequential(
          nn.Linear(64 * 17 * 17, 256),
          nn.ReLU(),
          nn.Dropout(p=0.4),
          nn.Linear(256, 128),
          nn.ReLU(),
          nn.Dropout(p=0.4),
          nn.Linear(128, 16)
      )
   
    def forward(self, x):
      x = self.conv3d_features(x)
      x = x.view(x.size()[0],x.size()[1]*x.size()[2],x.size()[3],x.size()[4])
      x = self.conv2d_features(x)
      x = x.view(x.size()[0],-1)
      x = self.classifier(x)
      return x
  
  ```

  【带有`Batch Normalization`的HybridSN模型】

  ```python
  class HybridSN_BN(nn.Module):  
    def __init__(self, in_channels=1, out_channels=class_num):
      super(HybridSN_BN, self).__init__()
      self.conv3d_features = nn.Sequential(
          nn.Conv3d(in_channels,out_channels=8,kernel_size=(7,3,3)),
          nn.BatchNorm3d(8),
          nn.ReLU(),
          nn.Conv3d(in_channels=8,out_channels=16,kernel_size=(5,3,3)),
          nn.BatchNorm3d(16),
          nn.ReLU(),
          nn.Conv3d(in_channels=16,out_channels=32,kernel_size=(3,3,3)),
          nn.BatchNorm3d(32),
          nn.ReLU()
      )
  
      self.conv2d_features = nn.Sequential(
          nn.Conv2d(in_channels=32 * 18, out_channels=64, kernel_size=(3,3)),
          nn.BatchNorm2d(64),
          nn.ReLU()
      )
  
      self.classifier = nn.Sequential(
          nn.Linear(64 * 17 * 17, 256),
          nn.ReLU(),
          nn.Dropout(p=0.4),
          nn.Linear(256, 128),
          nn.ReLU(),
          nn.Dropout(p=0.4),
          nn.Linear(128, 16)
      )
   
    def forward(self, x):
      x = self.conv3d_features(x)
      x = x.view(x.size()[0],x.size()[1]*x.size()[2],x.size()[3],x.size()[4])
      x = self.conv2d_features(x)
      x = x.view(x.size()[0],-1)
      x = self.classifier(x)
      return x
  
  ```

  

  | 网络层      | 尺寸 | 参数 |
  | ----------- | ---- | ---- |
  | `input_1`   | （） | 0    |
  | `Conv3d_1`  |      |      |
  | `Conv3d_2`  |      |      |
  | `Conv3d_3`  |      |      |
  | `reshape_1` |      |      |
  | `Conv2d_1`  |      |      |
  | `flatten_1` |      |      |
  | `dense_1`   |      |      |
  | `dropout_1` |      |      |
  | `dense_2`   |      |      |
  | `dropout_2` |      |      |
  | `dense_3`   |      |      |




# 训练分类器

## 【1】数据

- 将需要处理的数据加载到`Numpy`数组，再将该**数组转换为张量**(torch.*Tensor)
  - 对于图像，`Pillow`,`OpenCV`等包很有用；
- 针对视觉处理，有`torchvision`包，其中包含用于常见数据集（例如 `Imagenet`，`CIFAR10`，`MNIST `等）的数据加载器，以及用于图像（即`torchvision.datasets`和`torch.utils.data.DataLoader`）的数据转换器。
- 【例子】使用`CIFAR10`数据集；含10个类别，图像尺寸:`3×32×32`；

## 【2】训练图像分类器

- 【2.1】使用`torchvision`加载并标准化 `CIFAR10 `训练和测试数据集;
- 【2.2】定义卷积神经网络；
- 【2.3】定义损失函数；
- 【2.4】根据训练数据训练网络；
- 【2.5】在测试数据上测试网络；

```python
import torch
import torchvision
import torchvision.transforms as transforms
# 【2.1】
transform = transforms.Compose(
    [transforms.ToTensor(),
     transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])

trainset = torchvision.datasets.CIFAR10(root='./data', train=True,
                                        download=True, transform=transform)
trainloader = torch.utils.data.DataLoader(trainset, batch_size=4,
                                          shuffle=True, num_workers=2)

testset = torchvision.datasets.CIFAR10(root='./data', train=False,
                                       download=True, transform=transform)
testloader = torch.utils.data.DataLoader(testset, batch_size=4,
                                         shuffle=False, num_workers=2)

classes = ('plane', 'car', 'bird', 'cat',
           'deer', 'dog', 'frog', 'horse', 'ship', 'truck')

```

```python
# 【2.2】
import torch.nn as nn
import torch.nn.functional as F

class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x

net = Net()

```

```python
#【2.3】
import torch.optim as optim

criterion = nn.CrossEntropyLoss()
optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
```

```python
# 【2.4】
for epoch in range(2):  # loop over the dataset multiple times

    running_loss = 0.0
    for i, data in enumerate(trainloader, 0):
        # get the inputs; data is a list of [inputs, labels]
        inputs, labels = data

        # zero the parameter gradients
        optimizer.zero_grad()

        # forward + backward + optimize
        outputs = net(inputs)
        loss = criterion(outputs, labels)
        loss.backward()
        optimizer.step()

        # print statistics
        running_loss += loss.item()
        if i % 2000 == 1999:    # print every 2000 mini-batches
            print('[%d, %5d] loss: %.3f' %
                  (epoch + 1, i + 1, running_loss / 2000))
            running_loss = 0.0

print('Finished Training')
# 保存训练过的模型
PATH = './cifar_net.pth'
torch.save(net.state_dict(), PATH)
```

```python
# 【2.5】
net = Net()
net.load_state_dict(torch.load(PATH))    # 重新加载保存的模型
outputs = net(images)
# 输出是 10 类的能量。 一个类别的能量越高，网络就认为该图像属于特定类别。 因此，让我们获取最高能量的指数：
_, predicted = torch.max(outputs, 1)

print('Predicted: ', ' '.join('%5s' % classes[predicted[j]]
                              for j in range(4)))

```

