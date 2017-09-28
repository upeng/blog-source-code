---
title: Mac下的终端和命令行item2和zsh
date: 2016-12-04 23:19:50
tags: 
  - item
  - zsh
  - mac
categories: 其他
---


关于zsh和item2的关系可以理解为，zsh就是linux下bash shell的升级版；iterm2是termainal的升级版，可以自定义terminal主题配色；`cmd + ,`呼出配置。可以新增配置，并将之设置默认，（默认配置前有个⭐）
<!-- more -->

### [](#zsh的高效命令（常用） "zsh的高效命令（常用）")zsh的高效命令（常用）

1.  zsh 的命令**补全**功能非常强大，可以补齐路径，补齐命令，补齐参数等。你记住 tar 的所有参数了吗 ，只要 tar -加 tab 键

2.  按下 tab 键显示出所有待选项后，再按一次 tab 键，即进入选择模式.进入选择模式后，按 tab 切向下一个选项，按 `shift+tab` 键切向上一个选项，`ctrl+f/b/n/p`可以向前后左右切换

3.  `ps aux | grep xxx`查进程的 PID，然后kill PID有了 zsh，直接kill xxx然后按下 tab 键即可

    ```
    kill emac
    # 按下 tab，变成：
    kill 59683

    ```

4.  zsh的历史记录跨 session，可以共享。历史记录支持受限查找。比如，输入git，再按向上箭头，会搜索用过的所有 git 命令

5.  zsh 的目录跳转更为智能，你无需输入cd，直接输入路径即可。`..`表示后退一级目录，`../../`表示后退两级，依次类推。（`...`的作用和`../../`相同）

6.  输入d回车，将列出当前 session 访问过的所有目录，再输入提示的数字即可进入相应目录。

### [](#zsh配置 "zsh配置")zsh配置

位于 ~/.zshrc，可以修改theme,当前使用cloud,可以自定义alias

### [](#iterm2的配置 "iterm2的配置")iterm2的配置

color配色方案：colors import我们githubs主题即可，更多关于iterm的信息见
[https://item2.com。](https://item2.com。)
比如选择dracula配色方案
[https://github.com/dracula/iterm](https://github.com/dracula/iterm)

> `working directory：Reuse previous'directory` //directory总是展示当前dirname