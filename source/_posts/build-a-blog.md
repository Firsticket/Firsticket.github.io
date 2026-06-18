---
title: 个人博客搭建
categories: [技术]
tag: [hexo,butterfly,博客]
---

## 页面制作
本博客搭建基于 Github 开源项目 [A Hexo Theme: Butterfly](https://github.com/jerryc127/hexo-theme-butterfly "🦋 A Hexo Theme: Butterfly")。

按照该项目教程git到本地，然后对想要修改的地方进行修改。

### 一些经验
_config.butterfly.yml 这个文件（路径：项目根目录/themes）最好复制一份到 hexo 项目的 **根目录** 下，修改的时候是修改根目录下的文件，修改css样式时也是在 /public/css,新建一个 css 文件，然后引入到根目录下的 _config.butterfly.yml 中 head 下，修改 js 同理，新建 js 文件，然后引入 js 文件：


```yaml
inject:
  head:
    # - <link rel="stylesheet" href="/xxx.css">
  bottom:
    # - <script src="xxxx"></script>
```

在新建的文件中写的内容会覆盖主题默认的相关内容。

## Github 托管页面

### 创建仓库
GitHub 上创建仓库，选择 New repository。

Repository name 必须填写为：个人GitHub用户名.github.io。同时确保仓库是 Public。

### 配置 GitHub Actions 自动化部署
#### 第一步
终端里，运行 node --version 命令，记下主版本号。

#### 第二步
仓库页面，点击 Settings 标签 --> 左侧菜单点击 Pages --> Source 处，选择 GitHub Actions,然后保存

#### 第三步
在仓库中，点击 Actions 标签 --> 然后选择 Set up a workflow yourself

将编辑器里的默认内容全部删除，粘贴下面这段代码（记得把 node-version: "20" 里的 20 换成自己电脑装的 node.js 版本号）：

```yaml
name: Pages

on:
  push:
    branches:
      - main # 如果你默认分支是 master，请改成 master

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          submodules: recursive
      - name: Use Node.js 20
        uses: actions/setup-node@v4
        with:
          node-version: "20" # 替换成自己装的 Node.js 主版本号
      - name: Cache NPM dependencies
        uses: actions/cache@v4
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Upload Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
    needs: build
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

文件名命名为 pages.yml --> 点击 Commit changes... --> 选择 Commit changes，然后保存

#### 第四步
把电脑上的博客源文件（不包括 node_modules 和 public 文件夹）推送到 GitHub 仓库。

初始化本地仓库
```bash
$ git init
```

添加远程仓库地址：
```bash
$ git remote add origin https://github.com/你的用户名/你的用户名.github.io.git
```

添加并提交所有文件：
```bash
$ git add .
$ git commit -m "首次提交博客源文件"
```

推送到 GitHub 的 main 分支：
```bash
$ git push -u origin main
# 默认分支是 master，就改成 git push -u origin master
```

## 后续更新和维护
```bash
$ hexo clean   # 清理缓存（可选）
$ git add .
$ git commit -m "更新了博客内容或样式"
$ git push
```
