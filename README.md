# TNS-window-fenetrage
TNS-window-fenetrage 介绍了窗函数对于数字信号处理的泄露改善


在做信号处理时，经常涉及到“泄漏”。那泄漏是什么，是什么原因造成了泄漏呢？在这将告诉您答案。

 

1.信号截断

一次FFT分析截取1帧长度的时域信号，这1帧的长度总是有限的，因为FFT分析一次只能分析有限长度的时域信号。而实际采集的时域信号总时间很长，因此，需要将采样时间很长的时域信号截断成一帧一帧长度的数据块。这个截取过程叫做信号截断。

 

假设有一段10s的时域信号，取1帧的长度T=1s，无重叠，则该信号将被截断为10帧，如下图所示。按此规律进行FFT计算，将得到10个瞬时频谱，如果将这些瞬时频谱进行平均，那么平均次数为10次，最终的FFT分析结果为这10个瞬时频谱的平均结果。

图片
  

信号截断分为周期截断和非周期截断。周期截断是指截断后的信号为周期信号，而非周期截断是指截断后的信号不再是周期信号，哪怕原始信号本身是周期信号。

 

2. 周期截断

我们知道周期信号最明显的特征是信号的起始和结束时刻的幅值相等，哪怕是一个周期。在这假设采样时间很长的信号为单频正弦波（周期信号），若1帧的时间长度等于这个正弦波周期的整数倍，那么，截断后的信号仍为周期信号。取1帧的时间长度T等于原始信号的1个周期长度，那么截断后的信号仍为周期信号，如下图所示。

图片
 

将这个截断后的信号再重构，可以得到原始的正弦波，如下图所示。

图片
 

对截断的这一帧信号做FFT分析，得到它的频谱如下图所示。从图中可以看出，得到的频率成分为原始信号的真实频率，并且幅值与原始信号的幅值相等（100%幅值）。

图片
 

假设原始信号的频率为f Hz，则周期为1/f s。因为截取的时间长度T为信号周期的整数倍（假设为k倍），即

T=k/f

而频率分辨率为1/T，即

∆f=1/T= f/k

因而，信号的频率成分

f =k*∆f

即信号的频率成分为频率分辨率∆f的整数倍，也就是说频谱图中有一条谱线与信号的频率成分相同，这也就是所谓的信号“压谱线”。因而，对这个周期信号进行FFT分析时，信号的频谱样子与实际情况完全相同，与我们预期的样子相同。

 

3. 非周期截断

倘若信号截断的长度不为原始正弦信号周期的整数倍，那么，截断后的信号则不为周期信号，哪怕原始信号是周期信号。并且现实世界中，我们进行FFT分析时，绝大多数情况都是非周期截断。

 

对之前的正弦信号进行非周期截断，如下图所示。截断后的信号起始时刻和结束时刻的幅值明显不等，将这个信号再进行重构，在连接处信号的幅值不连续，出现跳跃，如图中黑色圆圈区域所示。

图片
 

对截断后的信号做FFT分析，得到的频谱如下图所示。这时的FFT频谱已远远不是我们预期的那种单条离散谱线了（周期截取的频谱样子）。对比周期截断的频谱，可以看出，此时频谱在整个频带上发生“拖尾”现象。峰值处的频率与原始信号的频率相近，但并不相等。另一方面，峰值处的幅值已不再等于原始信号的幅值，为原始信号幅值的64%（矩形窗的影响）。而幅值的其他部分（36%幅值）则分布在整个频带的其他谱线上。

图片
 

由于非周期截断的时间长度不等于信号周期的整数倍，因此，信号的频率成分f≠k*∆f，也就是说，在频谱图中，没有一条谱线与信号的频率成分完全相同。

 

4. FFT变换

在作进一步说明之前，让我们回顾一下FFT变换。FFT变换要求为：信号要么从-∞到+∞ ，要么为周期信号。现实世界中，不可能采集时间从-∞到+∞的信号，只能是有限时间长度的信号。

 

回想以前学过有关傅立叶级数的一些基本知识。对于一条单频正弦波，我们知道用傅立叶级数描述该信号是非常容易的。通常用傅立叶级数中的一项就可以描述了，形如Asinωt。但是对于一些信号，比如矩形脉冲信号，其傅立叶级数展开会是什么样的呢？我想你应该记得傅立叶级数展开项是一系列不同频率和不同幅值的正弦信号之和。对于矩阵脉冲，傅立叶级数要包含很多项，才能近似这个信号，这是因为矩形脉冲信号不连续，不像平滑的正弦波。

 

5. 泄漏

由于信号的非周期截断，导致频谱在整个频带内发生了拖尾现象。这是非常严重的误差，称为泄漏，是数字信号处理所遭遇的最严重误差。但是为什么会出现这种误差呢？原始实际信号为一条单频正弦波，它的频谱怎么会变得如此失真？这个问题很容易解释。这是因为截断后的信号不再是周期信号。

 

对比一下正确的频谱与发生泄漏的频谱，如下图所示，可以看出，泄漏后的频谱的幅值更小，频谱拖尾更严重。当截断后的信号不为周期信号时，就会发生泄漏。而现实世界中，在做FFT分析时，很难保证截断的信号为周期信号，因此，泄漏不可避免。

图片



现在返回到非周期截断的正弦波，可以看出在截断时间长度内没有捕捉到整数倍个周期正弦波，导致波形发生了失真，似乎在信号周期的末端波形出现了不连续。这就解释了为什么FFT会在整个频带上发生拖尾现象了。本质上，这需要多个傅立叶展开项（多条谱线）去近似这个明显不连续的信号，因此，频谱出现了拖尾现象。

 

6. 窗函数

为了将这个泄漏误差减小到最小程度（注意我说是的减少，而不是消除），我们需要使用加权函数，也叫窗。加窗主要是为了使信号似乎更好地满足FFT处理的周期性要求，减少泄漏。(关于窗函数，完全可以写一篇长长的文章了，在这只简单介绍)

 

如下图所示，若周期截断，则FFT频谱为单一谱线。若为非周期截断，则频谱出现拖尾，如图中部所示，可以看出泄漏很严重。为了减少泄漏，给信号施加一个窗函数（如图中红色曲线所示），原始截断后的信号与这个窗函数相乘之后得到的信号为右侧上面的信号。可以看出，此时，信号的起始时刻和结束时刻幅值都为0，也就是说在这个时间长度内，信号为周期信号，但是只有一个周期。对这个信号做FFT分析，得到的频谱如右侧下边所示。相比较之前未加窗的频谱，可以看出，泄漏已明显改善，但并没有完全消除泄漏。因此，窗函数只能减少泄漏，不能消除泄漏。

图片
 

希望通过以上的解释说明，使您明白了什么是泄漏。



泄露介绍：
来源：https://mp.weixin.qq.com/s?__biz=MzI5NTM0MTQwNA==&mid=2247484164&idx=1&sn=fdaf2164306a9ca4166c2aa8713cacc5&scene=21&poc_token=HDcIjGWjatqf6dLOUbcydcTzt2zanlS9M9ZYHOWL
