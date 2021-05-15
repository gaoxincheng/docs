##  问题描述
由于使用`OpenGL`时，有默认黑色背景问题，不能实现窗口透明效果，因此需要先抓取显存的图像，然后再在另一个窗口展示。之前使用`glReadPixels`抓取时，有效率问题，有些机型会特别慢。传统的`glReadPixels()`函数会阻塞渲染管线，它会一直等待直到所有的像素数据传输完成。然后才会将控制权交给应用程序。相反，使用`PBO`时`glReadPixel()`使用异步地`DMA`方式传输并且能立即返回。因此，应用程序`（CPU）`可以立即执行其他程序，这时数据会被`OpenGL（GPU）`使用`DMA`的方式传输。


##  概念
 `OpenGL PBO（Pixel Buffer Object）`，被称为像素缓冲区对象，主要被用于异步像素传输操作。 `PBO`仅用于执行像素传输，不连接到纹理，且与 `FBO` （帧缓冲区对象）无关。
 `PBO`最大的优点是使用`DMA（Direct Memory Access，直接内存访问）`的方式快速地将数据传入到显卡或从显卡传出，它不需要等待CPU的周期。`PBO`的另外一个优点是可以使用异步地DMA方式传输数据。***即我们可以使用PBO快速的读取显存图像或快速修改显存展示的图像。***

## 参考链接
- [PBO文档及事例](http://www.songho.ca/opengl/gl_pbo.html)

- [OpenGL像素缓冲区对象(PBO)](https://blog.csdn.net/wozhengtao/article/details/52671940)