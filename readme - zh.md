# svpng v0.1.1

一个把RGB/RGBA图片保存为PNG格式的简单C函数

Copyright (C) 2017 Milo Yip. 版权所有.


# 特点
* RGB/RGBA颜色格式
* 函数使用简单
* 仅仅32行 ANSI C 代码
* 没有多余依赖
* 可自定义输出流 (默认为C文件描述符)

# 使用方法

默认情况下，`svpng()`函数有如下定义

~~~c
/*!
    \brief Save a RGB/RGBA image in PNG format.
    \param out Output stream (by default using file descriptor).
    \param w Width of the image. (<16383)
    \param h Height of the image.
    \param img Image pixel data in 24-bit RGB or 32-bit RGBA format.
    \param alpha Whether the image contains alpha channel.
*/
void svpng(FILE* out, unsigned w, unsigned h, const unsigned char* img, int alpha);
~~~


你需要使用`fopen`来打开一个二进制文件以供写入，然后使用图像数据调用这个函数，像素会被从上到下，从左到右依次填充。


对于24位的RGB格式 ( `alpha = 0` ),R、G、B值被分别存储在`img[(y * w + x) * 3]`, `img[(y * w + x) * 3 + 1]`, `img[(y * w + x) * 3 + 2]`

对于32位的RGBA格式(`alpha != 0`)，R、G、B、A值被分别存储在 `img[(y * w + x) * 4]`, `img[(y * w + x) * 4 + 1]`, `img[(y * w + x) * 4 + 2]`, `img[(y * w + x) * 4 + 3]`

## 
## 例子
[example.c](example.c) 保存一个RGB和一个RGBA数据格式的PNG图片

~~~c
void test_rgb(void) {
    unsigned char rgb[256 * 256 * 3], *p = rgb;
    unsigned x, y;
    FILE *fp = fopen("rgb.png", "wb");
    for (y = 0; y < 256; y++)
        for (x = 0; x < 256; x++) {
            *p++ = (unsigned char)x;    /* R */
            *p++ = (unsigned char)y;    /* G */
            *p++ = 128;                 /* B */
        }
    svpng(fp, 256, 256, rgb, 0);
    fclose(fp);
}

void test_rgba(void) {
    unsigned char rgba[256 * 256 * 4], *p = rgba;
    unsigned x, y;
    FILE* fp = fopen("rgba.png", "wb");
    for (y = 0; y < 256; y++)
        for (x = 0; x < 256; x++) {
            *p++ = (unsigned char)x;                /* R */
            *p++ = (unsigned char)y;                /* G */
            *p++ = 128;                             /* B */
            *p++ = (unsigned char)((x + y) / 2);    /* A */
        }
    svpng(fp, 256, 256, rgba, 1);
    fclose(fp);
}

int main(void) {
    test_rgb();
    test_rgba();
    return 0;
}
~~~

## 编译及执行

~~~
gcc example.c && ./a.out
~~~

## 输出

![rgb.png](rgb.png)

![rgba.png](rgba.png)

# 自定义

用户可以在包含`svpng.inc`前定义以下宏

| Macro           | Default         | Description |
|-----------------|-----------------|-------------|
| `SVPNG_LINKAGE` | (empty)         | Linkage of `svpng()` function, e.g. `inline`, `static`, `extern "C"`, etc. |
| `SVPNG_OUTPUT`  | `FILE* out`     | Output stream parameter, e.g. `std::ostream& os`, `std::vector<std::uint8_t>& buffer`, etc. |
| `SVPNG_PUT(u)`  | `fputc(u, out)` | Output a byte into stream, e.g. `os.put(u)`, `buffer.push_back(u)`, etc. |
