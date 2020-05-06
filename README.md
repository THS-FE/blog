我们现在使用的博客框架是[Hexo](https://hexo.io/zh-cn/)，感兴趣的同学可以去了解下。

### 下载源码

从GitHub的仓库中下载博客工具源码到自己机器上

```bash
git clone https://github.com/THS-FE/blog.git
```

### 安装依赖

进入项目所在文件夹，执行

```bash
npm install -g hexo-cli
npm install
```

### 新建帖子

```bash
hexo new post '博客的名称'
```

需要用到图片等资源都放进新生成的文件夹中，图片尽量是JPG格式。
使用以下语句来引入图片

```javascript
{% asset_img 你的图片名称.jpg %}
```

### 删除帖子

直接在source\_posts文件夹下删除对应的md文件和文件夹，再运行命令

```bash
hexo clean
hexo g
```

### 运行博客

```bash
hexo s
```

执行成功后，在浏览器中输入 <http://localhost:4000/>

### 发布博客

前提是你有这个仓库的修改权限。
修改提交说明
{% asset_img deploy.jpg %}

运行命令

```bash
hexo g
hexo deploy
```

第一次会要求输入你Github的用户名和密码。

如果部署以后发现 <https://ths-fe.github.io/> 博客没有更新，需要手动删除项目中的.deploy_git文件夹，重新运行命令

```bash
hexo clean
hexo g -d
```
