---
title: Mac上使用cwebp压缩图片
date: 2025-11-12 16:07:26
tags:
---

> cwebp 是 WebP 图像格式的命令行工具，它允许你将常见的图像格式（如 PNG、JPEG）转换为 WebP 格式，这样可以减少图像的文件大小，同时保持较高的图像质量。

#### 下载cwebp
使用brew命令下载webp
~~~
brew install webp
~~~
安装完成以后，使用以下命令验证是否安装成功，安装成功以后会输出版本号
~~~
cwebp -version
~~~

#### cwebp 使用说明
cwebp 用于将图像转换为 WebP 格式，支持多种输入格式，如 PNG、JPEG、TIFF、GIF 等。转换后的 WebP 图像通常具有较小的文件大小，同时保持较高的图像质量。

###### 基本语法
~~~
cwebp [input options] -o [output file] [input file]
~~~

###### 常见参数说明
1. -q (质量)，用于设置输出图像的质量。质量值从 0 到 100，数字越大，图像质量越高，文件大小也越大，默认值是 75。
~~~
cwebp -q 80 input.jpg -o output.webp
~~~

2. -lossless (无损压缩)，启用无损压缩（即保持原图质量），这对于 PNG 到 WebP 的转换特别有用，尽管输出文件可能较大。
~~~
cwebp -lossless input.png -o output.webp
~~~

3. -resize (调整图像尺寸)，用于调整输出 WebP 图像的尺寸。格式：-resize width height，只需设置一个参数即可自动按比例调整。
~~~
cwebp -resize 800 600 input.jpg -o output.webp
~~~

4. -alpha_q (Alpha 通道质量)，控制透明度（Alpha 通道）的压缩质量。0 表示最小压缩（最好的透明度质量），100 表示最大压缩（最差的透明度质量）。
~~~
cwebp -alpha_q 90 input.png -o output.webp
~~~

5. -lossless，启用无损压缩。这将保留原始图像的质量，不会丢失任何细节。
~~~
cwebp -lossless input.png -o output.webp
~~~

6. -m (压缩方法)，设置压缩方法。m 参数用于控制压缩的速度和压缩比，取值范围是 0 到 6。0 表示最快，6 表示压缩最优，但速度最慢。
~~~
cwebp -m 6 input.jpg -o output.webp
~~~

7. -alpha (透明度处理)，控制 WebP 图像中透明区域的处理方式。适用于透明图像（通常为 PNG）。
~~~
cwebp -alpha input.png -o output.webp
~~~

8. -crop (裁剪图像)，裁剪图像的一部分，格式：-crop x y width height。
~~~
cwebp -crop 0 0 300 200 input.jpg -o output.webp
~~~

9. -v (详细模式)，启用详细模式，输出更详细的信息，适合调试使用。
~~~
cwebp -v input.jpg -o output.webp
~~~

10. -preset (预设值)，提供不同的质量/压缩设置，适合不同的用途，如 photo、picture、drawing、icon 和 text。
~~~
cwebp -preset photo input.jpg -o output.webp
~~~