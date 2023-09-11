---
title: pretty_errors,给你的调试高亮
comments: true
cover: cover.png
date: 2023-09-11 12:09:03
categories:
- Fastapi
keyword: 代码调试技巧,高亮
tags:
- 原创
---

# pretty_errors
> python第三方组件，美化python错误

## 安装

```bash
pip3 install pretty-errors
```

## 局部使用
```python
import pretty-errors
print(1/0)
```
![局部使用](error.png)

## 全局使用
```bash
python3 -m pretty_errors
```
![全局使用](global.png)

## 取消使用
只需要再次输入 python -m pretty_errors，然后输入 C 即可清除。

## 自定义配置
```python
import pretty_errors

# 【重点】进行配置
pretty_errors.configure(
    separator_character = '*', #指定堆栈跟踪行与错误信息之间的分隔符字符
    filename_display    = pretty_errors.FILENAME_EXTENDED, #指定文件名的显示方式
    line_number_first   = True, #指定行号是否放在文件名之前
    display_link        = True, #指定是否显示源码链接
    lines_before        = 5, #指定在错误行之前显示的堆栈跟踪行数
    lines_after         = 2, #指定在错误行之后显示的堆栈跟踪行数
    line_color          = pretty_errors.RED + '> ' + pretty_errors.default_config.line_color, #指定行号的显示颜色
    code_color          = '  ' + pretty_errors.default_config.line_color, #指定代码行的显示颜色
)
```