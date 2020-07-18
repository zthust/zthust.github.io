---
title: Hexo introuduction
---

# 部署hexo个人博客
## 1. 安装hexo

```
npm install -g hexo-cli

hexo init hexo-blog

```

## 2. 建立仓库
```
github中新建一个仓库

1. 仓库名为username.github.io
2. 打包产品必须部署到master分支

```

## 3. 部署
```
git init
git remote add origin https://github.com/zthust/zthust.github.io.git

git commit -m "init" #commit

yarn add hexo-deployer-git

_config.yml中配置
deploy:
  type: git
  repo: https://github.com/zthust/zthust.github.io.git
  branch: master
  
npm run deploy #public目录中的内容将会部署到master分支

git push origin master:dev #项目到dev分支 
```

## 4. 自动化打包部署

目的是将本地的更新部署到master分支

1. 根目录中创建 .github/workflows/deploy.yml

```
name: Build and Deploy
on: [push]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2 # If you're using actions/checkout@v2 you must set persist-credentials to false in most cases for the deployment to work correctly.
        with:
          persist-credentials: false

      - name: Install and Build  # This example project is built using npm and outputs the result to the 'build' folder. Replace with the commands required to build your project, or remove this step entirely if your site is pre-built.
        run: |
          npm install
          npm run build
        env:
          CI: false

      - name: Deploy 
        uses: JamesIves/github-pages-deploy-action@releases/v3
        with:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          BRANCH: master # The branch the action should deploy to.
          FOLDER: public # The folder the action should deploy.
```

当本地push到dev分支时，会将public目录更新到master分支上。


