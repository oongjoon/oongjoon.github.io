---
title : "Huggingface:Datasets"



excerpt: "Huggingface Datasets"

categories:
  - Huggingface
tags:
  - [Machine Learning,Huggingface,deep learning ,Dataset]
# classes : wide
toc: true
toc_sticky: true
---
## Huggingface Datasets

Huggingface provides a Module called Datasets. In this article, I would like to introduce Huggingface's Datasets and introduce simple methods and attributes that I use frequently.

## Datasets Arrow

Huggingface Datasets caches the dataset with an arrow in local when loading the dataset from the external filesystem. Arrow is designed to process large amounts of data quickly. Arrow is especially specialized for column-oriented data.

Additional characteristics will be updated again as we learn more.

## load_dataset

![image](https://user-images.githubusercontent.com/50165842/147385759-663a26d3-a631-4bf5-a3c0-c96d70276d8a.png)

Huggingface Datasets supports creating Datasets classes from CSV, txt, JSON, and parquet formats. load_datasets returns a Dataset dict, and if a key is not specified, it is mapped to a key called 'train' by default.

### txt

```python
load_dataset('txt' , data_files='my_file.txt')
```

To load a txt file, specify the path and txt type in data_files. A text file reads data line by line to create a datasets dict.

### csv

```python
load_dataset('csv' , data_files='my_file.csv')
```

To load a csv file, you just need to specify the path and csv type in data_files. Similarly, the csv file reads data line by line to create a datasets dict.

### json

```python
from datasets import  load_dataset

load_dataset('json' , data_files='my_file.json')
```

When loading a json file, it can be divided into two types. The first is when all rows in the file are json objects. In this case, you can pass the path of the file to the data_files argument.

```python
load_dataset('json' , data_files = 'my_file.json' , field='data')
```

In the second case, the json file is nested in multiple fields. My personal opinion is that there are multiple tables in one large dataset, similar to loading only the necessary tables. In this case, you need to specify which field to load in the field argument.

~~~python
load_dataset('json' , data_files = {'train' : 'my_train.json' , 'validation' : 'my_valid.json'} ,field = 'data')

```
    DatasetDict({
        train: Dataset({
            features: ['id', 'context', 'question', 'answers', 'class_type'],
            num_rows: 226887
        })
        validation: Dataset({
            features: ['id', 'context', 'question', 'answers', 'class_type'],
            num_rows: 18116
        })
    })
```
~~~

It is also possible to map two or more files to the key value specified in the datasets dict.

### summary

In summary, all three of txt, csv, and json can be loaded by reading data line by line. However, in the case of json, if it is a nested structure, it can be read by selecting a field.

## slice,shuffle

```python
load_dataset('json' , data_files = 'my_file.json' , field='data').select(1000)
```

If you do not need to load all the data, you can load only a part by using the select method. select returns a Datasets dict. The above code loads only 1000 data. However, select creates subsets sequentially.

```python
indicies = [10,20,30,40]
load_dataset('json' , data_files = 'my_file.json' , field='data').select(indicies)
```

You can also select a subset by creating a desired index list.

```python
load_dataset('json' , data_files = 'my_file.json' , field = 'data').shuffle(seed=1).select(1000)
```

If you want to make a random subset, you can make it by applying shuffle first and then selecting. To perform the same experiment every time, it is recommended to always set the seed to the same value.

## filter,map

```python
dataset.filter(lambda x  : x["title"].startswith('L'))
```

By using the filter method, you can return a dataset dict that satisfies a specific condition.

```python
dataset.map(tolower)
```

dataset provides map method. It works just like python's map method. Mainly, the map method is used when preprocessing is required for each row.

```python
dataset.map(tokenizing_word , batched= True, batch_size = 5000)
```

The map method also allows you to pass rows of a dataset to a function in batches at once. This is especially useful when tokenizing. With multithreading, multiple batches are processed at once.

## remove_columns , rename_columns , flatten

```python
datasets.rename_columns("context" , "passage")
```

The above code is the code to replace the column named "context" with the column named "passage". Unlike the above codes, rename_columns is a method to change the datasets dict object. Therefore, it does not return a dataset dict.

```python
datasets.remove_columns(["context" ,"passage"])
```

The code above deletes the columns named "context" and "passage".

```python
datasets.flatten(["answers"])
```

flatten is a method to denormalize nested columns.

## to_json,to_csv,..etc

![image](https://user-images.githubusercontent.com/50165842/147386907-cd1f584f-b964-4ac8-b878-66261f507f40.png)

Finally, the returned dataset can be exported to another format.