---
title: 同步Flomo卡片到markdown
description: 同步Flomo卡片到markdown
date: 2024-03-03
updated: 2024-03-03
taxonomies:
  tags:
    - 笔记
    - Flomo
extra:
  emoji: 🗃️️
---
年初开始了解到 Flomo，尝试注册了个账号并试用了下。开始的感触是功能怎么这么少，就一个简单的输入框，而且文本格式选项支持的也不多。抱着这么简单随便用下也没什么成本的想法，把自己一些简单的事项记录到上面。包括一些简单的 todo list，或者临时想到的内容。

偶尔在使用 Flomo 的时候会想，这么简单的工具有什么特别的亮点吗，感觉随便一个工具都能秒杀它。不说全能的 Notion、Obsidian，即使相比很简单的滴答清单、Microsoft To Do 感觉也没什么优势。有次正好通过 Flomo 的消息看到官方的帮助文档，也就是 flomo 101。但是看到笔记的方法时有一点小惊讶，没想到还教你怎么记笔记。

不过这个是正好击中我的兴趣点，我一直想优化自己的笔记方式。虽然现在通过 Obsidian 记录了不少东西，但是感觉没有体系很凌乱。之前也整理过几次结构，但是感觉效果也不咋样。网上也看到了一些教程，感觉都太复杂了也就没继续。简单看了笔记方法的内容后，感觉里面卡片笔记挺有实操性的，而且对于记录哪些内容也有一些指导。通过里面引导，我发现作者竟然还出了本书《笔记的方法》，然后一发不可收拾的，又去看书。

到现在也用 Flomo 零零散散记了 130 多张卡片，主要还是一些零散的内容，大块的笔记还是通过 Obsidian，并且 Flomo 的内容经过整理后，我还是会转移到 Obsidian 中。不过一直有一个痛点，Flomo 的笔记只能通过它的 APP 或者通过网页查看，我平时还是习惯使用 Obsidian 作为个人的知识中心，就想着是否可以将 Flomo 同步到 Obsidian 中。

期间也找了一些 Obsidian 插件，发现主要的实现都是通过 Flomo 官网离线导出，然后再导入到 Obsidian 中。这里的问题是 Flomo 7 天只能导出一次，而且导入的笔记格式跟我想要的不是很一致。搜索的时候发现 GitHub 上有 Python 代码可以使用官方 API 导出卡片数据，就萌生了自己写个脚本同步到 Obsidian 的想法。

正好乘着年底事情不太多，通过 ChatGPT 实现了一个简单的全量同步脚本。这个脚本已经差不多实现我当初想要的同步功能，不错一些不足，首先是只能全量同步，每次都需要删除之前的数据。另外是代码写的有够烂，心里总感觉不爽。在年后回来后，又花了些时间重构了下。现在能支持增量同步，包括更新、删除的卡片数据，也避免给 Flomo 的服务器造成太大压力。代码也根据笔记作为模块划分，相对清爽一些，虽然感觉还是一坨😂。

同步效果：
![flomo卡片效果](flomo-sync.webp)

脚本的使用方式：
```bash
usage: main.py [-h] dir_path auth

Sync flomo to markdown

positional arguments:
  dir_path    The markdown directory path
  auth        The Flomo authorization

options:
  -h, --help  show this help message and exit
```

脚本有两个参数，第一个是需要同步的目标文件夹，会在指定的文件夹中创建一个 flomo 子文件夹，然后根据时间同步对应的笔记，还会创建一个 tags 文件夹，里面同步所有的标签，标签会通过嵌入连接的方式引用笔记。

目录结构：
```bash
flomo
├─ 2024
│    ├─ 01
│    │    ├─ 2024-01-15.md
│    ├─ 02
│    └─ 03
├─ flomo.md
└─ tags
       ├─ 项目.md
       └─ 领域.md
```

同时为了方便使用，做了一个简单的 Alfred Workflow，用于一键同步。

[flomo-sync](https://github.com/linq/flomo-sync)
