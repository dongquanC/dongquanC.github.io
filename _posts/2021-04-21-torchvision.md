---
layout: post
title: torchvision 
categories: [程序语言]
tags: [python]
excerpt: torchvision与torch.utils.data读取图片数据的操作。
date: 2021-04-21
lastmod: 2021-04-21
comments: false
---

# torch 预处理图形数据
* torchvision
* torch.utils.data 对象构建

# torchvision
## torchvision-transform
[torchvision-transform 中文文档](https://pytorch-cn.readthedocs.io/zh/latest/torchvision/torchvision-transform/)

### 对PIL.Image进行变换

#### class torchvision.transforms.Compose(transforms)
将多个transform组合起来使用。  
参数transforms是由transform构成的列表。  
exp:  
```python
from torchvision import transforms
from PIL import Image

image = Image.open('test.png')
crop = transforms.Compose([transforms.CenterCrop(50),transforms.ToTensor()])
train_data = crop(image)
```
exp中transforms.CenterCrop(50)等列表元素均是torchvision.transforms中的方法，是对图片做处理的方法。

#### class torchvision.transforms.CenterCrop(size)
pass

#### class torchvision.transforms.RandomCrop(size, padding=0)
pass

#### class torchvision.transforms.RandomHorizontalFlip
pass

#### class torchvision.transforms.RandomSizedCrop(size, interpolation=2)
pass

#### class torchvision.transforms.Pad(padding, fill=0)
pass

### 对Tensor进行变换

#### class torchvision.transforms.ToTensor
pass

#### class torchvision.transforms.ToPILImage
pass

### 在我目前的项目超分辨率中需要做的预处理代码
```python
crop = transforms.Compose([transforms.RandomCrop(100),transforms.ToTensor()])
```

## torchvision.datasets
只做读取图片文件夹的解释，其他参考[torchvision-datasets 中文文档](https://pytorch-cn.readthedocs.io/zh/latest/torchvision/torchvision-datasets/)

### class torchvision.datasets.ImageFolder
**原型**  
```python
dataset=torchvision.datasets.ImageFolder(
                       root, transform=None, 
                       target_transform=None, 
                       loader=<function default_loader>, 
                       is_valid_file=None)
```
**参数解释**
* root 图片存储的根目录，即各类别文件夹所在目录的上一级目录。
* transform 对图片进行预处理的操作（函数），原始图片作为输入，返回一个转换后的图片。对应torchvision-transform。
* target_transform 对图片类别进行预处理的操作，输入为target，输出对其的转换。
* loader：表示数据集加载方式，通常默认加载方式即可。
* is_valid_file：获取图像文件的路径并检查该文件是否为有效文件的函数(用于检查损坏文件)
**返回的dataset的三种属性**
* self.classes：用一个list保存类别名称
* self.class_to_idx：类别对应的索引，与不做任何转换返回的target对应
* self.imgs：保存(img-path, class)tuple的list  
exp:

```python
import torchvision.datasets as dset

data = dset.ImageFolder('image/')
print(data.classes, data.class_to_idx)
print(data.imgs[0])

# 运行结果
['target', 'train'] {'target': 0, 'train': 1}
('image/target\\100075.jpg', 0)

# image/ 文件夹结构
- image
 - train
  - 8049.jpg
  - 8143.jpg
  - ...
 - target
  - 8049.jpg
  - 8143.jpg
  - ...
```

## torch.utils.data
[torch.utils.data 中文文档](https://pytorch-cn.readthedocs.io/zh/latest/package_references/data/)

### lass torch.utils.data.DataLoader

**函数原型**
```
lass torch.utils.data.DataLoader(
                        dataset, 
                        batch_size=1, 
                        shuffle=False, 
                        sampler=None, 
                        num_workers=0, 
                        collate_fn=<function default_collate>, 
                        pin_memory=False, drop_last=False)
```