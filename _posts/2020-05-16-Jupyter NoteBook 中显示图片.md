---
layout:     post
title:      Jupyter NoteBook 中使用显示图片
subtitle:   
date:       2020-05-16
author:     AspenStars
header-img: img/post-bg-keybord.jpg
catalog: true
tags:
    - Jupyter NoteBook
    - Python
---
# Jupyter NoteBook 中使用显示图片

Jupyter NoteBook 中无法开启cv2的窗口，因此只能使用内建的形式

有两种办法:

我使用第一种方法仍然不能正确运行，**建议使用第二种**
- 用 cv2.imshow时加入cv2.destroyAllWindows()
- 用 plt.imshow() 代替 cv2.imshow

## cv2.imshow

加入 cv2.destroyAllWindows() 后可以解决 crash 或者图片显示不出来的问题
```
import cv2
%matplotlib inline

image = cv2.imread("test.png")
cv2.imshow("test", image)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

## plt.imshow()
**运行测试通过**
load 图片的时候仍然采用 opencv 但是显示图片的时候用 plt.imshow。
只是需要注意的是要对 opencv load 上来的图片进行处理再显示。
```
import cv2
from  matplotlib import pyplot as plt
%matplotlib inline

im = cv2.imread(IMG_PATH,1)
im2 = im[:,:,::-1] 
plt.imshow(im2)
plt.show()
```

### 参考资料
1. [Jupyter NoteBook 中使用 cv2.imshow 显示图片](https://blog.csdn.net/kuweicai/article/details/103359299)