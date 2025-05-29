---
title: Webp
published: 2025-05-11
updated: 2025-05-28
description: 'An image format for the Web'
image: ''
tags: [Webp, Front End]
category: 'Misc'
draft: false 
lang: ''
---

## What is Webp

> [Google Webp Official](https://developers.google.com/speed/webp/docs/using?hl=zh-cn)
>
> [Developer Guides](https://developers.google.com/speed/webp/docs/using)

WebP is a modern image format that provides superior lossless and lossy compression for images on the web. Using WebP, webmasters and web developers can create smaller, richer images that make the web faster.

WebP lossless images are 26% smaller in size compared to PNGs. WebP lossy images are 25-34% smaller than comparable JPEG images at equivalent SSIM quality index.

### WebP Support

WebP is natively supported in Google Chrome, Safari, Firefox, Edge, the Opera browser, and by many other tools and software libraries. Developers have also added support to a variety of image editing tools.

WebP includes the lightweight encoding and decoding library [`libwebp`](https://developers.google.com/speed/webp/docs/api) and the command line tools [`cwebp`](https://developers.google.com/speed/webp/docs/cwebp) and [`dwebp`](https://developers.google.com/speed/webp/docs/dwebp) for converting images to and from the WebP format, as well as tools for viewing, muxing and animating WebP images. The full source code is available on the [download page](https://developers.google.com/speed/webp/download).

### WebP Converter Download

Convert your favorite collection from PNG and JPEG to WebP by downloading the precompiled cwebp conversion tool for [Linux, Windows or macOS](https://developers.google.com/speed/webp/docs/precompiled).

## Using Libwebp

The `libwebp` codec includes an extensive encoder and decoder API.

### Using `cwebp` to Convert Images to the WebP Format

Use cwebp on the command line to convert PNG or JPEG image files to WebP format. You can convert a PNG image file to a WebP image with a quality range of 80 with the command:

```shell
cwebp -q 80 image.png -o image.webp
```

- `-o string` Specify the name of the output WebP file. If omitted, `cwebp` will perform compression but only report statistics.
- `-q float` Specify the compression factor for RGB channels between 0 and 100. The default is 75.

    In case of lossy compression (default), a small factor produces a smaller file with lower quality. Best quality is achieved by using a value of 100.

    In case of lossless compression (specified by the -lossless option), a small factor enables faster compression speed, but produces a larger file. Maximum compression is achieved by using a value of 100.
- `-lossless` Encode the image without any loss.

More advanced options information [reference documentation](https://developers.google.com/speed/webp/docs/cwebp)

### Using `dwebp` to Convert Images from the WebP Format

Use dwebp on the command line to convert WebP image files to the PNG or PPM format. You can convert a WebP image file, say image.webp to a PNG image with the command:

```shell
dwebp image.webp -o image.png
```

- `-o string` Specify the name of the output file (as PNG format by default).

More advanced options information [reference documentation](https://developers.google.com/speed/webp/docs/dwebp)

### Using `webpinfo` to view WebP file info

```shell
webpinfo image.webp
```

Output:

```shell
File: image.webp
RIFF HEADER:
  File size: 944506
Chunk VP8  at offset     12, length 944494
  Width: 8000
  Height: 4300
  Alpha: 0
  Animation: 0
  Format: Lossy (1)
No error detected.
````
