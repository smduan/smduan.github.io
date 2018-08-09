---
title: Selective Search算法
date: 2018-08-09
categories: 
- 目标检测(Object Detection)

tags:
- Selective Search
- Object Detection

---
## 介绍
[Selective Search for Object Recognition](http://www.huppelen.nl/publications/selectiveSearchDraft.pdf)立足点是为目标识别(Object Recognition)产生可能的目标区域。
<!-- more -->
目标识别是指在图像中找到确定一个物体，并找出其具体位置。之前的做法主要是基于穷举搜索（Exhaustive Search），选择一个窗口扫描整张图像，改变窗口的大小，继续扫描整张图像。显然这种方法会比较耗时。

作者利用图像分割的方法得到一些原始区域，然后使用一些合并策略将这些区域合并，得到可能包含目标的区域。  
![](/images/selective-search/object.png)  
上图给出了四个例子，来说明目标识别的复杂性以及难度。（a）中的场景是一张桌子，桌子上面放了碗，瓶子，还有其他餐具等等。比如要识别“桌子”，我们可能只是指桌子本身，也可能包含其上面的其他物体。这里显示出了图像中不同物体之间是有一定的层次关系的。（b）中给出了两只猫，可以通过纹理（texture）来找到这两只猫，却又需要通过颜色（color）来区分它们。（c）中变色龙和周边颜色接近，可以通过纹理来区分。（d）中的车辆，我们很容易把车身和车轮看做一个整体，但它们两者之间在纹理和颜色方面差别都非常地大。

因此，不能通过单一的策略来区分不同的物体，需要充分考虑图像物体的多样性（diversity）。另外，在图像中物体的布局有一定的层次（hierarchical）关系，考虑这种关系才能够更好地对物体的类别（category）进行区分。
## Selective Search
首先介绍区域合并的算法。使用[Efficient Graph-Based Image Segmentation](http://people.cs.uchicago.edu/~pff/papers/seg-ijcv.pdf)产生原始区域。然后使用下面算法对这些原始区域进行合并。  
![](/images/selective-search/grouping-algorithm.png)  
输入：彩色图片  
输出：目标位置的可能结果集合L

1、使用Efficient Graph-Based Image Segmentation中的图像分割算法生成初始区域$R={r_{1},...,r_{n}}$  
2、初始化相似度集合$S=\varnothing$  
3、计算两两相邻区域之间的相似度，将其添加到相似度集合S中
4、从相似度集合S中找出相似度最大的两个区域$r_{i}$和$r_{j}$，将其合并成为一个区域$r_{t}$，从相似度集合中除去原先与$r_{i}$和$r_{j}$相邻区域之间计算的相似度，计算$r_{t}$与其相邻区域（原先与$r_{i}$或$r_{j}$相邻的区域）的相似度，将其结果添加的到相似度集合S中。同时将新区域$r_{t}$添加到区域集合R中。  
5、获取每个区域的Location，这个结果就是物体位置的可能结果L

## 策略多样化(Diversification Strategies)
### 颜色空间多样化
作者采用了8中不同的颜色方式，主要是为了考虑场景以及光照条件等。这个策略主要应用于Efficient Graph-Based Image Segmentation图像分割算法中原始区域的生成。主要使用的颜色空间有：（1）RGB，（2）灰度I，（3）Lab，（4）rgI（归一化的rg通道加上灰度），（5）HSV，（6）rgb（归一化的RGB），（7）C（具体请看[Color Invariance](http://www.eecs.harvard.edu/~zickler/wiki/ColorSubspaces/ColorInvariance_Geusebroek_PAMI_2001.pdf)），（8）H（HSV的H通道）
### 相似度计算多样性
区域合并时是根据两个区域的相似度进行合并，论文综合了四种相似度计算方法，分别是：颜色、纹理、大小(size)、吻合(fit)。
#### 颜色相似度
获取图像每个颜色通道的25 bins的直方图，这样每个区域$r_{i}$可以得到一个75维的颜色直方图$C_{i}={c^{1}_{i},...,c^{n}_{i}}$，颜色直方图使用L1-norm归一化。区域之间颜色相似度通过下面的公式计算:  
  <center> $$s_{colour}(r_{i},r_{j}) = \sum_{i=1}^{n}min(c^{k}_{i},c^{k}_{j})$$ </center >  
在区域合并过程中需要对合并后的区域进行计算其直方图，计算方法:  
  $$C_{t} = \frac{size(r_{i})\times C_{i}+size(r_{j})\times C_{j}}{size(r_{i})+size(r_{j})}$$  

#### 纹理相似度
这里的纹理采用SIFT-Like特征。具体做法是对每个颜色通道的8个不同方向计算方差σ=1的高斯微分（Gaussian Derivative），每个通道每个颜色获取10 bins的直方图（同样采用L1-norm归一化），这样就可以获取到一个240维的向量$T_{i}={t^{1}_{i},...,t^{n}_{i}}$，区域之间纹理相似度计算方式和颜色相似度计算方式类似。  
<center>$$s_{texture}(r_{i},r_{j}) = \sum_{i=1}^{n}min(t^{k}_{i},t^{k}_{j})$$</center> 

合并之后新区域的纹理特征计算方式和合并后颜色特征计算相同。
#### 大小相似度
这里的大小是指区域中包含像素点的个数。使用大小的相似度计算，主要是为了尽量让小的区域优先合并：  
<center>$$s_{size}(r_{i},r_{j}) =1-\frac{size(r_{i})+size(r_{j})}{size(im)}$$</center>   
其中size(im)表示整张图的像素大小
#### 吻合相似度
这里主要是为了衡量两个区域是否更加“吻合”，其指标是合并后的区域的外接矩形越小，其吻合度越高。其计算方式：  
<center>$$s_{fill}(r_{i},r_{j})=1-\frac{size(BB_{ij})-size(r_{i}-size(r_{j}))}{size(im)}$$</center>  
  
$BB_{ij}$表示$r_{i}和r_{j}$的外接矩形区域。size(im)表示整张图片的像素大小  
#### 整体相似度
最终的相似度评估方式是将上述四种相似度计算方式综合。  
<center>$$s(r_{i},r_{j}) =a_{1}s_{colour}(r_{i},r_{j})+a_{2}s_{texture}(r_{i},r_{j})+a_{3}s_{size}(r_{i},r_{j})+a_{4}s_{fill}(r_{i},r_{j})$$</center> 

## Selective Search用于目标识别
通过前面的区域合并，可以得到一些目标的位置假设集合L。接下来的任务就是如何从中找出目标的真正位置并确定目标的类别。使用的方式是传统的"特征+SVM"方法。  
1、先使用HOG或者词袋模型(BoW,Bag-of-Words)  
2、训练SVM用于识别目标。  
训练过程中，首先选择ground-truth窗口作为正样本，选择与正样本窗口重叠20%~50%的窗口作为负样本。  
迭代训练：一次训练结束后，选择分类时的false positive放入了负样本中，再次训练。

## 总结
Selective Search方式只是得到目标候选区域，并没有对这些目标候选区域的位置进行调整。后续的R-CNN和Fast R-CNN使用Selective Search方式生成目标候选区域，然后使用深度学习的方式对这些候选区域进行识别和位置回归得到更加精确的结果。
## 参考文献
[论文笔记 《Selective Search for Object Recognition》](https://blog.csdn.net/csyhhb/article/details/50425114)   

[Selective Search for Object Recognition](https://blog.csdn.net/surgewong/article/details/39316931)