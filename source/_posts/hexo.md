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

### 5. 增加编辑功能

在cactus的layout目录中，修改post.ejs模板
例如hexo.md，通过查看hexo主页的Docs，变量page.source为_posts/hexo.md
```
<article class="post" itemscope itemtype="http://schema.org/BlogPosting">
  <header>
    <%- partial('_partial/post/title', { post: page, index: false, class_name: 'posttitle' }) %>
    <div class="meta">
      <span class="author" itemprop="author" itemscope itemtype="http://schema.org/Person">
        <span itemprop="name"><% if (page.author) { %><%- page.author %><% } else { %><%- config.title %><% } %></span>
      </span>
      <%- partial('_partial/post/date', { post: page, class_name: 'postdate' }) %>
      <%- partial('_partial/post/category') %>
      <%- partial('_partial/post/tag') %>
    </div>
  </header>
  <%- partial('_partial/post/gallery') %>
  <div class="content" itemprop="articleBody">
    <%- page.content %>
  </div>

  <div style="float: right;">
  <a href="https://github.com/zthust/zthust.github.io/edit/dev/source/<%- page.source %>" target="_blank">编辑</a>
</div>
</article>
<%- partial('_partial/comments') %>

```
