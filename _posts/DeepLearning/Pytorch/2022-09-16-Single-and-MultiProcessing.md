---
title : "Pytorch data api : Multiprocessing"



excerpt: "Pytorch multiprocess"

categories:
  - Pytorch
tags:
  - [Machine Learning,Pytorch,deep learning,Pytorch data api ]
# classes : wide
toc: true
toc_sticky: true
---
When DataLoading is done in Pytorch, a class called DataLoader is used. At this time, data can be loaded within a single process, or it can be loaded by parallelizing it.

# Default Option
For DataLoader, Single Process data loading is the default option.

The Python Process prevents the Global Interpretation Lock (GIL) from parallelizing Python code into threads. Therefore, in order to prevent one process from blocking computation in data loading, Pytorch sets the argument 'num_workers' to a positive integer to easily convert it to multi-process data loading.


# Single Processing

Data fetching is done in the same process. That is, one process is blocking computing.
This is the preferred method if resources are limited or if the data is small enough to fit the entire memory.
Another advantage of this method is that error tracing is easy.

# Multi Processing

In Dataloader, you can set an argument called num_workers.

1. 'num_workers' = 0
    + Data loading is done in the main process.
2. 'num_workers' > 0
    + Multi-process data loading is activated, and as many worker processes as specified are created. These subprocesses are used for data loading.


When loading data, call `enumerate(dataloader)` to create an iterator of DataLoader each time. Here, as many worker_processes as specified in the `num_workers` argument are created. Then, `dataset` , `collate_fn`, and `worker_init_fn` are delivered to each worker. After the worker is initialized, data is fetched.

This means that internal I/O and transformation (including `collate_fn`) are executed in worker_process with dataset access.


## Iterating DataLoader

Actually, when fetching data from multiple workers, torch.utils.data.get_worekr_info() is used.
This function returns the following items.
+ worker_id
+ dataset replica
+ initial seed
+ .etc

In main_process it will return None. Pytorch developers use this function in their dataset code or worker_init_fn to determine whether the code is running in a worker_process . Then , developers configures individual dataset_replicas.

In particular, it is said to be helpful for data sharding.

### Map-style

If you create multiple subprocesses on the map-style dataset, you create indicies using samplers and deliver them to each worker. Shuffling in the sampler is performed in the main process.
That is, the main process assigns the indicies shuffled to the worker to load the data in data loading .
Let's take a look at the code example below.
```python 
import torch

class MyMapDataset(torch.utils.data.Dataset) :
    def __init__(self , start , end) :
        super(MyMapDataset).__init__()
        assert end>start , "this example code only works with end> = start"

        self.start = start
        self.end = end 
        self.data = list(range(self.start,self.end))
    def __getitem__(self,idx) :
        
        worker_info  =torch.utils.data.get_worker_info()
        worker_id = worker_info.id
        print(f"worekr_id : {worker_id} data : {self.data[idx]}\n")

        return self.data[idx]
    def __len__(self) :
        return len(self.data)


map_ds = MyMapDataset(3,100)


print(list(torch.utils.data.DataLoader(map_ds,num_workers=2)))

# worekr_id : 0 data : 3

# worekr_id : 1 data : 4
# worekr_id : 0 data : 5


# worekr_id : 1 data : 6
# worekr_id : 0 data : 7


# worekr_id : 1 data : 8
# worekr_id : 0 data : 9


# worekr_id : 1 data : 10
# worekr_id : 0 data : 11


# worekr_id : 1 data : 12
# worekr_id : 0 data : 13


# worekr_id : 1 data : 14
# worekr_id : 0 data : 15

# worekr_id : 1 data : 16


# worekr_id : 1 data : 18


#  ... 
# worekr_id : 0 data : 93
# worekr_id : 1 data : 96


# worekr_id : 0 data : 95
# worekr_id : 1 data : 98

# worekr_id : 0 data : 97
# worekr_id : 0 data : 99

# [tensor([3]), tensor([4]), tensor([5]), tensor([6]), tensor([7]), tensor([8]), tensor([9]), tensor([10]), tensor([11]), tensor([12]), tensor([13]), tensor([14]), tensor([15]), tensor([16]), tensor([17]), tensor([18]), tensor([19]), tensor([20]), tensor([21]), tensor([22]), tensor([23]), tensor([24]), tensor([25]), tensor([26]), tensor([27]), tensor([28]), tensor([29]), tensor([30]), tensor([31]), tensor([32]), tensor([33]), tensor([34]), tensor([35]), tensor([36]), tensor([37]), tensor([38]), tensor([39]), tensor([40]), tensor([41]), tensor([42]), tensor([43]), tensor([44]), tensor([45]), tensor([46]), tensor([47]), tensor([48]), tensor([49]), tensor([50]), tensor([51]), tensor([52]), tensor([53]), tensor([54]), tensor([55]), tensor([56]), tensor([57]), tensor([58]), tensor([59]), tensor([60]), tensor([61]), tensor([62]), tensor([63]), tensor([64]), tensor([65]), tensor([66]), tensor([67]), tensor([68]), tensor([69]), tensor([70]), tensor([71]), tensor([72]), tensor([73]), tensor([74]), tensor([75]), tensor([76]), tensor([77]), tensor([78]), tensor([79]), tensor([80]), tensor([81]), tensor([82]), tensor([83]), tensor([84]), tensor([85]), tensor([86]), tensor([87]), tensor([88]), tensor([89]), tensor([90]), tensor([91]), tensor([92]), tensor([93]), tensor([94]), tensor([95]), tensor([96]), tensor([97]), tensor([98]), tensor([99])]

```

As you can see, each indicies is passed to the worker to load the data.

### Iterative-style
Iterable sytle의 dataset을 worker를 이용해서 loading할 때에는 data 중복을 주의해야 합니다.   
Iterable style의 dataset을 loading할 때 subprocess들을 만드는 경우 , 각 worker들이 dataset object의 replica를 얻게 됩니다. 그 다음에, 각 worker들이 dataset object를 iterating 함으로 써 , data의 중복이 발생하게 됩니다. 아래의 예시코드에서 확인해보도록 하겠습니다. 
```python

import torch

class MyIterableDataset(torch.utils.data.IterableDataset) :
    def __init__(self,start, end) :
        super(MyIterableDataset).__init__()
        assert end>start , "this example code only works with end >=start"

        self.start = start
        self.end = end
    def __iter__(self) :
        worker_info = torch.utils.data.get_worker_info()
        worker_id = worker_info.id
        print(range(self.start,self.end))
        print(worker_info)
        return iter(range(self.start,self.end))

ds = MyIterableDataset(start=3 ,end =100)

print(list(torch.utils.data.DataLoader(ds,num_workers=2)))

# range(3, 100)
# WorkerInfo(id=0, num_workers=2, seed=4911920692807402111, dataset=<__main__.MyIterableDataset object at 0x7f5d2ef69910>)range(3, 100)

# WorkerInfo(id=1, num_workers=2, seed=4911920692807402112, dataset=<__main__.MyIterableDataset object at 0x7f5d2ef69910>)
# [tensor([3]), tensor([3]), tensor([4]), tensor([4]), tensor([5]), tensor([5]), tensor([6]), tensor([6]), tensor([7]), tensor([7]), tensor([8]), tensor([8]), tensor([9]), tensor([9]), tensor([10]), tensor([10]), tensor([11]), tensor([11]), tensor([12]), tensor([12]), tensor([13]), tensor([13]), tensor([14]), tensor([14]), tensor([15]), tensor([15]), tensor([16]), tensor([16]), tensor([17]), tensor([17]), tensor([18]), tensor([18]), tensor([19]), tensor([19]), tensor([20]), tensor([20]), tensor([21]), tensor([21]), tensor([22]), tensor([22]), tensor([23]), tensor([23]), tensor([24]), tensor([24]), tensor([25]), tensor([25]), tensor([26]), tensor([26]), tensor([27]), tensor([27]), tensor([28]), tensor([28]), tensor([29]), tensor([29]), tensor([30]), tensor([30]), tensor([31]), tensor([31]), tensor([32]), tensor([32]), tensor([33]), tensor([33]), tensor([34]), tensor([34]), tensor([35]), tensor([35]), tensor([36]), tensor([36]), tensor([37]), tensor([37]), tensor([38]), tensor([38]), tensor([39]), tensor([39]), tensor([40]), tensor([40]), tensor([41]), tensor([41]), tensor([42]), tensor([42]), tensor([43]), tensor([43]), tensor([44]), tensor([44]), tensor([45]), tensor([45]), tensor([46]), tensor([46]), tensor([47]), tensor([47]), tensor([48]), tensor([48]), tensor([49]), tensor([49]), tensor([50]), tensor([50]), tensor([51]), tensor([51]), tensor([52]), tensor([52]), tensor([53]), tensor([53]), tensor([54]), tensor([54]), tensor([55]), tensor([55]), tensor([56]), tensor([56]), tensor([57]), tensor([57]), tensor([58]), tensor([58]), tensor([59]), tensor([59]), tensor([60]), tensor([60]), tensor([61]), tensor([61]), tensor([62]), tensor([62]), tensor([63]), tensor([63]), tensor([64]), tensor([64]), tensor([65]), tensor([65]), tensor([66]), tensor([66]), tensor([67]), tensor([67]), tensor([68]), tensor([68]), tensor([69]), tensor([69]), tensor([70]), tensor([70]), tensor([71]), tensor([71]), tensor([72]), tensor([72]), tensor([73]), tensor([73]), tensor([74]), tensor([74]), tensor([75]), tensor([75]), tensor([76]), tensor([76]), tensor([77]), tensor([77]), tensor([78]), tensor([78]), tensor([79]), tensor([79]), tensor([80]), tensor([80]), tensor([81]), tensor([81]), tensor([82]), tensor([82]), tensor([83]), tensor([83]), tensor([84]), tensor([84]), tensor([85]), tensor([85]), tensor([86]), tensor([86]), tensor([87]), tensor([87]), tensor([88]), tensor([88]), tensor([89]), tensor([89]), tensor([90]), tensor([90]), tensor([91]), tensor([91]), tensor([92]), tensor([92]), tensor([93]), tensor([93]), tensor([94]), tensor([94]), tensor([95]), tensor([95]), tensor([96]), tensor([96]), tensor([97]), tensor([97]), tensor([98]), tensor([98]), tensor([99]), tensor([99])]

```

You can see that a replica of the dataset is passed to each worker and fetching duplicates.
There are several ways to solve this.

#### Using get_worker_info()

As mentioned above, get_worker_info() returns None for main_process , id,replica ,seed and so on for subprocess. You can configure fetching for each worker using worker_id.
```python
class MyIterableDataset(torch.utils.data.IterableDataset) :
    def __init__(self, start, end) :
        super(MyIterableDataset).__init__()
        assert end > start , "this example code only works with end >= start"

        self.start = start
        self.end = end
    
    def __iter__(self) :
        worker_info= torch.utils.data.get_worker_info()
        if worker_info is None :
            iter_start = self.start
            iter_end = self.end
        else :
            per_worker = int(math.ceil((self.end-self.start) ) /
                             float(worker_info.num_workers))
            worker_id = worker_info.id
            iter_start = self.start + worker_id * per_worker
            iter_end =  min(iter_start + per_worker , self.end)
            print(f'worker_id : {worker_id} \n iter_start : {iter_start}  iter_end : {iter_end}\n')
        return iter(range(iter_start, iter_end))


ds = MyIterableDataset(start = 3 ,end = 100) 


print(list(torch.utils.data.DataLoader(ds,num_workers=3)))


# worker_id : 0 
#  iter_start : 3  iter_end : 35
# worker_id : 1 
#  iter_start : 35  iter_end : 67


# worker_id : 2 
#  iter_start : 67  iter_end : 99

# [tensor([3]), tensor([35]), tensor([67]), tensor([4]), tensor([36]), tensor([68]), tensor([5]), tensor([37]), tensor([69]), tensor([6]), tensor([38]), tensor([70]), tensor([7]), tensor([39]), tensor([71]), tensor([8]), tensor([40]), tensor([72]), tensor([9]), tensor([41]), tensor([73]), tensor([10]), tensor([42]), tensor([74]), tensor([11]), tensor([43]), tensor([75]), tensor([12]), tensor([44]), tensor([76]), tensor([13]), tensor([45]), tensor([77]), tensor([14]), tensor([46]), tensor([78]), tensor([15]), tensor([47]), tensor([79]), tensor([16]), tensor([48]), tensor([80]), tensor([17]), tensor([49]), tensor([81]), tensor([18]), tensor([50]), tensor([82]), tensor([19]), tensor([51]), tensor([83]), tensor([20]), tensor([52]), tensor([84]), tensor([21]), tensor([53]), tensor([85]), tensor([22]), tensor([54]), tensor([86]), tensor([23]), tensor([55]), tensor([87]), tensor([24]), tensor([56]), tensor([88]), tensor([25]), tensor([57]), tensor([89]), tensor([26]), tensor([58]), tensor([90]), tensor([27]), tensor([59]), tensor([91]), tensor([28]), tensor([60]), tensor([92]), tensor([29]), tensor([61]), tensor([93]), tensor([30]), tensor([62]), tensor([94]), tensor([31]), tensor([63]), tensor([95]), tensor([32]), tensor([64]), tensor([96]), tensor([33]), tensor([65]), tensor([97]), tensor([34]), tensor([66]), tensor([98])]


```

In the code above, the iter method configures fetching based on worker_id by dividing data by num_workers when subprocess is set.


#### Using worker_init_fn(worker_id)

You can set worker_init_fn as an argument of pytorch dataloader. worker_init_fn receives worker_id as an argument and sets replicas of each dataset individually.
```python
class MyIterableDataset(torch.utils.data.IterableDataset) :
    def __init__(self,start, end) :
        super(MyIterableDataset).__init__()
        assert end>start , "this example code only works with end >=start"

        self.start = start
        self.end = end
    def __iter__(self) :
        worker_info = torch.utils.data.get_worker_info()
        worker_id = worker_info.id
        print(f'worker_id : {worker_id} \n iter_start : {self.start}  iter_end : {self.end}\n')
        return iter(range(self.start,self.end))


def worker_init_fn(worker_id) :
    worker_info = torch.utils.data.get_worker_info()
    dataset = worker_info.dataset
    overall_start = dataset.start
    overall_end = dataset.end

    per_worker = int(math.ceil((overall_end-overall_start) ) /
                     float(worker_info.num_workers))
    worker_id = worker_info.id
    dataset.start = overall_start + worker_id * per_worker
    dataset.end = min(dataset.start + per_worker , overall_end)
    # print(f'worker_id : {worker_id} , worker_start : {dataset.start}  ,  worekr_end : {dataset.end}')


ds = MyIterableDataset(start = 3 ,end = 100) 


print(list(torch.utils.data.DataLoader(ds,num_workers=10,worker_init_fn=worker_init_fn)))


# /usr/local/lib/python3.7/dist-packages/torch/utils/data/dataloader.py:481: UserWarning: This DataLoader will create 10 worker processes in total. Our suggested max number of worker in current system is 2, which is smaller than what this DataLoader is going to create. Please be aware that excessive worker creation might get DataLoader running slow or even freeze, lower the worker number to avoid potential slowness/freeze if necessary.
#   cpuset_checked))
# worker_id : 0 
#  iter_start : 3  iter_end : 12

# worker_id : 1 
#  iter_start : 12  iter_end : 21
# worker_id : 2 
#  iter_start : 21  iter_end : 30

# worker_id : 3 
#  iter_start : 30  iter_end : 39


# worker_id : 4 
#  iter_start : 39  iter_end : 48

# worker_id : 5 
#  iter_start : 48  iter_end : 57

# worker_id : 6 
#  iter_start : 57  iter_end : 66

# worker_id : 7 
#  iter_start : 66  iter_end : 75

# worker_id : 8 
#  iter_start : 75  iter_end : 84

# worker_id : 9 
#  iter_start : 84  iter_end : 93

# [tensor([3]), tensor([12]), tensor([21]), tensor([30]), tensor([39]), tensor([48]), tensor([57]), tensor([66]), tensor([75]), tensor([84]), tensor([4]), tensor([13]), tensor([22]), tensor([31]), tensor([40]), tensor([49]), tensor([58]), tensor([67]), tensor([76]), tensor([85]), tensor([5]), tensor([14]), tensor([23]), tensor([32]), tensor([41]), tensor([50]), tensor([59]), tensor([68]), tensor([77]), tensor([86]), tensor([6]), tensor([15]), tensor([24]), tensor([33]), tensor([42]), tensor([51]), tensor([60]), tensor([69]), tensor([78]), tensor([87]), tensor([7]), tensor([16]), tensor([25]), tensor([34]), tensor([43]), tensor([52]), tensor([61]), tensor([70]), tensor([79]), tensor([88]), tensor([8]), tensor([17]), tensor([26]), tensor([35]), tensor([44]), tensor([53]), tensor([62]), tensor([71]), tensor([80]), tensor([89]), tensor([9]), tensor([18]), tensor([27]), tensor([36]), tensor([45]), tensor([54]), tensor([63]), tensor([72]), tensor([81]), tensor([90]), tensor([10]), tensor([19]), tensor([28]), tensor([37]), tensor([46]), tensor([55]), tensor([64]), tensor([73]), tensor([82]), tensor([91]), tensor([11]), tensor([20]), tensor([29]), tensor([38]), tensor([47]), tensor([56]), tensor([65]), tensor([74]), tensor([83]), tensor([92])]
```


### Warning

Pytorch Docs says that returning CUDA Tensors from multi-processing loading is not recommended. Instead of sharing CUDA Tensor or using CUDA, it is recommended to use automatic memory pinning (pin_memory = True). This allows CUDA to quickly transfer data to available GPOs.

## Platform Specific

When Python Multiprocessing is used, the worker launch behavior varies depending on the OS.

1. UNIX
    + fork() is the default method to start multiprocessing. Using fork() , child workers have direct access to datasets and Python functions through a replicated address space.
  
2. Windows, MAC
   + spawn() is the default method to start multiprocessing. Using spawn() , executes the main script while other interpereters are executed. Then, serialize the dataset, collate_fn , and other arguments through pickle serialization, and run the internal worker function. Using serialization means that while using multiprocess data loads, you have to perform two steps to ensure compatibility with Windows.
        1. Enclose the main script code in an `if __name__ =='__main__'` block.
           + why? This is to prevent the main script code from being executed again when each worker process is executed. Include the Dataset and Datalodaer instance creation code in the main script code so that it is not executed again in the worker.
        2. Make sure custom collate_fn , worker_init_fn , and dataset code are defined in top level definitions, ie outside the code that checks `__main__`.
            + why? This is because fucntions are pickled by reference rather than bytecode.

## Randomness in multiprocessing data loading

Each worker sets the seed to (base_seed + worker_id). The base_seed is generated by main_process, and at this time, RNG (Random Number Generator) or a designated generator is used. However, seeds from other libraries may be duplicated. So, when workers are initialized, each worker can return the same random number.
You can use torch.utils.data.get_worker_info().seed or torch.initial.seed from worker_init_fn to access a Pytorch seed set for each worker and use it to pass seeds to other libraries before loading data. .

## Issues
+ Problem
  + After several iterations, the loader worker process occupies the same amount of CPU memory for all Python objects in the parent process (accessible to the worker process). If you include a very large filename lsit) or use a lot of workers, you will run into problems.
+ Why?
  + Storing arbitrary Python Objects in a shared memory causes a copy-on-write problem. Each time these objects are read, the reference count is incremented. The change in the reference count causes a copy-on-access problem of the forked python process. (It's not a memory-leak issue.)
+ Sol
  + Use objects like pandas,numpy,pyarrow instead of basic Python Objects(list,dict). They have a reference count of 1.
  + When storing String, it can be saved using numpy array as ASCII code. Alternatively, you can use ByteCode or Custom Datatype.
# References

[Pytorch-data-docs][Pytorch-data-api]

[Pytorch-data-api]: "https://pytorch.org/docs/stable/data.html#single-and-multi-process-data-loading"




