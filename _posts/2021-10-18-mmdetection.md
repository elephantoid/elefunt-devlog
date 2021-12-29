---
title: "mmcv : mmdetection 사용법"
description: "how to use mmdetection"
layout: post
toc: false
comments: true
search_exclude: true
categories: [ML/DL]
image: "/images/mmdetection.png"
---

# mmDetection 사용법

[mmdetection 사용법](https://www.youtube.com/playlist?list=PLrJaZ4ogQUHzc2wtaptvXX8r600hdyvy0)

DACON에서 mmdetection 활용하는 방법에 관해 자세히 설명해주는 영상이 있어서 수월하게 mmdetection을 사용할 수 있었습니다.
자 그럼 일단 mmdetection이 무엇인지 말씀드리겠습니다.
[mmcv github](https://mmcv.readthedocs.io/en/latest/#installation)에는 다양한 컴퓨터 비전(Computer Vision) 모델들을 공개하여 사용할 수 있게끔 해주는 오픈 소스 라이브러리들이 있습니다.  
그 중에 저는 CCTV Object detection task를 해야하기 때문에 mmdetection을 활용했습니다. 혹시 Segmentation을 하실 분들은  mmSegmentation도 있습니다.

## Installation

> **Prerequisites**
> 
- Linux or macOS (Windows is in experimental support)
- Python 3.6+
- PyTorch 1.3+
- CUDA 9.2+ (If you build PyTorch from source, CUDA 9.0 is also compatible)
- GCC 5+
- [MMCV](https://mmcv.readthedocs.io/en/latest/#installation)
    - `pip install mmcv-full**=={**mmcv_version**}** -f https://download.openmmlab.com/mmcv/dist/**{**cu_version**}**/**{**torch_version**}**/index.html`
    - cuda와 torch 버전에 맞게끔 설치해야 합니다.
    - MMCV버전에 따라 MMDetection의 버전도 상이하기 때문에 잘 보고 설치해야 합니다.
    - `**MMDetection version` | `MMCV version`**
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b2acd6d7-7543-4a60-87e1-576e69cd1f5d/Untitled.png)
        

cuda와 gcc 버전 체크

```python
# Check nvcc version
!nvcc -V
# Check GCC version
!gcc --version
```

설치된 패키지 버전 체크

```python
# Check Pytorch installation
import torch, torchvision
print(torch.__version__, torch.cuda.is_available())

# Check MMDetection installation
import mmdet
print(mmdet.__version__)

# Check mmcv installation
from mmcv.ops import get_compiling_cuda_version, get_compiler_version
print(get_compiling_cuda_version())
print(get_compiler_version())
#--------------------------------------------------------------------------
1.5.0 True
2.4.0
10.1
GCC 7.3
```

## Preprocessing

사용하는 모델에 따라 지정된 annotation format을 따라 주셔야합니다.
Yolo라면 txt로 되어있는 파일들 coco format이라면 json으로 작성된 파일을 준비해주세요

```python
datasets
├── train
│   ├── train_ann.json
│   └── train_images
│       └── img_jpgs
├── test
│   ├── test_ann.json
│   └── train_images
│       └── img_jpgs
└── val
    ├── val_ann.json
    └── val_images
        └── img_jpgs
```

## Train and Test

> using files
> 
- configs/_base_/
    - default_runtime.py
    - datasets/coco_detection.py
    - models/{base_model}
    - schedules/schedule_1x.py
- load_from=model.pth → mmdetection/configs/model에서 다운로드

`coco_detection.py` , `{base_model}` , `schedule_1x.py` 를 default_runtime.py 파일에 넣어주세요

```python
dataset_type = 'CocoDataset'
data_root = '데이터 root dir'
CLASSES = ('여러분들의 Classes')
'''
coco detection.py
1. data root 지정
'''
img_norm_cfg = dict(
    mean=[123.675, 116.28, 103.53], std=[58.395, 57.12, 57.375], to_rgb=True)
train_pipeline = [
    dict(type='LoadImageFromFile'),
    dict(type='LoadAnnotations', with_bbox=True),
    dict(type='Resize', img_scale=(1333, 800), keep_ratio=True),
    dict(type='RandomFlip', flip_ratio=0.5),
    dict(type='Normalize', **img_norm_cfg),
    dict(type='Pad', size_divisor=32),
    dict(type='DefaultFormatBundle'),
    dict(type='Collect', keys=['img', 'gt_bboxes', 'gt_labels']),
.
.
.
'''
base model.py
1. 사용하고자 하는 모델 method를 선정하세요
2. 해당 python 파일 상단에 있는 _base_를 따라가면 base model를 찾을 수 있습니다.
3. copy and paste 이후 num_classes를 변경해 주세요 =len(CLASSES)
4. 1번에서 선택한 method를 github에서 찾으시고 페이지 하단 model의 링크를 copy
5. load_from='copy한 model 링크'
'''
model = dict(
    type='FasterRCNN',
    pretrained='torchvision://resnet50',
    backbone=dict(
        type='ResNet',
        depth=50,
        num_stages=4,
        out_indices=(0, 1, 2, 3),
        frozen_stages=1,
        norm_cfg=dict(type='BN', requires_grad=True),
        norm_eval=True,
        style='pytorch')
.
.
.
'''
schedule.py
적절한 lr를 선정하세요
만약 single GPU일 경우에는 epochs를 x8해주시면 됩니다. mmdetection에서는 8gpu를 사용했기 때문입니다.
자세한 내용은 git_version tag {ex)2.4} docs/get_started.md 파일에 나와있습니다
'''
# optimizer
optimizer = dict(type='SGD', lr=0.0025, momentum=0.9, weight_decay=0.0001)
optimizer_config = dict(grad_clip=None)
# learning policy
lr_config = dict(
    policy='step',
    warmup='linear',
    warmup_iters=500,
    warmup_ratio=0.001,
    step=[8, 11])
total_epochs = 96

checkpoint_config = dict(interval=12)
# yapf:disable
log_config = dict(
    interval=50,
    hooks=[
        dict(type='TextLoggerHook'),
        # dict(type='TensorboardLoggerHook')
    ])
# yapf:enable
dist_params = dict(backend='nccl')
log_level = 'INFO'
load_from = None
resume_from = None
workflow = [('train', 1)]
```

> train
> 

```python
!python tools/train.py configs/_base_/default_runtime.py
```

> test
> 
1. 테스트 결과 시각화하기

```python
!python tools/test.py configs/*base*/default_runtime.py work_dirs/default_runtime/epoch_60.pth --show-dir work_dirs/result
```

1. 테스트 결과 json 파일로 저장하기

```python
!python tools/test.py configs/_base_/default_runtime.py work_dirs/default_runtime/epoch_60.pth --format-only --eval-options "jsonfile_prefix=/home/work/mmdetection-2.4.0/test_results"
#       {test.py}       {config file root}                  {model file root}                                                  {where you want to save the file as a json}
# output 
# id
# score
# bbox(x,y,w,h)
# confidence
```

### coco2yolo

```python
def yolobbox2bbox(x,y,w,h):
    x1, y1 = x-w/2, y-h/2
    x2, y2 = x+w/2, y+h/2
    return x1, y1, x2, y2
```