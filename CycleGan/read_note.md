##### web:https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix
### 思路框图
![figure3](https://github.com/lix3972/papers/blob/master/CycleGan/picture/Figure3.png)
Dx鉴别x,F(y)，Dy鉴别y,G(x).  x->G(x)->F(G(x))～=x(约等于)。y->F(y)->G(F(y))～=y
### 公式  
##### 对抗损失函数：（调整为式5）  
![formula1](https://github.com/lix3972/papers/blob/master/CycleGan/picture/Lgan.png)  
##### 循环一致性损失函数  
![formula2](https://github.com/lix3972/papers/blob/master/CycleGan/picture/Lcyc2.png)  
##### 完整目标函数  
![formula3](https://github.com/lix3972/papers/blob/master/CycleGan/picture/L3.png)  
##### 优化目标  
![formula4](https://github.com/lix3972/papers/blob/master/CycleGan/picture/G_F4.png)  
##### 训练细节  
![formula5](https://github.com/lix3972/papers/blob/master/CycleGan/picture/Llsgan5.png)  
  采用两项技术使网络稳定：1、去掉对数，loss变为式5(上图) 2、采用[42]的策略，用之前生成的图像更新Dx,Dy，而不是用最新的生成网络生成的图像。
我们缓冲了前面的50张图片。

