---
{"dg-publish":true,"dg-permalink":"python-script-for-newbees","permalink":"/python-script-for-newbees/","created":"2022-06-15T09:59:13.000+08:00"}
---

# 写Python脚本给不会编程的同事处理文本
作为英语编辑和教研，我们经常需要做一些处理文本的工作。

最常见的场景时筛词，比如拿着考研的词表去匹配小学的词表，看看考研考纲词里有多少个小学词汇。

另一个场景是去重，网上下载的单词表格式千奇百怪，有时甚至有重复的单词，特别是当我们需要汇总各个版本的词汇表时，去重功能更是刚需。

每次都来找我操作有点花时间，不如写一个脚本给同事，大体思路是把输出的文件地址写死，使用argparse控制输入的内容，在代码中给出足够的打印信息，讲好使用方法，开箱就能用：
1. 点击右上角的放大镜，打开终端
2. 先输入python
3. 依次拖入脚本文件、要筛的词表和筛子
4. 如果有特殊需求，输入flag

下面是去重单词的示例：

```Python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

import argparse

parser = argparse.ArgumentParser()
parser.add_argument("file", nargs="*")
parser.add_argument('--delete', '-d', action="store_true", default=False, help="Delete Repeated Words")
args = parser.parse_args()

output = "/Users/name/Desktop/output.txt"


def get_word_freq(list):
    print("正在检测是否有重复单词...")
    freq_list = []
    temp_list = {}
    for word in list:
        if word not in temp_list:
            temp_list[word] = 1
        else:
            temp_list[word] += 1

    for k, v in sorted(temp_list.items(), key=lambda p: p[1], reverse=True):
        entry = k.strip("\n") + " " + str(v)
        freq_list.append(entry)
    return freq_list


def main():
    with open(args.file[0], 'r') as f:
        list = [w.strip("\n") for w in f]
    freq = get_word_freq(list)
    print("重复的单词如下...")
    for line in freq:
        if not line.endswith('1'):
            print(line)

    if args.delete:
        new_list = []
        [new_list.append(i) for i in list if i not in new_list]
        print("正在生成去重后的文件...")
        with open(output, 'w') as w:
            for word in new_list:
                w.write(word + '\n')


if __name__ == "__main__":
    main()

```