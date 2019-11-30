# Denoising
做的关于去噪项目的一个总结 (最近接触，疏漏之处还望指出)

近年来关于去噪方面，不少人已经做过不错的归纳，
下面是几个归纳的State-of-the-art：
[Yang:Denoising1](https://github.com/flyywh/Image-Denoising-State-of-the-art)
[Prof.Wen:Denoising2](https://github.com/wenbihan/reproducible-image-denoising-state-of-the-art)

---

这几个月做了各种去噪方法的尝试，体会如下：

+ BM3D：
   这个在filter方法中可谓是相当强悍的存在，综合了block matching， 稀疏表示，域变换等一系列操作。
大体上是先用协同过滤的手段，对同一个group中的patch进行变换，shrinkage操作后在反变换回去，完成滤波去噪。
具体的实现上，先用基本的带噪图片生成很多的group，粗去噪后形成一个参考集，然后利用参考集为下次的去噪提供一个先验。
这个方法是很多后续方法的基础

+ DDID：
    这个方法来自ICIP，思路是结合双边滤波和频域滤波 效果还行(但感觉上相当于局部卷积滤波，因为对处理后结果进行锐化增强时
    又变了回去，说明DDID相当于个卷积滤波)

+ 稀疏编码部分：
   + KSVD： 这个方法比较古老了，实际效果也不是太好，速度也不管够
   + TWSC： 带权重的稀疏编码？ ECCV2018
    这种方法、或者相比于dl方法，最大的优势在于不需要训练，不需要groundtruth
    把图像分为rgb三个通道，搜索相近patch构成目标向量Y，然后用稀疏编码的通式求解
    创新点在于加入了三个权重矩阵，罚row，column和编码矩阵，再利用admm求
    缺点是跑的过慢，单帧720p，100s以上
   ...

总体而言,这玩意儿单用不太好，主要为以后的方法提供一个思路

+ 优化角度：
   最近看了几篇PnP 和 RED 之类的文章。它们好处在于可以用一个denoiser作为先验，反复迭代取得更好的效果，可与深度模型结合，效果还是很不错的。
   比较有意思，继续研究

+ 低秩 (Low Rank):
   拿以前弄过Robust-PCA的经验来看，基本上是和核范数之类的优化相关，这玩意儿数学上比较好看，
对于去掉异常噪声还行，对于比较混杂的噪声基本上效果不太好，而且速度比较慢就是

+ 深度方法：
   + DnCNN： 这个比较经典，结构简单，设计也不复杂，主要是训练数据的问题，算是一个baseline

   + Universal Denoising Networks： 这玩意其实不能算是经典的CNN(至少我觉得)，相当于每层是一个类似于BM3D这样
的块，然后贪心法训练整体，有点boosting的味道，训练比较慢

   + Recurrent Network： 还没细看过，RNN？似乎不是很有效，之前看过在Video上的，目前做的人比较少

   + 带GAN的训练网络：这种的基本上都有几个subNet，GAN似乎起到的主要是数据增强的作用，即模糊学出噪声分布，扩大数据集
以后可以尝试这方面，~~感觉比较靠谱~~。 用gan增强数据集比较靠谱，生成图片感觉不好。

   + RDN， GRDN， GRDN+CBAM
   RDN残差稠密网络，GRDN grouped-RDN，加上CBAM后PSNR高个0.0xdB (这个见仁见智，好处是添加很容易)
   跑数据集来看这个目前是比较给力的，内存要求大一些


