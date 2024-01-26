# Unified Perceptual Parsing for SceneUnderstanding


## Reference

> Tete Xiao, Yingcheng Liu, Bolei Zhou, Yuning Jiang, Jian Sun. "Unified Perceptual Parsing for Scene Understanding." Proceedings of the European Conference on Computer Vision (ECCV). 2018.
## 目录

- [1. 模型介绍](#1)
    - [1.1 模型简介](#1.1)
    - [1.2 模型指标](#1.2)
    - [1.3 Benchmark](#1.3)
      - [1.3.1 基于 V100 GPU 的预测速度](#1.3.1)
- [2. 模型训练、评估和预测](#2)
  - [2.1 环境配置](#2.1)
  - [2.2 数据准备](#2.2)
  - [2.3 模型训练](#2.3)
  - [2.4 模型评估](#2.4)
  - [2.5 模型预测](#2.5)
- [3. 模型推理部署](#3)
  - [3.1 推理模型准备](#3.1)
    - [3.1.1 模型准备](#3.1.1)
  - [3.2 基于 Python 预测引擎推理](#3.2)
    - [3.2.1 预测单张图像](#3.2.1)
  - [3.3 基于 C++ 预测引擎推理](#3.3)
  - [3.4 服务化部署](#3.4)
  - [3.5 端侧部署](#3.5)
  - [3.6 Paddle2ONNX 模型转换与预测](#3.6)

<a name='1'></a>

## 1. 模型介绍

<a name='1.1'></a>

### 1.1 模型简介

旷视科技提出一种称之为统一感知解析（Unified Perceptual Parsing/UPP）的新任务，要求机器视觉系统从一张图像中识别出尽可能多的视觉概念。同时，多任务框架 UPerNet 被提出，训练策略被开发以学习混杂标注（heterogeneous annotations）。旷视科技在 UPP 上对 UPerNet 做了基准测试，结果表明其可有效分割大量的图像概念。这一已训练网络进一步用于发现自然场景中的视觉知识。[论文地址](https://arxiv.org/pdf/1807.10221.pdf)。

<a name='1.2'></a>

### 1.2 模型指标

| Model | Backbone | Resolution | Training Iters | mIoU | mIoU (flip) | mIoU (ms+flip) | Links |
|-|-|-|-|-|-|-|-|
|UPerNet|ResNet101_OS8|512x1024|40000|79.58%|80.11%|80.64%|[model](https://bj.bcebos.com/paddleseg/dygraph/cityscapes/upernet_resnet101_os8_cityscapes_512x1024_40k/model.pdparams)\|[log](https://bj.bcebos.com/paddleseg/dygraph/cityscapes/upernet_resnet101_os8_cityscapes_512x1024_40k/train.log)\|[vdl](https://www.paddlepaddle.org.cn/paddle/visualdl/service/app/index?id=c635ae2e70e148796cd58fae5273c3d6)|

### 1.3 Benchmark

<a name='1.3.1'></a>

#### 1.3.1 基于 V100 GPU 的预测速度

敬请期待。

<a name="3"></a>

## 2. 模型训练、评估和预测

此部分内容包括训练环境配置、数据的准备、模型训练、评估、预测等内容。详细可参考[PaddleSeg使用文档](https://github.com/PaddlePaddle/PaddleSeg/blob/release/2.9/docs/whole_process_cn.md)

<a name="2.1"></a>  

### 2.1 环境配置

#### 安装 paddlepaddle

- 您的机器安装的是 CUDA9 或 CUDA10，请运行以下命令安装

```bash
python3 -m pip install paddlepaddle-gpu -i https://mirror.baidu.com/pypi/simple
```

- 您的机器是CPU，请运行以下命令安装

```bash
python3 -m pip install paddlepaddle -i https://mirror.baidu.com/pypi/simple
```

更多的版本需求，请参照[飞桨官网安装文档](https://www.paddlepaddle.org.cn/install/quick)中的说明进行操作。

#### 安装 paddleSeg

如果大家需要基于PaddleSeg进行开发和调试，推荐采用源码安装的方式。如果大家只是调用PaddleSeg，推荐安装发布的PaddleSeg包

从Github下载PaddleSeg代码。
```
git clone https://github.com/PaddlePaddle/PaddleSeg
```

如果连不上Github，可以从Gitee下载PaddleSeg代码，但是Gitee上代码可能不是最新。
```
git clone https://gitee.com/paddlepaddle/PaddleSeg.git
```

执行如下命令，从源码编译安装PaddleSeg包。大家对于PaddleSeg/paddleseg目录下的修改，都会立即生效，无需重新安装。

```
cd PaddleSeg
pip install -r requirements.txt
pip install -v -e .
```

安装发布的PaddleSeg
```
pip install paddleseg
```
在PaddleSeg目录下执行如下命令，会进行简单的单卡预测。查看执行输出的log，没有报错，则验证安装成功
```
sh tests/install/check_predict.sh
```

<a name="2.2"></a>

### 2.2 数据准备

对于公开数据集，大家只需要下载并存放到特定目录，就可以使用PaddleSeg进行模型训练评估.
PaddleSeg是按照如下数据集存放目录，来定义配置文件中默认的公开数据集路径。 所以，建议大家下载公开数据集，然后存放到PaddleSeg/data目录下。 如果公开数据集不是按照如下目录进行存放，大家需要根据实际情况，手动修改配置文件中的数据集目录。

```
PaddleSeg
├── data
│   ├── cityscapes
│   │   ├── leftImg8bit
│   │   │   ├── train
│   │   │   ├── val
│   │   ├── gtFine
│   │   │   ├── train
│   │   │   ├── val
│   ├── ADEChallengeData2016
│   │   │── annotations
│   │   │   ├── training
│   │   │   ├── validation
│   │   │── images
│   │   │   ├── training
│   │   │   ├── validation
│   ├── VOCdevkit
│   │   ├── VOC2012
│   │   │   ├── JPEGImages
│   │   │   ├── SegmentationClass
│   │   │   ├── SegmentationClassAug
│   │   │   ├── ImageSets
│   │   │   │   ├── Segmentation
```

使用cityscapes进行训练和测试，Cityscapes是关于城市街道场景的语义理解图片数据集。它主要包含来自50个不同城市的街道场景，拥有5000张（2048 x 1024）高质量像素级注释图像，包含19个类别。Cityscapes数据集的训练集2975张，验证集500张，测试集1525张。请前往[CityScapes官网](https://www.cityscapes-dataset.com/)下载数据集。 数据集结构如下:

```
    cityscapes
    |
    |--leftImg8bit
    |  |--train
    |  |--val
    |  |--test
    |
    |--gtFine
    |  |--train
    |  |--val
    |  |--test
```
下载原始数据集后，运行下面命令进行转换，其中cityscapes_path是数据集保存的根目录，num_workers是进程数。执行完成后，转换后的数据集依旧保存在原先数据集目录下。

```
pip install cityscapesscripts
python tools/data/convert_cityscapes.py --cityscapes_path data/cityscapes --num_workers 8
```

<a name="2.3"></a>

### 2.3 模型训练

在 `PaddleSeg/configs/upernet` 中提供了训练配置，可以通过如下脚本启动训练：

单卡训练
```shell
export CUDA_VISIBLE_DEVICES=0 # Linux上设置1张可用的卡
# set CUDA_VISIBLE_DEVICES=0  # Windows上设置1张可用的卡

python tools/train.py \
       --config ./configs/uhrnet/fcn_uhrnetw48_cityscapes_1024x512_80k.yml \
       --save_interval 500 \
       --do_eval \
       --use_vdl \
       --save_dir output
```

多卡训练

```
export CUDA_VISIBLE_DEVICES=0,1,2,3 # 设置4张可用的卡
python -m paddle.distributed.launch tools/train.py \
       --config ./configs/uhrnet/fcn_uhrnetw48_cityscapes_1024x512_80k.yml \
       --do_eval \
       --use_vdl \
       --save_interval 500 \
       --save_dir output
```

<a name="2.4"></a>

### 2.4 模型评估

训练好模型之后，可以通过以下命令实现对模型指标的评估。

```bash
python tools/val.py \
       --config ./configs/uhrnet/fcn_uhrnetw48_cityscapes_1024x512_80k.yml \
       --model_path output/best_model/model.pdparams
```

<a name="2.5"></a>

### 2.5 模型预测

模型训练完成之后，可以加载训练得到的预训练模型，进行模型预测。在模型库的 `tools/infer.py` 中提供了完整的示例，只需执行下述命令即可完成模型预测：

```python
python tools/predict.py \
       --config ./configs/uhrnet/fcn_uhrnetw48_cityscapes_1024x512_80k.yml \
       --model_path output/best_model/model.pdparams \
       --image_path data/optic_disc_seg/JPEGImages/H0002.jpg \
       --save_dir output/result
```


<a name="3"></a>

## 3. 模型推理部署


<a name="4.1"></a>

### 3.1 推理模型准备

Paddle Inference 是飞桨的原生推理库， 作用于服务器端和云端，提供高性能的推理能力。相比于直接基于预训练模型进行预测，Paddle Inference可使用MKLDNN、CUDNN、TensorRT 进行预测加速，从而实现更优的推理性能。更多关于Paddle Inference推理引擎的介绍，可以参考[Paddle Inference官网教程](https://www.paddlepaddle.org.cn/documentation/docs/zh/guides/infer/inference/inference_cn.html)。

当使用 Paddle Inference 推理时，加载的模型类型为 inference 模型。本案例提供了两种获得 inference 模型的方法，如果希望得到和文档相同的结果，请选择[直接下载 inference 模型](#6.1.2)的方式。

<a name="3.1.1"></a>

### 3.1.1 模型导出

上述模型训练、评估和预测，都是使用飞桨的动态图模式。动态图模式具有灵活、方便的优点，但是不适合工业级部署的速度要求。
为了满足工业级部署的需求，飞桨提供了动转静的功能，即将训练出来的动态图模型转化成静态图预测模型。预测引擎加载、执行预测模型，实现更快的预测速度。
执行如下命令，加载精度最高的模型权重，导出预测模型。

```
python tools/export.py \
    --config ./configs/uhrnet/fcn_uhrnetw48_cityscapes_1024x512_80k.yml \
    --model_path output/best_model/model.pdparams \
    --save_dir output/infer_model
```

得到如下文件:

```
output/infer_model
  ├── deploy.yaml            # 部署相关的配置文件
  ├── model.pdiparams        # 静态图模型参数
  ├── model.pdiparams.info   # 参数额外信息，一般无需关注
  └── model.pdmodel          # 静态图模型文件，可以使用netron软件进行可视化查看
```




<a name="3.2"></a>

### 3.2 基于 Python 预测引擎推理

<a name="3.2.1"></a>  

#### 3.2.1 预测单张图像

使用Python端部署方式，运行如下命令，会在output文件下面生成一张H0002.png的分割图像。
```
python deploy/python/infer.py \
  --config output/infer_model/deploy.yaml \
  --image_path data/optic_disc_seg/JPEGImages/H0002.jpg \
  --save_dir output/result
```

<a name="3.3"></a>

### 3.3 基于 C++ 预测引擎推理

PaddleClas 提供了基于 C++ 预测引擎推理的示例，您可以参考[服务器端 C++ 预测](../../deployment/image_classification/cpp/linux.md)来完成相应的推理部署。如果您使用的是 Windows 平台，可以参考[基于 Visual Studio 2019 Community CMake 编译指南](../../deployment/image_classification/cpp/windows.md)完成相应的预测库编译和模型预测工作。

<a name="3.4"></a>

### 3.4 服务化部署

Paddle Serving 提供高性能、灵活易用的工业级在线推理服务。Paddle Serving 支持 RESTful、gRPC、bRPC 等多种协议，提供多种异构硬件和多种操作系统环境下推理解决方案。更多关于Paddle Serving 的介绍，可以参考[Paddle Serving 代码仓库](https://github.com/PaddlePaddle/Serving)。

PaddleClas 提供了基于 Paddle Serving 来完成模型服务化部署的示例，您可以参考[模型服务化部署](../../deployment/image_classification/paddle_serving.md)来完成相应的部署工作。

<a name="3.5"></a>

### 3.5 端侧部署

Paddle Lite 是一个高性能、轻量级、灵活性强且易于扩展的深度学习推理框架，定位于支持包括移动端、嵌入式以及服务器端在内的多硬件平台。更多关于 Paddle Lite 的介绍，可以参考[Paddle Lite 代码仓库](https://github.com/PaddlePaddle/Paddle-Lite)。

PaddleClas 提供了基于 Paddle Lite 来完成模型端侧部署的示例，您可以参考[端侧部署](../../deployment/image_classification/paddle_lite.md)来完成相应的部署工作。

<a name="3.6"></a>

### 3.6 Paddle2ONNX 模型转换与预测

Paddle2ONNX 支持将 PaddlePaddle 模型格式转化到 ONNX 模型格式。通过 ONNX 可以完成将 Paddle 模型到多种推理引擎的部署，包括TensorRT/OpenVINO/MNN/TNN/NCNN，以及其它对 ONNX 开源格式进行支持的推理引擎或硬件。更多关于 Paddle2ONNX 的介绍，可以参考[Paddle2ONNX 代码仓库](https://github.com/PaddlePaddle/Paddle2ONNX)。

PaddleClas 提供了基于 Paddle2ONNX 来完成 inference 模型转换 ONNX 模型并作推理预测的示例，您可以参考[Paddle2ONNX 模型转换与预测](../../deployment/image_classification/paddle2onnx.md)来完成相应的部署工作。



