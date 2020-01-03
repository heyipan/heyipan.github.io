---
title: Hexo搭建个人博客
date: 2019-11-14 23:10:17
categories: hexo
top: 100
tags:
    - hexo
    - 学习笔记和总结
photos: 
    - "https://desk-fd.zol-img.com.cn/t_s1440x900c5/g5/M00/0C/00/ChMkJ13VHGeIQbMvAAZiH-yBkAsAAvVdQCGfhkABmI3388.jpg"
---
<!--more-->
## 准备环境

1. 安装 Node
2. 安装 Git
3. 注册码云或者github
4. 安装 Hexo

```
npm install hexo-cli -g
```
- 出现下图表示安装成功

![](http://zwd.yyzheng.cn/hexo_manong_blog/933c7992797c0f0fs.png)

## 搭建本地个人博客

### 初始化 hexo
- 新建一个空白文件夹（下文提到的“项目根目录”是指你新建的文件夹的位置）用于存放 hexo 资源。在空白文件夹里面打开 Git Bash ，输入下列命令行进行初始化，初始化成功后会在文件夹生成如下图的文件。

``` 
hexo init
```
![](http://zwd.yyzheng.cn/hexo_manong_blog/2454d988bda5f01es.png)

### 生成静态页面
```
hexo g
```
### 启动本地服务
```
hexo s
```
- 关闭本地服务器在 Git Bash 界面按 Ctrl+C， 在浏览器输入：http://localhost:4000 查看

![](http://zwd.yyzheng.cn/hexo_manong_blog/ecc250d77459285bs.png)

## 美化个人博客

### 博客主题设置

#### 克隆主题

- 在项目根目录下的 themes 文件中，打开 Git Bash ，用命令行克隆下新的主题。我这里用的 Next 主题，需要其他主题的自己百度找。

```
git clone https://github.com/theme-next/hexo-theme-next.git
```
![](http://zwd.yyzheng.cn/hexo_manong_blog/3e09b4d14b279371s.png)

### 配置主题
- 用文本的方式打开项目根目录下的 _config.yml 配置文件，找到 theme 把原来默认的 landscape 主题名字，改成刚刚克隆的主题名字（主题名字为上图中文件夹的名字）。

![](http://zwd.yyzheng.cn/hexo_manong_blog/7a032ed5279bfee2s.png)

### 测试主题
 - 重新回到项目根目录下，打开 Git Bath ，用命令行启动服务器。在浏览器访问 http://localhost:4000
 
![](http://zwd.yyzheng.cn/hexo_manong_blog/51e4059c599bbd2fs.png)

### 发布文章

- 方法一：在项目根目录下，打开 Git Bash ，执行新建命令，然后 hexo 会自动在指定目录下生成对应文件，如下图所示。然后找到新建好的文件，打开即可进行编辑。

```
hexo new "此处输入文章名字"
```
![](http://zwd.yyzheng.cn/hexo_manong_blog/596fc4a69978bed5s.png)

- 方法二：可以直接把已经准备的 md 格式的文章复制到 项目名称 /source/_posts 目录下，然后打开文件，在文件头加入 front-matter 部分，title 表示文章标题，date 表示发布时间。如图所示，图片上用到的其他参数，后面会介绍到。

> front-matte 书写的时候要注意，冒号后面要跟一个空格号

```
---
title: a
date: 2019-04-14 23:10:17
---
```
![](http://zwd.yyzheng.cn/hexo_manong_blog/e9512a34d0ffddaas.png)

- 准备好 md 格式文件后，使用下面命令生成网站静态文件到默认设置的 public 文件夹，然后再启动本地服务器。
```
hexo g
```

### 主题风格设置
- 打开主题文件夹下的 _config.yml 配置文件（注意：这里要区别，不是項目根目录，主题文件夹的路径为：新建空白文件夹名称/themes/主题文件夹名称）。通过查找功能找到 Schemes 模块，修改为 Gemini 风格。如果喜欢其他风格可以自己修改。如下图所示：
![](http://zwd.yyzheng.cn/hexo_manong_blog/57735b310de44a59s.png)

- 刷新页面可以看到新风格的界面如下图所示：
![](http://zwd.yyzheng.cn/hexo_manong_blog/a17bbca793681dbes.png)

### 博客左侧栏设置
- 在上面的网站界面，可以发现网站的文字是英文，只要修改一下语言模式即可。打开根目录文件夹下的 _config.yml 配置文件。找到 language，设置为 zh-CN。标题等其他参数的设置如下。可以对照效果图的具体位置，根据自己的实际需求进行修改。（注意：修改了项目根目录下的 _config.yml配置文件，需要重启部署项目后才能生效）

![](http://zwd.yyzheng.cn/hexo_manong_blog/cf7f8b7318ba13aes.png)
![](http://zwd.yyzheng.cn/hexo_manong_blog/89e91ba175bfc5d7s.png)
![](http://zwd.yyzheng.cn/hexo_manong_blog/89e91ba175bfc5d7s.png)

### 分类设置
#### 添加分类列表
- 在项目根目录下，执行下面的命令行，新建分类页面，然后会在项目根目录下的 source 文件夹中新建一个 categories 文件夹。
```
hexo new page categories
```
![](http://zwd.yyzheng.cn/hexo_manong_blog/ec8bf34d15fb441es.png)

- 打开 categories 文件夹中的 index.md 文件，添加 type 字段，设置为 “categories”。如下图所示。

![](http://zwd.yyzheng.cn/hexo_manong_blog/8b72d1e4839a457fs.png)

- 接着到主题文件夹下的 _config.yml 配置文件下，找到 menu 模块，把 categories 的注释给去掉。如下图所示。

![](http://zwd.yyzheng.cn/hexo_manong_blog/3205c65c02e1d02cs.png)

- 刷新页面（如果刷新没效果，可以重启服务），可以在页面左侧栏上看到多了一个“分类”列表。
- 
![](http://zwd.yyzheng.cn/hexo_manong_blog/3c0bcb4e00b69674s.png)

####  如何将文章添加到对应分类？
- 文章发布前，在 front-matter 部分，多写一个 categories 字段，然后参数写上类别的名称，保存后重启服务，在网页上点击“分类”，可以看到分类下已经生成了刚刚设置的类别，并把刚刚发布的文章归类在此类别下。如下图所示。
![](http://zwd.yyzheng.cn/hexo_manong_blog/9c256cae759cdcffs.png)

### 标签设置

- 方法跟分类设置一样，所以不再赘述介绍
- 但是需要补充一点， front-matter 中字段有多个参数的时候，可以使用如下图的写法。
![](http://zwd.yyzheng.cn/hexo_manong_blog/7f1dc337505102c6s.png)

### Hexo 博客添加站内搜索
- NexT主题支持集成 Swiftype、 微搜索、Local Search 和 Algolia。下面介绍 Local Search 的安装吧。注意：安装的时候要是项目根目录下安装。
- 安装 hexo-generator-search
```
npm install hexo-generator-search --save
```
- 安装 hexo-generator-searchdb
```
npm install hexo-generator-searchdb --save
```
- 在项目根目录下的 _config.yml 配置文件的文末添加下面这段代码。
```
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```
- 编辑主题文件夹的 _config.yml 配置文件，设置 Local searchenable 为 ture。
![](http://zwd.yyzheng.cn/hexo_manong_blog/7c51e609df9e5c42s.png)
- 重启服务，效果图如下：
![](http://zwd.yyzheng.cn/hexo_manong_blog/f92ee473a8c701cds.png)

### 博客头像设置
#### 添加博客头像
- 打开主题文件夹下的 _config.yml 配置文件，通过查找功能找到 avatar，然后把一个在线的头像图片地址（百度图片中直接复制链接即可），作为 url 的参数。如下图所示：
![](http://zwd.yyzheng.cn/hexo_manong_blog/a0137663728e3f0fs.png)
- 然后刷新页面，可以看到网站上已经显示了相应的头像了： 
![](http://zwd.yyzheng.cn/hexo_manong_blog/c8ee1492e43b626as.png)

#### 设置头像圆角并旋转打开
- 打开主题文件夹的 source\css_common\components\sidebar 目录下的 sidebar-author.styl 文件，然后把下面的代码添加进去即可。
```css
.site-author-image {
  display: block;
  margin: 0 auto;
  padding: $site-author-image-padding;
  max-width: $site-author-image-width;
  height: $site-author-image-height;
  border: $site-author-image-border-width solid $site-author-image-border-color;
  /* 头像圆形 */
  border-radius: 80px;
  -webkit-border-radius: 80px;
  -moz-border-radius: 80px;
  box-shadow: inset 0 -1px 0 #333sf;
  /* 设置循环动画 [animation: (play)动画名称 (2s)动画播放时长单位秒或微秒 (ase-out)动画播放的速度曲线为以低速结束 
    (1s)等待1秒然后开始动画 (1)动画播放次数(infinite为循环播放) ]*/
 
  /* 鼠标经过头像旋转360度 */
  -webkit-transition: -webkit-transform 1.0s ease-out;
  -moz-transition: -moz-transform 1.0s ease-out;
  transition: transform 1.0s ease-out;
}
img:hover {
  /* 鼠标经过停止头像旋转 
  -webkit-animation-play-state:paused;
  animation-play-state:paused;*/
  /* 鼠标经过头像旋转360度 */
  -webkit-transform: rotateZ(360deg);
  -moz-transform: rotateZ(360deg);
  transform: rotateZ(360deg);
}
/* Z 轴旋转动画 */
@-webkit-keyframes play {
  0% {
    -webkit-transform: rotateZ(0deg);
  }
  100% {
    -webkit-transform: rotateZ(-360deg);
  }
}
@-moz-keyframes play {
  0% {
    -moz-transform: rotateZ(0deg);
  }
  100% {
    -moz-transform: rotateZ(-360deg);
  }
}
@keyframes play {
  0% {
    transform: rotateZ(0deg);
  }
  100% {
    transform: rotateZ(-360deg);
  }
}
```
- 效果图如下：
![](http://zwd.yyzheng.cn/hexo_manong_blog/0a14468ce0d901d9.gif)

### 网页背景设置
- 打开主题文件夹下的 source 文件夹，进入 css/_custom 文件下，用文本形式打开 custom.styl 文件，然后添加下面这段代码。代码中 url 的地址是指到： 主题文件夹/source/images/ 。
```css
body{
    background:url(/images/bg.jpg);
    background-size:cover;
    background-repeat:no-repeat;
    background-attachment:fixed;
    background-position:center;
    // 设置主题部分的透明度，具体看图
    opacity: 0.8;
}
```
效果图如下：
![](http://zwd.yyzheng.cn/hexo_manong_blog/3d0102f69054f0e6s.png)

### 首页文章预览设置
- 默认情况下，文章在首页是全文显示的，这样肯定是不方便读者浏览。所以需要实现预览模式。效果图如下：
![](http://zwd.yyzheng.cn/hexo_manong_blog/5e832db322ee611es.png)

- 方法一：使用 < !–more–> 手动切断

这种方法可以根据文章的内容，自己在合适的位置添加 < !–more–> 标签，使用灵活，也是Hexo推荐的方法。

- 方法二：添加 description

在文章的 front-matter 中添加 description 和 photos 字段，如下图所示。如果不需要显示图片的话，可以把 photos 去掉。
ps：不知道 front-matter 是什么的话，跳转到第二章的第4点的发布文章看下。
![](http://zwd.yyzheng.cn/hexo_manong_blog/df10c8fb9de7b7b8s.png)
- 方法三：自动形成摘要

在主题文件下的_config.yml配置文件中添加默认截取的长度为 150 字符，可以根据需要自行设定。

### 设置网站图片 Favicon
- 在 阿里巴巴矢量图标库 中找到自己的喜欢的图标，下载下来，覆盖掉主题文件夹下的 source/images 目录里面的三张图片即可。
![](http://zwd.yyzheng.cn/hexo_manong_blog/c4e5e945c3a1c0d5s.png)

### 网页顶部进度加载条设置
![](http://zwd.yyzheng.cn/hexo_manong_blog/00fc9d55b074d32es.png)

### 博客置顶设置
- 安装插件
```
npm uninstall hexo-generator-index --save
npm install hexo-generator-index-pin-top --save
```
- 然后在需要置顶的文章的 Front-matter 中加上 top 即可，数值越大表示等级越高，越靠前显示。
```
---
title: this is my first blog
date: 2019-04-14
top: 100
---
```
-在主题文件夹中打开 layout/_macro/post.swig 文件，定位到 post-header ，把下面的代码添加进去即可。
```
{% if post.top %}
  <i class="fa fa-thumb-tack"></i>
  <font color=7D26CD>置顶</font>
{% endif %}
```
![](http://zwd.yyzheng.cn/hexo_manong_blog/8b095f7c592707bas.png)
- 重启服务，效果图如下：
![](http://zwd.yyzheng.cn/hexo_manong_blog/51bfdaba5f3841a0s.png)

### 在hexo博客中添加图片：

参考文章：
- [官方网站](https://hexo.io/zh-cn/docs/setup)
- [码云+Hexo搭建个人博客+评论功能接入](http://zwd596257180.gitee.io/blog/2019/04/15/hexo_manong_bog/)
- [Hexo+Next 添加菜单分类页面](https://www.jianshu.com/p/3fe88ef479dd)
- [hexo史上最全搭建教程](https://blog.csdn.net/sinat_37781304/article/details/82729029)
- [最全Hexo博客搭建+主题优化+插件配置+常用操作+错误分析](https://www.simon96.online/2018/10/12/hexo-tutorial/)
- [利用Hexo将博客部署到GitPages和CodingPages](https://blog.csdn.net/nightmare_dimple/article/details/86661465)