---
title: Bash Shell Practices
published: 2025-05-22
description: '一些 shell 脚本实践'
image: ''
tags: [bash]
category: 'Linux'
draft: false 
lang: ''
---

## 将当前路径所有图片压缩成 webp

```shell
#!/bin/bash

# 检查cwebp是否安装
if ! command -v cwebp &> /dev/null; then
    echo "错误：未找到cwebp命令，请先安装WebP工具："
    echo "Ubuntu/Debian: sudo apt-get install webp"
    echo "MacOS: brew install webp"
    exit 1
fi

# 支持的图片格式（可自行扩展）
# formats=("jpg" "jpeg" "png" "tiff" "bmp" "gif")
formats=("png")

# 转换质量参数
quality=100

# 计数器
converted=0
skipped=0

echo "开始批量转换图片到WebP格式（质量${quality}）..."

遍历所有支持的图片格式
for ext in "${formats[@]}"; do
    while IFS= read -r -d $'\0' file; do
        # 生成输出文件名
        output="${file%.*}.webp"
        
        # 跳过已存在的webp文件
        if [[ -f "$output" ]]; then
            echo "跳过已存在文件：$output"
            ((skipped++))
            continue
        fi

        # 执行转换命令
        if cwebp -q $quality "$file" -o "$output" >/dev/null 2>&1; then
            echo "成功转换：$file → $output"
            ((converted++))
        else
            echo "转换失败：$file"
            # 删除可能生成的无效文件
            [[ -f "$output" ]] && rm "$output"
        fi
    done < <(find . -maxdepth 1 -type f -iname "*.${ext}" -print0)
done

# 统计结果
echo ""
echo "转换完成！"
echo "成功转换文件数：${converted}"
echo "跳过已存在文件数：${skipped}"

```
