---
title: "Datasets and DataLoaders: Part 1"
layout: post
date: 2025-08-15"
categories: [PyTorch Basics]
---

# TL;DR

1. What is a PyTorch `Dataset`?

   > a PyTorch `Dataset` is a class that knows **how many data samples it contains** and can **return a specific sample when you ask for it by its index**. Both the way it determines the total number of samples and the way it retrieves an individual sample are **fully customizable**.

2. What's the purpose of a PyTorch `Dataset`?

   > The purpose of a `Dataset` object is to define a way of accessessing your data during model training.

3. Why should you care?
   > By defining a way to access your data and return the size of your data, the `Dataset` object allows you to train a model on any data source (images, CSV files, audio, etc.) just by changing the Dataset implementation.

# Intro

My first encounter with the PyTorch `Dataset` and `DataLoader` objects was 3 years ago, when I was going through PyTorch recipes and HuggingFace tutorials for the first time. I'll admit I tended to follow tutorials rather blindly, which led to never really understanding how PyTorch worked.

Fast forward 3 years, here I am trying to relearn everything from the ground up. While I was going through some PyTorch basics I inevitably came across the `Dataset` and `DataLoader`s section, and the confusion was still there. This time, I tried articulating my confusion into some questions that I can focus on while learning about the two data-related objects in PyTorch.

In this notebook, the first of the two-part series, I'll address my questions about the `Dataset` object as listed in the TL;DR:

1. What is a PyTorch `Dataset`?
2. What's the purpose of a PyTorch `Dataset`?
3. Why should you care?

```python
import os
from PIL import Image
import torch
from torch.utils.data import Dataset
import torchvision.transforms as transforms
```

# `Dataset` Objects

Here's the [definition of a `Dataset` from PyTorch](https://docs.pytorch.org/docs/stable/data.html#torch.utils.data.Dataset):

> All datasets that represent a map from keys to data samples should subclass it. All subclasses should overwrite `__getitem__()`, supporting fetching a data sample for a given key. Subclasses could also optionally overwrite `__len__()`, which is expected to return the size of the dataset by many Sampler implementations and the default options of DataLoader. Subclasses could also optionally implement `__getitems__()`, for speedup batched samples loading. This method accepts list of indices of samples of batch and returns list of samples.

Sorry PyTorch, this didn't make any sense to me.

Here were my initial confusions:

- What does the definition mean by "represent a map from keys to data samples"?
- How do I explain what this is without bringing in the concept of a DataLoader?
- I think the problem is that when I hear "Dataset", I imagine a table in a relational database

So let's unpack this.

First, when you hear "dataset," a table with rows and columns instantly pops up in your head, try ignoring that. Instead, think of a Python list and how you'd access its elements.

```python
my_cats = ["Brie", "Chip"]
```

What do you do if you want to access `"Brie"`?

```python
my_cats[0] # This will return "Brie"
```

    'Brie'

Well... How did I know how to access "Brie"?

Because a Python list returns a the value at a given index position. It's a predefined rule: you give it the index position, and the list gives you the element at that position.

Now I want you to take this exact idea into understanding PyTorch's `Dataset` class.
Think of it as something that creates a list-like object. It is a class you write, where you define the rules to fetch **one entry** from your data, given an index.

Put it simply:

- `Dataset` index => list index
- `Dataset` entry => the value at the index

Here's an example of a `Dataset` class definition that reflects the `my_cats` list:

```python
class MyCatsDataset(Dataset):
    def __init__(self):
        self.data = ["Brie", "Chip"]  # My cats' names

    def __len__(self):
        return len(self.data)

    def __getitem__(self, idx):
        cat = self.data[idx]
        return cat

my_cats_ds = MyCatsDataset()

for i in range(len(my_cats_ds)):
    print(f"Cat #{i+1}: {my_cats_ds[i]}")
```

    Cat #1: Brie
    Cat #2: Chip

As you can see in the code block above:

- `__init__`: I defined the data -- a list of my cats' names
- `__len__`: A method to define how many items are in your data
- `__getitem__`: A method that defines how you want to be able to fetch your data

So, essentially, a PyTorch `Dataset` is a class that knows **how many data samples it contains** and can **return a specific sample when you ask for it by its index**. Both the way it determines the total number of samples and the way it retrieves an individual sample are **fully customizable**.

At the end of the day, it's just another wrapper around your raw data that tells PyTorch "Here's how you should access my data, and how you can figure out its size/shape." Without a dataset, PyTorch wouldn’t know how to get your data or what shape it’s in — it’s the foundation.

# Perhaps a more practical use case of defining a `Dataset` object

...would be if you have some unstructured data.

Say you're building an image classifier. You would probably need some images to train your model with, but you can't just use the image as is to train your model, right?
This is where `Dataset`s come in. By defining and initializing a `Dataset` object, we can define custom rules to how the model will process the image data.

```python
class CatsAndLionsDataset(Dataset):
    def __init__(self, root_dir):
        self.root_dir = root_dir
        self.classes = ['cats', 'lions']
        self.image_paths = [] # List to store image paths
        self.labels = []

        # Go through each class folder
        for label, class_name in enumerate(self.classes):
            class_folder = os.path.join(root_dir, class_name)
            for file in os.listdir(class_folder):
                self.image_paths.append(os.path.join(class_folder, file))
                self.labels.append(label)

        # Transform images into tensors
        self.transform = transforms.Compose([
            transforms.Resize((128, 160)),
            transforms.ToTensor()
        ])

    def __len__(self):
        return len(self.image_paths)

    def __getitem__(self, idx):
        img_path = self.image_paths[idx]
        label = self.labels[idx]

        image = Image.open(img_path).convert("RGB")
        image = self.transform(image)

        return image, label
```

```python
cats_and_lions_ds = CatsAndLionsDataset(root_dir="image_data") # this directory contains 4 images in total, 2 of cats and 2 of lions
cats_and_lions_ds
```

    <__main__.CatsAndLionsDataset at 0x1180d48c0>

So what is this `CatsAndLionsDataset` doing?

Remember what I said about Dataset objects allowing you to define:

1. How your data is accessed, given an index position (e.g. What data is at index position 0?)
2. The length/size of your dataset (i.e. how many "entries" of data you have)

So let's take a look at the length first, since that's more straight-forward.

```python
len(cats_and_lions_ds)
```

    4

Makes sense, since I have 2 images of cats and 2 images of lions.

Now what about the first element in the `Dataset`?

```python
print(f"Data at index position 0: {cats_and_lions_ds[0]}")
print(f"Length of data at index position 0: {len(cats_and_lions_ds[0])}")
```

    Data at index position 0: (tensor([[[0.2000, 0.2078, 0.2157,  ..., 0.1804, 0.2039, 0.2275],
             [0.2353, 0.2353, 0.2392,  ..., 0.1804, 0.2039, 0.2275],
             [0.2431, 0.2431, 0.2431,  ..., 0.1843, 0.2078, 0.2314],
             ...,
             [0.2863, 0.3020, 0.3098,  ..., 0.3922, 0.4314, 0.4941],
             [0.2235, 0.2353, 0.2745,  ..., 0.2157, 0.2824, 0.3608],
             [0.2627, 0.2667, 0.2980,  ..., 0.2784, 0.3176, 0.3098]],

            [[0.2353, 0.2510, 0.2667,  ..., 0.1725, 0.1882, 0.2039],
             [0.2706, 0.2824, 0.2902,  ..., 0.1765, 0.1961, 0.2078],
             [0.2902, 0.2941, 0.3020,  ..., 0.1765, 0.1961, 0.2078],
             ...,
             [0.3294, 0.3294, 0.3137,  ..., 0.4353, 0.4863, 0.5608],
             [0.2196, 0.2196, 0.2549,  ..., 0.2392, 0.3176, 0.4039],
             [0.2353, 0.2510, 0.3255,  ..., 0.3059, 0.3412, 0.3333]],

            [[0.2118, 0.2235, 0.2353,  ..., 0.1216, 0.1412, 0.1569],
             [0.2392, 0.2471, 0.2549,  ..., 0.1255, 0.1451, 0.1608],
             [0.2549, 0.2588, 0.2627,  ..., 0.1294, 0.1490, 0.1647],
             ...,
             [0.1647, 0.1882, 0.2000,  ..., 0.5176, 0.5804, 0.6745],
             [0.1961, 0.2118, 0.2627,  ..., 0.2745, 0.3725, 0.4980],
             [0.2431, 0.2745, 0.3765,  ..., 0.3843, 0.4353, 0.4314]]]), 0)
    Length of data at index position 0: 2

As you can see, the data contains a tensor-ized version of the image (i.e. a numerical representation of the image), and the label. In this case, the blob of numbers you see represent one of the cat pictures I have, and the label `0` stands for the label `cat` (refer back to the class definition).

# So why does this matter?

Think of creating a `Dataset` object as the data-prep stage. You have some data (e.g. a bunch of image files), and you need to turn it into a format that you can use to train some kind of machine/deep learning model. By defining a way to access your data and return the size of your data, the `Dataset` object allows you to train a model on any data source (images, CSV files, audio, etc.) just by changing the Dataset implementation.

The reason why this class matters might make more sense once we dive into `DataLoaders`, which will be the second part to this series.

Until then, <br>Ael
