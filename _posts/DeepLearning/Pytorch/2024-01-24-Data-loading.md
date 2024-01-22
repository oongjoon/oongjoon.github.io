---
title : "Pytorch data api : worker, pinned memory, prefetch,  non-blocking?? 이 것들이 다 설정되면 어떻게 작동할까요?"



excerpt: "Data Transfer optimization and overlap"

categories:
  - Pytorch
tags:
  - [Machine Learning,Pytorch,deep learning ,DeepLearning General Method]
# classes : wide
toc: true
toc_sticky: true
---
# Let's think about Pin-memory, worker, pre-fetch, and GPU non-blocking all combined.
Looking at the pytorch docs, I only knew that if you specify a configuration called worker, a worker will be created and data will be loaded. The moment I combined this with pin-memory, pre-fetch, and GPU non-blocking, I found myself completely unable to understand how it worked. Pytorch itself is a framework that provides a high-level API, but I found out that this configuration allows deep learning model training to be set up at a low-level. It is believed that adjusting the training configuration at a low-level will help improve your capabilities not only as a deep learning engineer but also as a software engineer.

!! If you don't know what Asynchronous is, I recommend you look at this [post]().
## worker
If you look at Pytorch's DataLoader class, there is a configuration called num_worker. The description of num_worker is as follows.
```
num_workers (int, optional) how many subprocesses to use for data loading. 0 means that the data will be loaded in the main process. (default: 0)
```

A subprocess is created with fork() (in the case of Unix), and data as much as batch_size * num_worker is loaded. Here, the point to note is that the calling main process is asynchronous to the worker. In other words, when the main process loads data into the GPU and trains it, num workers fetch data in parallel.Since a worker is an I/O operation that reads data, it can be said that the worker is non-blocking the main process.

One thing to keep in mind when using num_worker configuration is that you must consider how much CPU memory it consumes. The worker process occupies the same amount of CPU memory as the parent process. If the Datset Class contains a very large List, it will occupy as much memory as `worker * parent process size`.

## Pin memory

Here is pytorch's explanation of Pin Memory:
>pin_memory (bool, optional) If True, the data loader will copy Tensors into device/CUDA pinned memory before returning them. If your data elements are a custom type, or your collate_fn returns a batch that is a custom type, see the example below.

Let's find out what it means to copy a tensor to pinned memory.

### nvidia

![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217834&authkey=%21ALlP7XyB6BzlyAo&width=717&height=379)

In order to transfer data from the host to the device (GPU) in Cuda, data must be transferred to a temporarily locked page or pinned memory. Next, data is transmitted to the device. It can be seen as a similar concept to Git's staging area. It is a similar concept to moving changes from git to a commit object by going through a staging area.
By fixing pinned memory, the transfer cost between the host's pageable memory and pinned memory can be eliminated.

## prefetch
Here is the definition from the pytorch docs of prefetch:

>prefetch_factor (int, optional, keyword-only arg) Number of batches loaded in advance by each worker. 2 means there will be a total of 2 * num_workers batches prefetched across all workers. (default value depends on the set value for num_workers. If value of num_workers=0 default is None. Otherwise, if value of num_workers > 0 default is 2).

All workers preload data as much as prefetch * batch_size. Therefore, communication between disk and host is reduced, greatly reducing overhead. However, this must also be set carefully considering the specifications of the CPU.  

### In my opinion
I think this is a difficult option to actually use. It may vary depending on how long the GPU will be waiting for data. However, if hardware resources are sufficient, I think it is more economical to lower the cost by optimizing the hardware specifications from the beginning.
## non_blocking
The definition from pytorch docs of non block is as follows:

> Returns a Tensor with the specified device and (optional) dtype. If dtype is None it is inferred to be self. dtype. When non_blocking, tries to convert asynchronously with respect to the host if possible, e.g., converting a CPU Tensor with pinned memory to a CUDA Tensor. When copy is set, a new Tensor is created even when the Tensor already matches the desired conversion.

This option is available when pinned_memory is set to true, that is, when pinned memory is pinned. The host is asynchronous in the transfer of tensors to the GPU. In other words, the host performs the following operation without paying attention to the function that transmits the tensor to the GPU. Let's find out how this works internally through NVIDIA's docs.


### nvidia
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217835&authkey=%21AK_rXcRO4cr-r1s&width=1139&height=727)

In this figure, h2d refers to data transfer from the device to the host, kernel refers to parallel computation, and d2h refers to data transfer from the device to hardware.
NVIDIA's Cuda is said to reduce bottlenecks by splitting data into small chunks and then scheduling data transfer and computation.
Here, in the case of the sequential version, the stream (function) related to h2d is non-blocking to the host, and the host is asynchronous to the h2d stream. Because control returns to the host, computation proceeds in parallel.

### Precautions

  Here's something to keep in mind. Setting a non-blocking configuration itself does not have any negative effects on the program. However, in practice there may be no difference between using non-blocking and not using it. This is because of the sync point, which is the point where computation cannot be done in parallel and you have to wait for all small chunk data. For example, there is no difference in the final result when transforming a tensor is applied to each small chunk. However, when normalizing a specific dimension, for example batch normalization, statistics for the entire batch must be calculated, so hots must be synchronous to the batch norm function.

  In other words, if you do not have a mathematical understanding of the model's computation, you will not be able to profile its performance.

## All together
Let's explain the data fetching process by combining workers, pinned memory, prefetch, and non blocking.
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217836&authkey=%21AO0XjU31chGSrI8&width=730&height=414)
pin memory configuration is set and num_worker is set to 2. Therefore, the main process (host) loads data as much as batch * 2 into pinned memory.
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217837&authkey=%21AKiwiniQwNuDN2o&width=696&height=463)
When data is transmitted to the device (gpu), the dataloader loads the next batch of data into pinned memory because the host is asynchronous for data transmission.
![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217838&authkey=%21AE0hAnAwRJMia-4&width=750&height=451)
If the prefetch factor is set, data as much as prefetch factor*worker*batch will be loaded. The rest of the process will proceed the same as above.

![image](https://onedrive.live.com/embed?resid=7E81BBCD99889380%217839&authkey=%21AJHSTuTRWcQ3g2s&width=1091&height=622)
If non-blocking is set, data transfer and computation will overlap internally like async version 2. When d2h transfer occurs, the output is transferred back to the CPU or the model parameters are updated while the checkpoint is saved to the disk connected to the host.

## Conclusion

Overlapping data transfer with computation can also be used only from an abstracted high-level API perspective. However, I looked at this at a more low-level level and learned how overlap and optimization occur. If you rely only on the high-level API and do not know the internal operation, you will be dependent on a specific library, making customization and debugging difficult. Thinking about how something works at a low-level makes customization and debugging easier, and deepens your understanding of its operation. It will also deepen your understanding of Computer Science, improve your problem-solving skills, and help you learn new skills more easily. I am still a junior, and since the retirement age is increasing, I will continue to practice thinking at a low level.

## References

https://d2.naver.com/helloworld/3773258


https://developer.nvidia.com/blog/how-optimize-data-transfers-cuda-cc/

https://discuss.pytorch.org/t/when-does-a-pytorch-dataset-worker-pull-new-data/153286


https://discuss.pytorch.org/t/how-to-maximize-cpu-gpu-memory-transfer-speeds/173855/4


https://docs.nvidia.com/cuda/cuda-runtime-api/api-sync-behavior.html#api-sync-behavior__memcpy-async


https://discuss.pytorch.org/t/non-blocking-memory-transfer-to-gpu/188941


https://discuss.pytorch.org/t/should-we-set-non-blocking-to-true/38234/24


https://discuss.pytorch.org/t/doing-data-augmnetation-in-parallel/90685/2


https://pytorch.org/tutorials/recipes/recipes/tuning_guide.html