---
title: Bash Shell Practices
published: 2025-05-22
updated: 2025-05-29
description: '一些 shell 脚本实践'
image: ''
tags: [Bash]
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
### 创建code命令，使用IDE打开文件

```shell
#!/bin/zsh

# 0参数  直接vscode .
# 1参数  code $1 => code vscode / code idea / code goland
# ide 直接打开当前路径
# 2参数  code $1 $2 => code vscode ddd / code idea ddd / code goland dd
# 先选择ide 在选择路径

# 定义 IDE 命令映射（关联数组）
typeset -A IDE_COMMANDS=(
    [vs]="open -a 'Visual Studio Code'"      # VSCode
    [idea]="open -a 'IntelliJ IDEA'"         # IDEA
    [go]="open -a 'GoLand'"                  # Goland
    [web]="open -a 'WebStorm'"               # WebStorm
    # 添加更多 IDE...
)

# 参数处理逻辑
case $# in
    0)  # 无参数：用 VSCode 打开当前目录
        eval "${IDE_COMMANDS[vs]} ."
        ;;
    1)  # 一个参数：用指定 IDE 打开当前目录
        ide_type=$1
        if [[ -n "${IDE_COMMANDS[$ide_type]}" ]]; then
            eval "${IDE_COMMANDS[$ide_type]} ."
        else
            echo "🔴 错误：未知 IDE 类型 '$ide_type'"
            echo "可用选项: ${(k)IDE_COMMANDS[@]}"
            exit 1
        fi
        ;;
    2)  # 两个参数：用指定 IDE 打开目标路径
        ide_type=$1
        target_path=$2
        if [[ -n "${IDE_COMMANDS[$ide_type]}" ]]; then
            if [[ -d "$target_path" ]]; then
                eval "${IDE_COMMANDS[$ide_type]} '$target_path'"
            else
                echo "🔴 错误：路径不存在 '$target_path'"
                exit 2
            fi
        else
            echo "🔴 错误：未知 IDE 类型 '$ide_type'"
            echo "可用选项: ${(k)IDE_COMMANDS[@]}"
            exit 1
        fi
        ;;
    *)  # 错误参数数量
        echo "🔴 用法:"
        echo "  code              -> 用 VSCode 打开当前目录"
        echo "  code <IDE类型>    -> 用指定 IDE 打开当前目录"
        echo "  code <IDE类型> <路径> -> 用指定 IDE 打开目标路径"
        exit 3
        ;;
esac
```

在 `bashrc` 或者 `zshrc` 配置文件中添加别名

```
# For a full list of active aliases, run `alias`.
...
alias code="$ZSH/scripts/ide-launch.sh"
...
```

