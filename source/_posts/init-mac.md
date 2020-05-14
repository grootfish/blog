---
title: 初始化 Mac 环境
tags: [Mac]
comments: true    // 是否开启评论
date: 2020-05-14 11:01:13\
categories: [Mac]
description: 初始化 Mac
---

笔者使用的电脑是 Mac.拿到一台新 Mac 需要装各种环境 & 软件。这篇文章主要做一个记录，减少初始化一台新 Mac 的时间，记录给自己用的，每个人的工作环境都不一样，没有参考性

### 初始化终端

- [ ] 先安装 macOS Command Line Tools `xcode-select --install`
- [ ] 安装 homebrew (Mac 包管理工具)
- [ ] 使用 [Homebrew Bundler](https://github.com/Homebrew/homebrew-bundle) 初始化系统
- [ ] 安装 Iterm 2
- [ ] 安装 oh-my-zshell

```zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
- [ ] 把 zsh 设置成默认 shell

```zsh
# 把 zsh 加入 shell 列表
sudo sh -c 'echo /usr/local/bin/zsh >> /etc/shells'
# 设置 zsh 为默认 shell
chsh -s $(which zsh)
```

- [ ] 配置终端主题 & 字体 (我使用 [powerlevel10k](https://github.com/romkatv/powerlevel10k))
- [ ] 编辑器 VS code 增加终端快捷方式

```zsh
sudo ln -s /Applications/Visual\ Studio\ Code.app/Contents/Resources/app/bin/code ~/Applications/code
```

### 初始化编辑器

- [ ] 编辑器配置都通过 `code-settings-sync` 保存在 gist,直接下载配置使用即可

```zsh
code --install-extension Shan.code-settings-sync 
```

### 其他

- [ ] 配置 overWall 环境
- [ ] 按需安装应用
