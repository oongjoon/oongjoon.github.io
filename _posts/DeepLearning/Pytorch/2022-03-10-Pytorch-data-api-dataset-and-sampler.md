---
title : "Pytorch data api : sampler and dataset style"



excerpt: "Pytorch에서 dataset and data sampler"

categories:
  - Pytorch
tags:
  - [Machine Learning,Pytorch,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---
23.12.01 modify Customsampler code : sampler  must return indices/key , so modify dic to dic.keys()

Algorithms of artificial intelligence become more and more complex, but considering only deep learning 1.0 level, this means that features become more and more complex. In other words, it means more hand engineering of data. This means that the features become more and more complex, and the hand engineering weight on the data increases.

For example, in the case of object detection, in addition to the label, additional feature engineering is required, such as a number of things (bounding box, object, multi-object label..etc). It costs a bit more than image recognition. In the case of natural language processing, feature engineering is required to tokenize words and create lookup tables. In the case of answering a question, additional feature engineering is required, such as information about the answer span and calculating the offset for when the passage is cut because it is long. Compared to text classification requiring only one label, the cost is relatively high.

Due to the nature of Deep Learning 1.0, it is important how to experiment with features to learn the distribution of a given feature. Therefore, you need to know how to do data feature engineering, and to do this, you need to know how to use the pytorch data api. There will be a trend moving towards Deep Learning 2.0, but since Deep Learning 1.0 is still the mainstream, data feature engineering is an important craft.



# Dataset 

- To train a deep learning model, you need to use data, and the api specification for using it is called a dataset. In Pytorch, these apis are implemented with the torch.utils.data.Dataset class. Therefore, if you use the Dataset class in Pytorch, you do not need to implement it yourself. However, it can be said that it is helpful to study because it is implemented to override some methods.

  ## Map-style dataset

  A map-style dataset represents a map from (non-integral)indicies or keys to a data sample.

  The map-style dataset must implement the following protocol(method).

  - `__getitem(idx)__ `
  - `__len__`

  It is torch's utils.data.Dataset class that provides APIs for these map-style datasets. This is an abstract class, and the method that must be implemented is `__ getitem(idx)__`, and the optional part is `__len()__`.

  `__getitem(idx)__` helps to fetch a data sample given some key(idx) . In this case, the key does not have to be an integer.

  The `__len__` method is absolutely necessary when iterates the dataloader. The docs states that many sampler implementations expect to return the dataset size due to the default option of the daatloader.

  In other words, sequentialsampler is used as the default value. This sequentialsampler requires the len() method of the dataset in the `iter` method, so you need to implement the len method. Python's built-in iter method doesn't require `__len__` method, probably overriding it in Pytorch.

  ### Using with Dataloader

  When no argument is passed to the sampler, an index sampler that yields an integer index is created.

  Due to the nature of the map-style dataset, it may have a non-integer index, in which case a custom sampler is required. (It will be explained in detail later in the Sampler part.)

  ## Iterable-style dataset

  An instance of a subclass of IterableDataset is called an iterable-style dataset.

  An iterable-style dataset must implement the following protocol(method).

  - `__iter__()`

  An iterable style is iterable over data samples. Since the `__getitem__` method is not implemented, random read is expensive and impossible, and the batch size varies depending on the fetched data.

  Due to the nature of the `iter` method, it is suitable for databases, remote servers, and real-time logs that cannot be stored in memory at once to return a stream.

  ### Using with Dataloader

  Unlike map-style datasets, it returns an iterator that yields each item in the dataset.

  # Data loading in map-style dataset

  Iterable dataset has the following characteristics.

  - Data loading order is determined according to user-defined iterable.
  - You can easily implement arbitrary chunk reading and dynamic batch size.

  Q. So, what about Map-style datasets?

  A. In case of map-style, sampler is used.

  ## Sampler

  In the case of `torch.utils.data.Sampler`, it has the following characteristics.

  - Specify the sequence of [indicies/or(key)], and use this sequence for data loading.
  - An iterable object for indicies.

  Which sampler is used depends on the argument related to the dataloader's sampler.

  - In Dataloader, if the shuffle argument is None, SequentialSampler , otherwise, the shuffled sampler is assigned as the default sampler.

  - For the sampler argument, SequentialSampler is used by default even if no arugment is passed.
  - If you pass the custom sampler to the sampler arugment, you can use the custom sampler.
  - You can use a custom sampler that yields batch indicies for the batch sampler argument.

## Integer Indicies Sampler

```python
class MySamplerTestDataset(torch.utils.data.Dataset) :

    def __init__(self , inp):
        self.inp  =inp

    def __getitem__(self , idx) :
        return self.inp[idx]
        
    def __len__(self):

        return len(self.inp)

    
    
```



#### default sampler



```python
a = np.arange(100)

IntegerIndiciesSamplerdataset = MySamplerTestDataset(a)

print(list(torch.utils.data.DataLoader(IntegerIndiciesSamplerdataset,sampler=None )))



```

```bash
[tensor([0]), tensor([1]), tensor([2]), tensor([3]), tensor([4]), tensor([5]), tensor([6]), tensor([7]), tensor([8]), tensor([9]), tensor([10]), tensor([11]), tensor([12]), tensor([13]), tensor([14]), tensor([15]), tensor([16]), tensor([17]), tensor([18]), tensor([19]), tensor([20]), tensor([21]), tensor([22]), tensor([23]), tensor([24]), tensor([25]), tensor([26]), tensor([27]), tensor([28]), tensor([29]), tensor([30]), tensor([31]), tensor([32]), tensor([33]), tensor([34]), tensor([35]), tensor([36]), tensor([37]), tensor([38]), tensor([39]), tensor([40]), tensor([41]), tensor([42]), tensor([43]), tensor([44]), tensor([45]), tensor([46]), tensor([47]), tensor([48]), tensor([49]), tensor([50]), tensor([51]), tensor([52]), tensor([53]), tensor([54]), tensor([55]), tensor([56]), tensor([57]), tensor([58]), tensor([59]), tensor([60]), tensor([61]), tensor([62]), tensor([63]), tensor([64]), tensor([65]), tensor([66]), tensor([67]), tensor([68]), tensor([69]), tensor([70]), tensor([71]), tensor([72]), tensor([73]), tensor([74]), tensor([75]), tensor([76]), tensor([77]), tensor([78]), tensor([79]), tensor([80]), tensor([81]), tensor([82]), tensor([83]), tensor([84]), tensor([85]), tensor([86]), tensor([87]), tensor([88]), tensor([89]), tensor([90]), tensor([91]), tensor([92]), tensor([93]), tensor([94]), tensor([95]), tensor([96]), tensor([97]), tensor([98]), tensor([99])]
```



In the case of the default sampler, sequentialsampler is used, so you can see that the output is in order as above.

#### shuffle sampler

```python
Randomsampler = torch.utils.data.RandomSampler(IntegerIndiciesSamplerdataset)

print(list(torch.utils.data.DataLoader(IntegerIndiciesSamplerdataset , shuffle = True)))
```

```
[tensor([63]), tensor([32]), tensor([15]), tensor([25]), tensor([9]), tensor([31]), tensor([87]), tensor([68]), tensor([10]), tensor([42]), tensor([21]), tensor([72]), tensor([24]), tensor([56]), tensor ([71]), tensor([70]), tensor([78]), tensor([14]), tensor([73]), tensor([83]), tensor([76]), tensor( [16]), tensor([18]), tensor([5]), tensor([49]), tensor([29]), tensor([39]), tensor([77]), tensor([ 26]), tensor([45]), tensor([52]), tensor([8]), tensor([86]), tensor([17]), tensor([7]), tensor([88] ]), tensor([59]), tensor([55]), tensor([54]), tensor([35]), tensor([22]), tensor([0]), tensor([57] ), tensor([92]), tensor([20]), tensor([64]), tensor([75]), tensor([43]), tensor([85]), tensor([1]) , tensor([2]), tensor([89]), tensor([46]), tensor([28]), tensor([11]), tensor([82]), tensor([80]), tensor([91]), tensor([27]), tensor([6]), tensor([79]), tensor([44]), tensor([19]), tensor([58]), tensor ([4]), tensor([47]), tensor([90]), tensor([51]), tensor([81]), tensor([33]), tensor([67]), tensor( [50]), tensor([61]), tensor([3]), tensor([97]), tensor([93]), tensor([84]), tensor([48]), tensor([34]), tensor ([66]), tensor([62]), tensor([41]), tensor([53]), tensor([23]), tensor([74]), tensor([60]), tensor( [96]), tensor([30]), tensor([69]), tensor([12]), tensor([37]), tensor([65]), tensor([98]), tensor([ 13]), tensor([36]), tensor([38]), tensor([94]), tensor([95]), tensor([99]), tensor([40])]

```

If you set the shuffle argument to true, you can see that it is shuffled as above.



## Non-integral sampler

```
a = np.arange(100)

b = np.arange(100 , 200)

c = np.arange(200,300)

dic = {'a' : a, 'b' :b , 'c' : c}

class MySamplerTestDataset(torch.utils.data.Dataset) :

    def __init__(self , inp):
        self.inp = inp

    def __getitem__(self , idx) :
        print(f"idx : {idx}")
        return self.inp[idx]
        ...
    def __len__(self):

        return len(self.inp)

    
```

```
class CustomSampler(torch.utils.data.Sampler) :
    def __init__(self, data_source) :
        self.data_source = data_source
    def __iter__(self) :
        print('hi')

        return iter(self.data_source)
        # return iter(['a' , 'b' , 'c'])
    def __len__(self) :
        return len(self.data_source)
```

```
NonintegerSamplerTestDataset = MySamplerTestDataset(dic)

nonintegersampler = CustomSampler(dic.keys())

print(list(torch.utils.data.DataLoader(NonintegerSamplerTestDataset , sampler = nonintegersampler)))
```

```
hi
idx: a
idx: b
idx: c
[tensor([[ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17,
         18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35,
         36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, 52, 53,
         54, 55, 56, 57, 58, 59, 60, 61, 62, 63, 64, 65, 66, 67, 68, 69, 70, 71,
         72, 73, 74, 75, 76, 77, 78, 79, 80, 81, 82, 83, 84, 85, 86, 87, 88, 89,
         90, 91, 92, 93, 94, 95, 96, 97, 98, 99]]), tensor([[100, 101, 102, 103, 104, 105, 106, 107, 108, 109, 110, 111 , 112, 113,
         114, 115, 116, 117, 118, 119, 120, 121, 122, 123, 124, 125, 126, 127,
         128, 129, 130, 131, 132, 133, 134, 135, 136, 137, 138, 139, 140, 141,
         142, 143, 144, 145, 146, 147, 148, 149, 150, 151, 152, 153, 154, 155,
         156, 157, 158, 159, 160, 161, 162, 163, 164, 165, 166, 167, 168, 169,
         170, 171, 172, 173, 174, 175, 176, 177, 178, 179, 180, 181, 182, 183,
         184, 185, 186, 187, 188, 189, 190, 191, 192, 193, 194, 195, 196, 197,
         198, 199]]), tensor([[200, 201, 202, 203, 204, 205, 206, 207, 208, 209, 210, 211, 212, 213,
         214, 215, 216, 217, 218, 219, 220, 221, 222, 223, 224, 225, 226, 227,
         228, 229, 230, 231, 232, 233, 234, 235, 236, 237, 238, 239, 240, 241,
         242, 243, 244, 245, 246, 247, 248, 249, 250, 251, 252, 253, 254, 255,
         256, 257, 258, 259, 260, 261, 262, 263, 264, 265, 266, 267, 268, 269,
         270, 271, 272, 273, 274, 275, 276, 277, 278, 279, 280, 281, 282, 283,
         284, 285, 286, 287, 288, 289, 290, 291, 292, 293, 294, 295, 296, 297,
         298, 299]])]
```



Now let's look at an example of non-integral indicies.

In the above example, numpy arrays a , b , and c are set as items, and each key is set to a , b and c.

When overriding the `iter` method, the word hi is printed. You can see that the iterator for the sampler is assigned only once. This is because the iterator is called only once and the data sample is yielded by the next method.

## Custom batch Sampler

In the example above, the sampler yields all indicies, and iterates each data sample at a time with a for loop. However, it allows you to iterate data samples in batches using a custom batch sampler.

```python
class MySamplerTestDataset(torch.utils.data.Dataset) :

    def __init__(self , inp):
        self.inp = inp

    def __getitem__(self , idx) :
        return self.inp[idx]
        ...
    def __len__(self):

        return len(self.inp)

    
class CustomSampler(torch.utils.data.Sampler) :
    def __init__(self, data_source) :
        self.data_source = data_source
    def __iter__(self) :
        print('hi')

        return iter(np.split(self.data_source,10))
    def __len__(self) :
        return len(self.data_source)
a = np.arange(100)
dds = MySamplerTestDataset(a)
customsampler = CustomSampler(a)
```

When defining the dataset, this time, the data set is divided into 10 groups and an iterator is returned.

### using default sampler argument

```
# Combine a sequence with 10 elements into two batches and return it as a tensor.


#for indices in batch_sampler:
    # yield collate_fn([dataset[i] for i in indices])
ddd = list(torch.utils.data.DataLoader(dds,sampler = customsampler , batch_size = 1 ))
for dd in ddd :
    print(dd)
```



```
hi
tensor([[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]])
tensor([[10, 11, 12, 13, 14, 15, 16, 17, 18, 19]])
tensor([[20, 21, 22, 23, 24, 25, 26, 27, 28, 29]])
tensor([[30, 31, 32, 33, 34, 35, 36, 37, 38, 39]])
tensor([[40, 41, 42, 43, 44, 45, 46, 47, 48, 49]])
tensor([[50, 51, 52, 53, 54, 55, 56, 57, 58, 59]])
tensor([[60, 61, 62, 63, 64, 65, 66, 67, 68, 69]])
tensor([[70, 71, 72, 73, 74, 75, 76, 77, 78, 79]])
tensor([[80, 81, 82, 83, 84, 85, 86, 87, 88, 89]])
tensor([[90, 91, 92, 93, 94, 95, 96, 97, 98, 99]])
```



If you use the default sampler agument, you can see that the result is a list of lists.

The dataloader yields batched samples instead of individual samples with batch_size and drop_last arguments. Therefore, in the above example, each sample is used as 10 groups, so 10 groups become individual batch_samples, and individual batch samples as much as batch_size are passed as an argument to collate_fn as a list. Therefore, the list of list comes out as a result value.

```python
# Combine a sequence with 10 elements into two batches and return it as a tensor.

#for index in sampler:
    # yield collate_fn(dataset[index])

ddd = list(torch.utils.data.DataLoader(dds,sampler = customsampler , batch_size = 2 ))
for dd in ddd :
    print(dd)
    
hi
tensor([[ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9],
        [10, 11, 12, 13, 14, 15, 16, 17, 18, 19]])
tensor([[20, 21, 22, 23, 24, 25, 26, 27, 28, 29],
        [30, 31, 32, 33, 34, 35, 36, 37, 38, 39]])
tensor([[40, 41, 42, 43, 44, 45, 46, 47, 48, 49],
        [50, 51, 52, 53, 54, 55, 56, 57, 58, 59]])
tensor([[60, 61, 62, 63, 64, 65, 66, 67, 68, 69],
        [70, 71, 72, 73, 74, 75, 76, 77, 78, 79]])
tensor([[80, 81, 82, 83, 84, 85, 86, 87, 88, 89],
        [90, 91, 92, 93, 94, 95, 96, 97, 98, 99]])
```

If batch_size is set to 2, the result will be as shown above.



### using batch sampler argument



```python
ddd = list(torch.utils.data.DataLoader(dds,batch_sampler = customsampler ))

#for indices in batch_sampler:
    # yield collate_fn([dataset[i] for i in indices])
for dd in ddd :
    print(dd)
```

```
hi
tensor([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
tensor([10, 11, 12, 13, 14, 15, 16, 17, 18, 19])
tensor([20, 21, 22, 23, 24, 25, 26, 27, 28, 29])
tensor([30, 31, 32, 33, 34, 35, 36, 37, 38, 39])
tensor([40, 41, 42, 43, 44, 45, 46, 47, 48, 49])
tensor([50, 51, 52, 53, 54, 55, 56, 57, 58, 59])
tensor([60, 61, 62, 63, 64, 65, 66, 67, 68, 69])
tensor([70, 71, 72, 73, 74, 75, 76, 77, 78, 79])
tensor([80, 81, 82, 83, 84, 85, 86, 87, 88, 89])
tensor([90, 91, 92, 93, 94, 95, 96, 97, 98, 99])
```



If so, what happens if you use the batch_sampler argument?

If you use batch_sampler, iterate batch_sampler to get 10 indicies, make it a list with list comprehension and apply collate_fn. So, the result is a list.

```python
ddd = list(torch.utils.data.DataLoader(dds,batch_sampler = customsampler ,batch_size= 3 ))

#for indices in batch_sampler:
    # yield collate_fn([dataset[i] for i in indices])
for dd in ddd :
    print(dd)
    
    
```

```
------------------------------------------------------------ -------------------------
ValueError Traceback (most recent call last)
<ipython-input-16-05e1dcc877a7> in <module>()
----> 1 ddd = list(torch.utils.data.DataLoader(dds,batch_sampler = customsampler ,batch_size= 3 ))
      2
      3 #for indices in batch_sampler:
      4 # yield collate_fn([dataset[i] for i in indices])
      5 for dd in ddd:

/usr/local/lib/python3.7/dist-packages/torch/utils/data/dataloader.py in __init__(self, dataset, batch_size, shuffle, sampler, batch_sampler, num_workers, collate_fn, pin_memory, drop_last, timeout, worker_init_fn , multiprocessing_context, generator, prefetch_factor, persistent_workers)
    249 # auto_collation with custom batch_sampler
    250 if batch_size != 1 or shuffle or sampler is not None or drop_last:
--> 251 raise ValueError('batch_sampler option is mutually exclusive '
    252 'with batch_size, shuffle, sampler, and '
    253 'drop_last')

ValueError: batch_sampler option is mutually exclusive with batch_size, shuffle, sampler, and drop_last
```

If batch_size and drop_last are used together when batch_sampler argument is not None, you should know that batch_sampler cannot be used together because batch_szie, shuffle, sampler and drop_last argument are mutually exclusive.

## Iterative with Sampler not allowed



```
a = np.arange(100)

b = np.arange(100 , 200)

c = np.arange(200,300)

dic = {'a' : a, 'b' :b , 'c' : c}

class MySamplerTestIterableDatset(torch.utils.data.IterableDataset) :
    def __init__(self,inp) :
        self.inp = inp
    def __iter__(self) :
        return iter(self.inp)
    def __len__(self) :
        return len(self.inp)

itercustomsamplerdataset = MySamplerTestIterableDatset(dic)

```

```
itersampler = torch.utils.data.RandomSampler(itercustomsamplerdataset)

# Do not apply sampler, batch_sampler, and batch_size to iterable dataset.

print(list(torch.utils.data.DataLoader(itercustomsamplerdataset ,batch_size = 4, batch_sampler = itersampler))))
```

```
------------------------------------------------------------ -------------------------
ValueError Traceback (most recent call last)
<ipython-input-36-5a86c478aaf9> in <module>()
      1 # Do not apply sampler, batch_sampler and batch_size to iterable dataset.
      2
----> 3 print(list(torch.utils.data.DataLoader(itercustomsamplerdataset ,batch_size = 4, batch_sampler = itersampler))))

/usr/local/lib/python3.7/dist-packages/torch/utils/data/dataloader.py in __init__(self, dataset, batch_size, shuffle, sampler, batch_sampler, num_workers, collate_fn, pin_memory, drop_last, timeout, worker_init_fn , multiprocessing_context, generator, prefetch_factor, persistent_workers)
    238 raise ValueError(
    239 "DataLoader with IterableDataset: expected unspecified "
--> 240 "batch_sampler option, but got batch_sampler={}".format(batch_sampler))
    241 else:
    242 self._dataset_kind = _DatasetKind.Map

ValueError: DataLoader with IterableDataset: expected unspecified batch_sampler option, but got batch_sampler=<torch.utils.data.sampler.RandomSampler object at 0x7f5511cf1690>
```

Since Sampler yields indicies, it cannot be used with Iterable Dataset that iterates using iterator.

That is, batch_sampler and sampler argument cannot be used in Iterable Dataset.