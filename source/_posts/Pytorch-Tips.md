---
title: Pytorch Tips
date: 2021-04-02 16:42:31
tags:
categories: "学习资源"
---
<center><h1>代码中的一些记录</h1></center>

1.如果用cv2.imread读取的是灰度图图像，imgB = cv2.imread('./train_data/label/' + img_name, 0)，请在末尾写上0，否则出来的图像是三个通道  
2.imgB = imgB.transpose(0, 1, 2)表示图像不变   imgB = imgB.transpose(2, 0, 1)表示原来的2轴转到0，原来的0轴转到1，原来的1轴转到2，主要是适应pytorch中的一些设置，因为pytorch中[4,1,64,64]表示一个批次4张图片，然后有一个通道，最后两个参数是h,w  
3.img_train = torch.unsqueeze(img_train, 0)对数据进行扩充维度，本身是64*64，可以变成[1,64,64]，0代表在哪里扩充  

