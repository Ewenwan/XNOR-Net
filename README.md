# Binary-Weight-Networks 只是对CNN网络的滤波器进行二值化近似，使其占内存降低32倍多，

# XNOR-Networks 则对CNN网络的滤波器及其输入都进行二值化近似，

## XNOR-Net: ImageNet Classification Using Binary Convolutional Neural Networks.

This is the Torch 7.0 implementation of XNOR-Net: ImageNet Classification Using Binary Convolutional Neural Networks.

### Citation 
```bash
@inproceedings{rastegariECCV16,
    Author = {Mohammad Rastegari and Vicente Ordonez and Joseph Redmon and Ali Farhadi},
    Title = {XNOR-Net: ImageNet Classification Using Binary Convolutional Neural Networks},
    Booktitle = {ECCV},
    Year = {2016}
}
```

# 对于每一次CNN网络，我们使用一个三元素 《I,W,#》来表示，I 表示卷积输入，W表示滤波器，#表示卷积算子

## BWN

该网络主要是对W 进行二值化，主要是一些数学公式的推导，公式推导如下:
      
      对W进行二值化，使用 B 和缩放比例 a 来近似表达W
![](https://img-blog.csdn.net/20160715113533581)
      
      全精度权重W 和 加权二进制权重 aB 的误差函数，求解缩放比例a和二值权重B，使得误差函数值最小
![](https://img-blog.csdn.net/20160715113542440)

      误差函数展开
![](https://img-blog.csdn.net/20160715113549674)
      
      二值权重B的求解，误差最小，得到 W转置*B最大
![](https://img-blog.csdn.net/20160715113600645)

      缩放比例a的求解，由全精度权重W求解得到
![](https://img-blog.csdn.net/20160715113609159)

## BWN网络的训练


![](https://img-blog.csdn.net/20160715113831914)


## 异或网络 XNOR-Networks  对 I(神经元激活输出，下一层的输入) 及 W(权重参数) 都二值化

     最开始的输入X，权重W, 使用b*H代替X, 使用a*B代替W , a,b为缩放比例，H,B为 二值矩阵。
![](https://img-blog.csdn.net/20160715114052958)
      
     网络中间隐含层的量化，二值化的矩阵相乘，在乘上一个矩阵和一个缩放因子。
![](https://img-blog.csdn.net/20160715114402250)

      主框架:
![](https://img-blog.csdn.net/20160715114256287)



### Requirements
This software is implemented on top of the implementation of [ImageNet-multiGPU](https://github.com/soumith/imagenet-multiGPU.torch) and has all the same requirements.

Download the cache files from [here](https://s3-us-west-2.amazonaws.com/ai2-vision/xnornet/cache.tar) and run:
```bash
tar -xvf ./cache.tar
```

### Training Binary Weight Network

```bash
th main.lua -data [path to ImageNet dataset] -nGPU 1 -batchSize 128 -netType alexnet -binaryWeight -dropout 0.1
``` 
### Training XNOR-Networks
```bash
th main.lua -data [path to ImageNet dataset] -nGPU 4 -batchSize 800 -netType alexnetxnor -binaryWeight -optimType adam -epochSize 1500
```
### Trained Models
To use the trained models use the option `-retrain [path to the trained model file]` and `-testOnly`

[Binary-Weight-Network(BWN)](https://s3-us-west-2.amazonaws.com/ai2-vision/xnornet/alexnet_BWN.t7)

[XNOR-Network](https://s3-us-west-2.amazonaws.com/ai2-vision/xnornet/alexnet_XNOR.t7)

If you use the same image pre-processing as [here](https://github.com/soumith/imagenet-multiGPU.torch) by
```bash
find . -name "*.JPEG" | xargs -I {} convert {} -resize "256^>" {}
``` 
then the accuracies for top-1 should be: 

alexnet_BWN  %56.8

alexnet_XNOR %43.3 

### License
By downloading this software you acknowledged that you agreed on the terms and conditions in the `SOFTWARE-LICENSE-AGREEMENT.lic`
