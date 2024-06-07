
搭建模型，**训练，测试**


# LeNet-5 

## 诞生背景

![[Pasted image 20240407175053.png]]

![[Pasted image 20240407175207.png|270]]

**处理数据 + 训练** 占比很大

## 网络结构

![[Pasted image 20240407175554.png]]


## 网络参数

![[Pasted image 20240407181210.png]]
![[Pasted image 20240407181408.png]]
![[Pasted image 20240407181520.png]]

卷积：通道变大
池化：特征图变大

## 总结

![[Pasted image 20240407181920.png]]


# AlexNet 
## 诞生背景

![[Pasted image 20240407182039.png]]

## 网络结构


![[Pasted image 20240407182929.png]]

![[Pasted image 20240407182950.png]]
### 参数详解

![[Pasted image 20240407183411.png]]

![[Pasted image 20240407183638.png]]

### 防止过拟合
	Dropout

![[Pasted image 20240407184101.png]]
  
### 创新点

1. 数据扩充
2. 模型改进

## 图像增强
	扩充数据——训练数据多，防止过拟合

### 水平翻转
	扩充一倍

### 随机裁剪
	产生更多数据集，

### PCA

![[Pasted image 20240407185201.png]]

## LRN 正则化

### 局部归一化

![[Pasted image 20240409171113.png]]
![[Pasted image 20240409171150.png]]
![[Pasted image 20240409181528.png]]




# 实战

Model 重要但是代码少 80%
train 数据处理，模型训练，结果保存 10%
test 测试验证 10%
使用不同比例的数据

model.py
train.py
test.py

模型代码不同，使用相同的训练和测试代码即可

## LeNet搭建过程

## AlexNet搭建过程




# VGG 

# GoodleNet

