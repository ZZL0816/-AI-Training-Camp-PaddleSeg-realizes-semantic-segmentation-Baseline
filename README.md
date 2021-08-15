# 语义分割

图像分类任务是把一张图片划分为某个类别，而语义分割则是更进一步的分类 — 在像素级别上的分类，属于同一类的像素都要被归为一类。

# 一、项目背景

![](https://ai-studio-static-online.cdn.bcebos.com/68c07195495d45c59121248f057561dc94abc2a8d45a4c1e8051f5bffa726cc2)


# 二、数据集简介

### 本项目是对含有马的图片进行分割，数据集中包含两个文件：Annotations、Images，其中Annotations文件中存储的为mask；Images中存储的为训练图像，一共328幅。
![](https://ai-studio-static-online.cdn.bcebos.com/fb6642dd7c6c4c33849a99d57c9646358d53bf61c6874583b5875b19528c3d9c)



## 1.数据加载和预处理
```
!unzip -oq work/data/horse.zip -d segDataset
```

### * 首先，通过上边的像素值分析以及horse本身的标签表现，我们确定horse数据集为二分类任务

### * 然而，实际label中，却包含多个像素值，因此需要将horse中的所有label进行一个预处理
```
# 查看label中像素分类情况
!python show_segDataset_label_cls_id.py
```

### * 预处理内容为: 0值不变，非0值变为1，然后再保存label

### * 并且保存文件格式为png，单通道图片为Label图片，最好保存为png——否则可能出现异常像素

### 对应horse的预处理脚本，位于:

### parse_horse_label.py

```
!python parse_horse_label.py
```

### * 预处理完成后，配置训练的索引文件txt，方便后边套件读取数据
#### txt创建脚本位于: horse_create_train_list.py
#### 同时，生成的txt位于: segDataset/horse/train_list.txt

```
!python horse_create_train_list.py
```

# 三、使用套件训练
- 1. 本项目使用PaddleSeg ,需要大家手动挂载到项目中，地址为：**data/data102250/PaddleSeg-release-2.1.zip**
```
# 解压套件
!unzip -oq data/data102250/PaddleSeg-release-2.1.zip
# 通过mv指令实现文件夹重命名
!mv PaddleSeg-release-2.1 PaddleSeg
```

- 2.选择模型，baseline选择**bisenet**, 位于: **PaddleSeg/configs/quick_start/bisenet_optic_disc_512x512_1k.yml**

- 3.配置模型文件

> 首先修改训练数据集加载的dataset类型:

![](https://ai-studio-static-online.cdn.bcebos.com/2f5363d71034490290f720ea8bb0d6873d7df2712d4b4e84ae41b0378aed8b89)

> 然后配置训练数据集如下:

![](https://ai-studio-static-online.cdn.bcebos.com/29547856db4b4bfc80aa3732e143f2788589f9316c694f369c9bd1da44b815dc)

> 类似的，配置验证数据集: -- **注意修改train_path为val_path**

![](https://ai-studio-static-online.cdn.bcebos.com/09713aaaed6b4611a525d25aae67e4f0538224f7ac0241eb941d97892bf6c4c1)


- 4.开始训练

使用PaddleSeg的train.py，传入模型文件即可开启训练
训练后进行预测

# 四、效果展示

通过PaddleSeg预测输出的结果为图片，对应位于:PaddleSeg/output/horse

其中包含两种结果：

- 一种为掩膜图像，即叠加预测结果与原始结果的图像 -- 位于: **PaddleSeg/output/horse/added_prediction**
- 另一种为预测结果的伪彩色图像，即预测的结果图像 -- 位于: **PaddleSeg/output/horse/pseudo_color_prediction**

<font color='red' size=5> ---数据集 horse 的预测结果展示--- </font>

<font color='black' size=5> 掩膜图像： </font>

![](https://ai-studio-static-online.cdn.bcebos.com/31ce006a3a3f45378701a8f3064612c87555d827de3748fd9a98a1bc1efe2b4a)

<font color='black' size=5> 伪彩色图像： </font>

![](https://ai-studio-static-online.cdn.bcebos.com/8d6710c888954d1b92725e3defc9ae71cb07df5d271d40e6bf8581d6f7025ad6)

# 五、总结与升华

修改配置模型文件一定要点保存，否则修改的并没有更新，还是原始原文运行。

