---
title: 如何使用 npm 管理包
tags: [node]
comments: true    // 是否开启评论
date: 2019-03-30 17:07:35
# description: 如何使用dist-tags & version标记包
---

> 分布标签（dist-tags）补充语义版本控制（例如，v0.12）。使用它们来组织和标记不同版本的包。除了比 semver 编号更具人性可读性之外，标签还允许发布者更有效地分发他们的包。

### 添加标签

要将标记添加到包的特定版本，请使用：

```bash
npm dist-tag add <pkg>@<version> [<tag>]
npm dist-tag rm <pkg> <tag>
npm dist-tag ls [<pkg>]

aliases: dist-tags
```

### 使用标签发布

默认情况下，`npm publish`会使用标记标记您的包`latest`。如果使用该`--tag`标志，则可以指定要使用的另一个标记。例如，以下内容将使用`beta`标记发布您的包：

```bash
npm publish --tag beta
```

### 使用标签安装

默认`npm install <pkg>`会使用`latest`标签。要覆盖此行为，请使用`npm install <pkg>@<tag>`。以下示例将安装`somepkg`已标记的版本`beta`。

```bash
npm install somepkg@beta
```

### 注意事项

因为`dist-tag`与`semver`共享相同的名称空间，所以请避免使用可能导致冲突的标记名称。最佳做法是避免使用以数字或字母`v`开头的标签。

### 使用 `npm version`

```bash
npm version [<newversion> | major | minor | patch | premajor | preminor | prepatch | prerelease [--preid=<prerelease-id>] | from-git]

'npm [-v | --version]' to print npm version
'npm view <pkg> version' to view a package's published version
'npm ls' to inspect current package/dependency versions
```

### `npm version` 的`hooks`

```bash
"scripts": {
  "preversion": "npm test",
  "version": "npm run build && git add -A dist",
  "postversion": "git push && git push --tags && rm -rf build/temp"
}
```
