---
tags:
  - technical
  - Rails
  - Docker
---
### Problem: 
**Không upload được file pdf khi upgrade ruby / rails từ ruby v2.6 lên v3.2.**
### Bối cảnh: 
Dự án có chức năng upload file jpg / pdf, tuy nhiên sau khi nâng ruby từ ver 2.6 lên 3.2 thì upload toàn lỗi  
Sử dụng gem Minimagick là thư viện wrapper for ImageMagick

```
`magick convert /tmp/ActiveStorage-216461-20231214-1-1ekdlk.jpg[0] -auto-orient -resize 88x88> /tmp/image_processing20240212.pdf` failed with status: 1 and error:

convert: no decode delegate for this image format `PDF' @ error/constitute.c/ReadImage/746.

convert: no images defined `/tmp/image_processing20240212.pdf' @ error/convert.c/ConvertImageCommand/3362.
):
```

### Cause:
 Dockerfile build image [alpine linux](https://www.alpinelinux.org/)
 Ruby 2.6 sử dụng imagemagick v6
```
 # Dockerfile

FROM ruby:2.6.3-alpine
RUN apk add --update --no-cache imagemagick6
```

 Khi upgrade ruby 3.2 thì docker image chuyển sang dùng imagemagick v7. Tuy nhiên đây không phải nguyên nhân chính.

Nguyên nhân chính:
 Ruby 2.6 sử dụng linux alpine:18, Ruby 3.2 sử dụng linux alpine:19
 Alpine:18 gồm một imagemagick chứa chung trong [1 package](https://pkgs.alpinelinux.org/packages?name=imagemagick-*&branch=v3.18&repo=&arch=&maintainer=) 
 Alpine:19 phân tách imagemagick thành các [package riêng lẻ] (https://pkgs.alpinelinux.org/packages?page=1&name=imagemagick%2d%2a&branch=v3%2e19) 

### Solution:
Để read / write được file pdf hay các file tương tự cần phải install các package liên quan.

```
# Dockerfile
FROM ruby:3.2.2-alpine

RUN apk add --update --no-cache \
  imagemagick \
  imagemagick-heic \
  imagemagick-jpeg \
  imagemagick-pdf \
  imagemagick-tiff \
  imagemagick-svg \
 imagemagick-webp \
```


Ref:
- https://github.com/ImageMagick/ImageMagick/issues/6935
- https://docs.komagata.org/5932
