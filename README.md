# 无约束场景下的ALPR 

## 介绍

该存储库包含作者对 ECCV 2018 论文“无约束场景中的车牌检测和识别”的实现。

* 论文页面: http://sergiomsilva.com/pubs/alpr-unconstrained/

如果您在任何出版物中使用我们的代码生成的结果，请引用我们的论文：

```
@INPROCEEDINGS{silva2018a,
  author={S. M. Silva and C. R. Jung}, 
  booktitle={2018 European Conference on Computer Vision (ECCV)}, 
  title={License Plate Detection and Recognition in Unconstrained Scenarios}, 
  year={2018}, 
  pages={580-596}, 
  doi={10.1007/978-3-030-01258-8_36}, 
  month={Sep},}
```

## 要求

为了轻松运行代码，您必须安装带有 TensorFlow 后端的 Keras 框架。 Darknet 框架独立于“darknet”文件夹中，必须在运行测试之前进行编译。 要构建 Darknet，只需在“darknet”文件夹中输入“make”：

```shellscript
$ cd darknet && make
```

**当前版本在 Ubuntu 16.04 机器上进行了测试，使用 Keras 2.2.4、TensorFlow 1.5.0、OpenCV 2.4.9、NumPy 1.14 和 Python 2.7**

## 下载模型

构建完 Darknet 框架后，您必须执行“get-networks.sh”脚本。 这将下载所有经过训练的模型：

```shellscript
$ bash get-networks.sh
```

## 执行简单的测试

使用脚本“run.sh”来运行我们的 ALPR 方法。 它需要 3 个参数:
* __Input directory (-i):__ 应包含至少 1 张 JPG 或 PNG 格式的图像;
* __Output directory (-o):__ 识别过程中，该目录下会产生很多临时文件，最终会被删除。 其余文件将与自动注释图像相关;
* __CSV file (-c):__ 指定输出 CSV 文件.

## 执行
```shellscript
$ bash get-networks.sh && bash run.sh -i samples/test -o /tmp/output -c /tmp/output/results.csv
```

## 训练 LP 检测器

要从头开始训练 LP 检测器网络，或针对新样本对其进行微调，您可以使用 train- detector.py 脚本。 
在样本/训练检测器文件夹中，有 3 个带注释的样本，仅用于演示目的。
为了正确地重现我们的实验，该文件夹必须填充训练集中提供的所有注释，以及从原始数据集传输的各自图像。
考虑到 train- detector 文件夹内的数据，可以使用以下命令从头开始训练网络：

```shellscript
$ mkdir models
$ python create-model.py eccv models/eccv-model-scracth
$ python train-detector.py --model models/eccv-model-scracth --name my-trained-model --train-dir samples/train-detector --output-dir models/my-trained-model/ -op Adam -lr .001 -its 300000 -bs 64
```

要进行微调，请使用带有 --model 选项的模型。

## 关于GPU和CPU的一句话

我们知道并不是每个人都有可用的 NVIDIA 卡，有时正确配置 CUDA 很麻烦。 
因此，我们选择将 Darknet makefile 设置为默认使用 CPU 而不是 GPU，以利于大多数人轻松执行而不是快速性能。
因此，车辆检测和 OCR 会相当慢。 如果您想加速它们，请编辑 Darknet makefile 变量以使用GPU。
