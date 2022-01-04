# <center>前言

​        市面上的笔记软件百花齐放，但对Markdown的支持和渲染各不相同，选来选去还是Typora最好用，简洁方便，唯一不足只支持本地存储，怎么办？那就托管到Github上吧，通过Github Pages还能在美丽的页面上访问查看笔记。

> GitHub Pages是免费的静态站点，三个特点：免费托管、自带主题、支持自制页面和Jekyll。

# 1 创建Github Pages页面

登录自己的Github账号，新建仓库。

点击右上角头像-->`Your repositories`-->绿色按钮`New`，开始新建仓库。

Repository name填写规则："github用户名.github.io"。

然后选择`Public`，勾选`Add a README file`，点击`Create Repository`创建即可。

建好仓库之后，在仓库界面选择仓库名称下面一行选项中的"settings"进入到仓库的设置界面中。

可以看到settings下面的`Pages`选项中提示你的网站已经发布在仓库名字对应的网址上了。

访问地址为：https://你的github用户名.github.io/

初始页面很丑，选择主题之后也很丑，那么，美化起来吧。

# 2 利用docsify美化页面

> `docsify`，一个神器的文档网站生成器[文档化 (docsify.js.org)](https://docsify.js.org/#/)
>
> **这节是搬了搬官方使用文档，也可以略过，直接看3的成品自行修改就好。**

## 2.1 手动创建主页index.html

官方初始的index.html如下：

```html
<!-- index.html -->

<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <meta charset="UTF-8">
  <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify@4/themes/vue.css" />
</head>
<body>
  <div id="app"></div>
  <script>
    window.$docsify = {
      //...
    }
  </script>
  <script src="//cdn.jsdelivr.net/npm/docsify@4"></script>
</body>
</html>
```

## 2.2 配置主题

### 2.2.1 官方社区的4种基本主题

```html
<!-- 1 vue.css -->
<link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/vue.css" />
<!-- 2 泡泡.css -->
<link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/buble.css" />
<!-- 3 黑暗.css -->
<link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/dark.css" />
<!-- 4 纯净.css -->
<link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/pure.css" />
```

### 2.2.2 其他主题

docsify-themeable

如下主题配置中选择一款替换原主题配置：

```html
<!-- 1 Theme: Defaults -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/docsify-themeable@0/dist/css/theme-defaults.css">
<!-- 2 Theme: Simple -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/docsify-themeable@0/dist/css/theme-simple.css">
<!-- 3 Theme: Simple Dark -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/docsify-themeable@0/dist/css/theme-simple-dark.css">
```

主题插件替换成如下：

```html
<!-- docsify-themeable (latest v0.x.x) -->
<script src="https://cdn.jsdelivr.net/npm/docsify-themeable@0/dist/js/docsify-themeable.min.js"></script>
```

script中添加主题配置：

```html
<script>
  window.$docsify = {
      // ...
      themeable: {
          readyTransition : true, // default
          responsiveTables: true  // default
      }
  }
</script>
```

head中添加或修改自定义样式：

```shell
<style>
  :root {
    /* Reduce the font size */
    --base-font-size: 14px;

    /* Change the theme color hue */
    --theme-hue: 325;

    /* Change the sidebar bullets */
    --sidebar-nav-link-before-content: '😀';
  }
</style>
```

### 2.2.3 自定义定制主题样式

参考文档：

[docsify-themeable - A delightfully simple theme system for docsify.js (jhildenbiddle.github.io)](https://jhildenbiddle.github.io/docsify-themeable/#/)

自行设计自定义样式。

## 2.3 自定义导航栏

## 2.4 插件

## 2.5 代码块内容突出显示

Docsify 使用[Prism](https://prismjs.com/)来突出显示页面中的代码块。默认情况下，Prism 支持以下语言：

- 标记 - ， ， ， ， ，`markup` `html` `xml` `svg` `mathml` `ssml` `atom` `rss`

- CSS -`css`
- C型 -`clike`
- JavaScript - ，`javascript` `js`

通过 CDN 加载特定于语言的[语法文件](https://cdn.jsdelivr.net/npm/prismjs@1/components/)，可以支持[其他](https://prismjs.com/#supported-languages)语言：

```html
<script src="//cdn.jsdelivr.net/npm/prismjs@1/components/prism-bash.min.js"></script>
<script src="//cdn.jsdelivr.net/npm/prismjs@1/components/prism-php.min.js"></script>
<script src="//cdn.jsdelivr.net/npm/prismjs@1/components/prism-java.min.js"></script>
```

# 3 一个很漂亮的页面模板

> 页面所有样式基本搬自大佬：[ETS' NoteBook - By Mr.Wu - 微信公众号：码客趣分享 🌹](https://notebook.js.org/#/README)

以下是大佬的，请自行修改：

## 3.1 主页配置

根据自己需要自行增添删减。

index.html：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>ETS' NoteBook - By Mr.Wu - 微信公众号：码客趣分享 🌹</title>
  <link rel="icon" href="https://cdn.jsdelivr.net/gh/wugenqiang/StaticRepo/images/favicon.ico">
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  <meta name="description" content="Description">
  <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
  <!-- Adsense 资源投放 -->
  <script data-ad-client="ca-pub-1890271224952559" async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>

  <!-- 谷歌站点收录 -->
  <meta name="google-site-verification" content="qTFCf1hJ275saQ7H1kin5t2DVpznBKAj0Gi50XMOVMo" />
  <!-- 百度站点收录-->
  <meta name="baidu-site-verification" content="SZyWUIzWiU" />

  <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/vue.css">
  <!-- 支持 LaTex 语言 -->
  <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/katex@latest/dist/katex.min.css" />
  <link rel="stylesheet" href="https://wugenqiang.js.org/src/css/iconfont.css">
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/custom.css">
  <!-- alert -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/sweetalert.min.css" type='text/css' media='all' />
  <!-- 友链 -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/css/friends-link.css">
  <!-- 自定义特色样式：by myself -->
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/css/me.css">

  <style>

    /*加重描述strong*/
    .markdown-section strong {
      color: rgb(239, 112, 96);
    }

    .app-nav{
      position: fixed;
      margin: 0;
      /*padding: 10px 50px 10px 0;*/
      padding: 10px 0 10px 0;/*上、右、下、左*/
      width: calc(100% - 325px);
      background-color: #fff;
      height: 55px;
      border-bottom: 1px solid #eee;
    }

    /*.github-corner{
      position: absolute;
      z-index: 999999;
      top: 0;
    }*/

    .markdown-section code {
      border-radius: 2px;
      font-family: "Helvetica Neue",Helvetica,"Hiragino Sans GB","Microsoft YaHei",Arial,sans-serif;
      font-size: 16px !important;
      margin: 0 2px;
      padding: 3px 5px;
      white-space: nowrap;
      border: 1px solid #282c34;
      color: rgb(184, 101, 208);
    }

    .markdown-section > div > img, .markdown-section pre {
      box-shadow: 2px 2px 20px 6px rgb(255, 255, 255) !important;
    }

    .markdown-section a:not(:hover) {
      text-decoration: none;
    }

    /*侧边栏*/
    .sidebar {
      padding-top: 6px;
    }

    aside.sidebar ul li {
      margin: 0;
      position: relative;
    }

    aside.sidebar ul li ul {
      margin: 6px 0;
    }

    aside.sidebar ul li p {
      padding-left: 22px;
      font-size: 18px;
      font-weight: normal;
    }

    aside.sidebar ul li a {
      line-height: 35px;
      font-size: 14px;
      padding: 3px 0 3px 22px;
    }

    aside.sidebar ul li.active > a {
      font-size: 16px !important;
    }

    aside.sidebar ul li.active > a:before {
      content: '' !important;
      position: absolute !important;
      margin: 0 !important;
      width: 10px !important;
      height: 10px !important;
      top: 15px !important;
      left: 0px !important;
      border-radius: 50% !important;
      background-color: #fed24a !important;
      box-shadow: 0 0 0 3px rgba(254, 210, 74, 0.4) !important;
    }

    /*以上调侧边栏*/

    h1 span{
      display:inline-block;
      background: rgb(66, 185, 131);
      color:#ffffff;
      padding:  10px  16px;
      border-radius:5px;
      box-shadow: 1px 1px 3px black;
    }

    /*代码块背景*/
    p code{
      background-color: rgb(255, 255, 255) !important;
    }

    .markdown-section p.tip,
    .markdown-section tr:nth-child(1n) {
      font-size: 16px !important;
    }

    .markdown-section h1 {
      margin: 3rem 0 2rem 0;
    }

    .markdown-section h2 {
      margin: 2rem 0 1rem;
    }
    img,
    pre {
      border-radius: 5px;
    }

    /*添加代码块复制按钮样式*/
    .docsify-copy-code-button {
      background: #00a1d6 !important;
      color: #FFFFFF !important;
      font-size: 13px !important;
    }

    ::after{
      color: #9da2fd !important;
      font-size: 13px !important;
    }
    .markdown-section>p {
      font-size: 16px !important;
    }


    /*代码块头部图标 start*/
    .markdown-section pre:before {
      content: '';
      display: block;
      background: url(https://gitee.com/wugenqiang/PictureBed/raw/master/images01/20200716201955.png);
      height: 30px;
      background-size: 40px;
      background-repeat: no-repeat;
      background-color: #1C1C1C;
      background-position: 10px 10px;
    }
    /*代码块头部图标 end*/

    .markdown-section pre>code {
      color: #c0c3c1 !important;
      font-family: 'Inconsolata', consolas,"PingFang SC", "Microsoft YaHei", monospace !important;
      background-color: #212121 !important;    //#accfff  #282c34
      font-size: 15px !important;
      white-space: pre !important;
      line-height: 1.5 !important;
      -moz-tab-size: 4 !important;
      -o-tab-size: 4 !important;
      tab-size: 4 !important;
      /*border-radius: .3em;*/
    }

    ol, ul, li{
      line-height: 27px !important;
      font-size: 16px !important;
    }
    @media (min-width:600px) {
      .markdown-section pre>code {
        font-size: 15px !important;
        letter-spacing: 1.1px !important;
      }

    }
    @media (max-width:600px) {
      .markdown-section pre>code {
        padding-top: 5px;
        padding-bottom: 5px;
        padding-left: 16px !important;
      }
      pre:after {
        content: "" !important;
      }
    }
    section.cover h1 {
      margin: 0;
    }

    pre {
      background-color: #212121 !important;
    }

    @media (min-width:600px) {
      pre code {
        /*box-shadow: 2px 1px 20px 2px #aaa;*/
        /*border-radius: 10px !important;*/
        padding-left: 20px !important;
      }

    }

    @media (max-width:600px) {
      pre {
        padding-left: 3px !important;
        padding-right: 3px !important;
        margin-left: -20px !important;
        margin-right: -20px !important;
        box-shadow: 0px 0px 20px 0px #f7f7f7 !important;
      }

      /*代码块复制按钮默认隐藏*/
      .docsify-copy-code-button {
        display: none;
      }

      .advertisement{
        display: none;
      }

    }

    .markdown-section pre {
      padding-left: 0 !important;
      padding-right: 0px !important;
    }

    .markdown-section {
      margin: 0 3.2% !important;
    }

    /*修改代码块代码颜色显示*/
    .token.directive.keyword{
      color: #4faee2 !important;
    }

    .token.keyword{
      color: #c678dd !important;
    }

    .token.comment{
      color: #737c8b !important;
    }

    .token.tag{
      color: #a589ad !important;
    }

    .token.attr-name{
      color: #de916c !important;
    }

    .token.attr-value{
      color: #4faee2 !important;
    }

    .token.macro.property{
      color: #4faee2 !important;
    }

    .token.function{
      color: #e6b456 !important;
    }
    .token.string{
      color: #98b755 !important;
    }
    .token.punctuation{
      color: #c0c3c1 !important;
    }

    .token.number{
      color:#c0c3c1  !important;
    }

    a.section-link{
      font-size: .9rem !important;
    }

    .advertisement {
      position: fixed;
      right: 20px;
      top: 100px;
      width: 110px;
      box-shadow: -1px 0 2px 0px #c5ebda;
      padding: 10px;
      z-index: 99;
      background-color: #fff;
      text-align: center;
    }

    .advertisement p,
    h4 {
      margin: 0;
      padding: 0;
    }

    .advertisement .Tencent_code h4 {
      font-size: 15px;
      color: #25a46a;
      margin-bottom: 10px;
    }

    /*滚动条样式 start*/
    /* 滚动条宽度 */
    ::-webkit-scrollbar{width:5px;}
    /* 滚动条颜色 */
    ::-webkit-scrollbar-thumb{
      background: #33a9dc;
      background-image: linear-gradient(#6ecd56, #33a9dc, #cb6196, #c16290);
      border-radius: 2em;
    }

  </style>

</head>
<body>
<div id="app">🏃‍🏃‍🏃‍💨 加载中...</div>

<div class="aside_container">
    <div class="advertisement">
      <div class="Tencent_code">
        <h4>关注作者公众号</h4>
        <p style="font-size: 12px;">万千小伙伴陪你一起学</p>
        <img src="https://cdn.jsdelivr.net/gh/wugenqiang/PictureBed/images01/20200808182633.jpg" alt="EnjoyToShare" />
      </div>
    </div>
</div>

  <script>
    window.$docsify = {
      name: "<p>🌈 ETS' NoteBook</p>",
      nameLink: 'https://notebook.js.org/',
      //repo: 'wugenqiang/NoteBook',
      themeColor: '#007be8', // 主题颜色
      //loadSidebar: true,//_sidebar.md如果为真，则从_sidebar.md文件加载边栏，否则从指定的路径加载
      auto2top: true,  //当路线改变时,滚动到屏幕的顶部
      loadNavbar: true,//_navbar.md如果为真，则从_navbar.md文件加载navbar ，否则从指定的路径加载
      mergeNavbar: true,//Navbar将在小屏幕上与侧边栏合并
      executeScript: true,//执行页面上的脚本。只解析第一个脚本标记（演示）。如果存在Vue，则默认开
      maxLevel: 6,//最大的内容表级别
      //subMaxLevel: 6,//在自定义边栏中添加目录（TOC)
      externalLinkTarget: '_blank', //外链打开方式：_blank表示在新标签页中打开
      coverpage: true,
      onlyCover: true,
      topMargin: 60,//调整top
      //executeScript: true,//执行页面上的脚本，仅解析第一个脚本标签
      search: {
        paths: 'auto',
        placeholder: '🔍 搜索',
        noData: '😒 找不到结果',
        // Headline depth, 1 - 6
        depth: 6,
        maxAge: 86400000, // 过期时间，单位毫秒，默认一天
      },//添加搜索框
      // 谷歌分析 SEO
      ga: 'UA-164658031-2',

      footer: {
        copy: '<div class = "over" >完结</div><br/><span>我能想到最浪漫的事，就是我喝咖啡你付钱~😆😏 ❤️ 打赏地址：<a href="https://wugenqiang.js.org/sponsor/" target="_blank">https://wugenqiang.js.org/sponsor/</a></span><iframe src="https://wugenqiang.js.org/sponsor/" style="overflow-x:hidden;overflow-y:hidden; min-height:240px; width:100%;"  frameborder="0" scrolling="no"></iframe><br/><span id="sitetime"></span><br/><span>Copyright &copy; 2019 - 至今</span>',
        auth: ' <a href="https://wugenqiang.github.io/" target="_blank">🏷️ EnjoyToShare Blog</a> <span> 一个人可以走的很快，但一群人才能走的更远！</span>',
        pre: '<hr/>',
        style: 'text-align: left;',
      },//添加页脚

      markdown: {
        renderer: {
          code: function(code, lang, base=null) {

            if (lang === "dot") {
              return (
                      '<div class="viz">'+ Viz(code, "SVG")+'</div>'
              );
            }

            var pdf_renderer = function(code, lang, verify) {
              function unique_id_generator(){
                function rand_gen(){
                  return Math.floor((Math.random()+1) * 65536).toString(16).substring(1);
                }
                return rand_gen() + rand_gen() + '-' + rand_gen() + '-' + rand_gen() + '-' + rand_gen() + '-' + rand_gen() + rand_gen() + rand_gen();
              }
              if(lang && !lang.localeCompare('pdf', 'en', {sensitivity: 'base'})){
                if(verify){
                  return true;
                }else{
                  var divId = "markdown_code_pdf_container_" + unique_id_generator().toString();
                  var container_list = new Array();
                  if(localStorage.getItem('pdf_container_list')){
                    container_list = JSON.parse(localStorage.getItem('pdf_container_list'));
                  }
                  container_list.push({"pdf_location": code, "div_id": divId});
                  localStorage.setItem('pdf_container_list', JSON.stringify(container_list));
                  return (
                          '<div style="margin-top:'+ PDF_MARGIN_TOP +'; margin-bottom:'+ PDF_MARGIN_BOTTOM +';" id="'+ divId +'">'
                          + '<a href="'+ code + '"> Link </a> to ' + code +
                          '</div>'
                  );
                }
              }
              return false;
            }
            if(pdf_renderer(code, lang, true)){
              return pdf_renderer(code, lang, false);
            }
            //return this.origin.code.apply(this, arguments);
            return (base ? base : this.origin.code.apply(this, arguments));
          }
        }
      },

    }
  </script>
  <script src="//cdn.jsdelivr.net/npm/docsify/lib/docsify.min.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/search.min.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/zoom-image.min.js"></script>

  <!-- 支持 DOT 语言 -->
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/viz.js"></script>
  <!-- 支持 LaTex 语言 -->
  <script src="//cdn.jsdelivr.net/npm/docsify-katex@latest/dist/docsify-katex.js"></script>

  <!-- 添加 PDF 页面展示功能 -->
  <!-- PDFObject.js is a required dependency of this plugin -->
  <!--<script src="//cdnjs.cloudflare.com/ajax/libs/pdfobject/2.1.1/pdfobject.min.js"></script>-->
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/pdfobject.min.js"></script>
  <!-- This is the source code of the pdf embed plugin -->
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/docsify-pdf-embed.js"></script>

  <!-- 复制代码-->
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/docsify-copy-code.min.js"></script>

  <!-- 回到顶部功能 -->
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/jquery.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/jquery.goup.js"></script>
  <script type="text/javascript">
    $(document).ready(function () {
      $.goup({
        trigger: 100,
        bottomOffset: 52,
        locationOffset: 25,
        //title: 'TOP',
        titleAsText: true
      });
    });
  </script>

  <!-- 代码块样式优化-->
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-c.js"></script>
  <!--<script src="//unpkg.com/prismjs/components/prism-cpp.js"></script>-->
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-cpp.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-css.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-docker.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-java.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-javascript.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-json.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-latex.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-sql.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-markdown.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-bash.min.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-php.min.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-scala.min.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-nginx.min.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-json.min.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-markdown.min.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/prism-python.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/js/prism-yaml.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/StaticRepo/src/js/prism-go.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/StaticRepo/src/js/prism-matlab.js"></script>

  <!--使用 Gitter 实现一个 IM 即时通讯聊天室功能-->
  <script>
    ((window.gitter = {}).chat = {}).options = {
      room: 'enjoytoshare/community'
    };
  </script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/sidecar.v1.js" async defer></script>

  <!-- mouse click -->
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/click_heart.js"></script>

  <!-- 复制提醒 -->
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/sweetalert.min.js"></script>
  <script>
    document.body.oncopy = function () {
     swal("复制成功 🎉",
             "若要转载或引用请务必保留原文链接，并申明来源。如果你觉得本仓库不错，那就来 GitHub 给个 Star 吧 😊   - by 吴跟强",
             "success"); };
  </script>

  <!-- 添加页脚 -->
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/docsify-footer-enh.min.js"></script>

  <!-- 添加网站运行时间统计 -->
  <script language=javascript>
      function siteTime() {
         window.setTimeout("siteTime()", 1000);
         var seconds = 1000;
         var minutes = seconds * 60;
         var hours = minutes * 60;
         var days = hours * 24;
         var years = days * 365;
         var today = new Date();
         var todayYear = today.getFullYear();
         var todayMonth = today.getMonth() + 1;
         var todayDate = today.getDate();
         var todayHour = today.getHours();
         var todayMinute = today.getMinutes();
         var todaySecond = today.getSeconds();
         /* Date.UTC() -- 返回date对象距世界标准时间(UTC)1970年1月1日午夜之间的毫秒数(时间戳)
         year - 作为date对象的年份，为4位年份值
         month - 0-11之间的整数，做为date对象的月份
         day - 1-31之间的整数，做为date对象的天数
         hours - 0(午夜24点)-23之间的整数，做为date对象的小时数
         minutes - 0-59之间的整数，做为date对象的分钟数
         seconds - 0-59之间的整数，做为date对象的秒数
         microseconds - 0-999之间的整数，做为date对象的毫秒数 */
         var t1 = Date.UTC(2019, 06, 21, 22, 22, 22); //北京时间2019-06-21 22:22:22 //计划考研的日子，6月20日毕业典礼结束后，人生需要继续努力，加油，看到这句话的朋友们！
         var t2 = Date.UTC(todayYear, todayMonth, todayDate, todayHour, todayMinute, todaySecond);
         var diff = t2 - t1;
         var diffYears = Math.floor(diff / years);
         var diffDays = Math.floor((diff / days) - diffYears * 365);
         var diffHours = Math.floor((diff - (diffYears * 365 + diffDays) * days) / hours);
         var diffMinutes = Math.floor((diff - (diffYears * 365 + diffDays) * days - diffHours * hours) / minutes);
         var diffSeconds = Math.floor((diff - (diffYears * 365 + diffDays) * days - diffHours * hours - diffMinutes * minutes) / seconds);
         document.getElementById("sitetime").innerHTML = " 本站已安全运行 " + diffYears + " 年 " + diffDays + " 天 " + diffHours + " 小时 " + diffMinutes + " 分 " + diffSeconds + " 秒 ";
      }
      siteTime();
  </script>

<script src="https://eqcn.ajz.miesnfu.com/wp-content/plugins/wp-3d-pony/live2dw/lib/L2Dwidget.min.js"></script>
<script>
  L2Dwidget.init({
    "model": {
      //jsonpath控制显示那个小萝莉模型，
      //(切换模型需要改动)
      jsonPath: "https://unpkg.com/live2d-widget-model-koharu@1.0.5/assets/koharu.model.json",
      "scale": 1
    },
    "display": {
      "position": "right", //看板娘的表现位置
      "width": 70,  //小萝莉的宽度
      "height": 140, //小萝莉的高度
      "hOffset": 35,
      "vOffset": -20
    },
    "mobile": {
      "show": true,
      "scale": 0.5
    },
    "react": {
      "opacityDefault": 0.7,
      "opacityOnHover": 0.2
    }
  });
</script>

<!-- 访问量统计 -->
<script async src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/js/busuanzi.pure.mini.js"></script>

<!-- 谷歌分析 -->
<script src="https://cdn.jsdelivr.net/npm/docsify/lib/plugins/ga.min.js"></script>

  <!-- 实现离线化 -->
  <script>
    if (typeof navigator.serviceWorker !== 'undefined') {
      navigator.serviceWorker.register('pwa.js')
    }
  </script>

</body>
</html>
```

## 3.2 侧边栏

根据需要自行修改。

_navbar.md：

```markdown
- [<span class="iconfont icon-icon_fabu"></span> 首页](/README.md)
  - [📌 C](README?id=📌-c)
  - [☁️ C++](README?id=☁%ef%b8%8f-c)
  - [☕️ Java](README?id=☕%ef%b8%8f-java)
  - [🐍 Python](README?id=🐍-python)
  - [🥭 Golang](/README?id=🥭-golang)
  - [🚀 计算机基础](README?id=🚀-计算机基础)
  - [📝 面试有招](README?id=📝-面试有招)
  - [🎨 论文投稿](README?id=🎨-论文投稿)
  - [🐝 生物信息学](README?id=🐝-生物信息学)
  - [🐋 刷题 OJ](README?id=🐋-刷题-oj)
  - [🥼 前端学习](README?id=🥼-前端学习)
  - [🔨 工具 COOL](README?id=🔨-工具-cool)
  - [🎅 赞赏作者](README?id=🎅-赞赏作者)
- [<span class="iconfont icon-csdn"></span> CSDN](https://wugenqiang.blog.csdn.net/)
- [<span class="iconfont icon-wodeguanzhu"></span> 关于本站](关于/)
- [⛷ 生信交流群](https://mp.weixin.qq.com/s/rWAl_jRxay-IVUM1S_19LA)
```

说明：

这里括号里填的是对应地址栏的，把相应地址栏的README后面粘过来就好。

## 3.3 首页面

根据需要自行修改，也可不设置此页面。

_coverpage.md：

```markdown
![icon](https://cdn.jsdelivr.net/gh/wugenqiang/StaticRepo/images/icon.png)

## AI & CS & SE

- 做一个眼中有梁木的人，记录一路走来学习的计算机专业知识 ，力求完美构建 AI & CS & SE 知识体系

<img src="https://img.shields.io/badge/version-v2.0.0-green.svg" data-origin="https://img.shields.io/badge/version-v2.0.0-green.svg" alt=""> 
<img src="https://img.shields.io/github/stars/wugenqiang/NoteBook" data-origin="https://img.shields.io/github/stars/wugenqiang/NoteBook" alt=""> 
<img src="https://img.shields.io/github/forks/wugenqiang/NoteBook" data-origin="https://img.shields.io/github/forks/wugenqiang/NoteBook" alt="">
<img src="https://img.shields.io/github/license/wugenqiang/NoteBook" data-origin="https://img.shields.io/github/license/wugenqiang/NoteBook" alt="">

<br>

<br>

<span id="busuanzi_container_site_pv" style='display:none'>
    👀 本站总访问量：<span id="busuanzi_value_site_pv"></span> 次
</span>
<span id="busuanzi_container_site_uv" style='display:none'>
    | 🚴‍♂️ 本站总访客数：<span id="busuanzi_value_site_uv"></span> 人
</span>

<br>

[GitHub](https://github.com/wugenqiang/NoteBook)
[开始阅读](/README.md)



<!-- 背景色 -->
![color](#fff)
```

## 3.4 渲染页

通过README.md文件进行渲染页面。

README.md：

```markdown
# 🎨 前言

!> <b>说明</b>：做一个有趣的爱分享的人，记录本科及研究生阶段所学的计算机专业知识，力求构建「AI & CS & SE」知识体系。如果你喜欢这个文档网站欢迎到 [GitHub](https://github.com/wugenqiang/NoteBook) 点个 Star，或者交换[友链](https://notebook.js.org/#/Friends/) ( •̀ ω •́ )✧🔑

* ⏳ 爱分享，爱生活！在我眼里，`你永远是不一样的烟火`！觉得还不错的话，记得好好学习吖！
* ✨ 本仓库建立的初衷是为了记录一路走来学习的计算机专业知识，方便之后复习与查看。`起于此，但不止于此`，在不断的摸索和完善，勤能补拙，相信一点点的积累最后汇聚成海！希望我的这个小小的计划，可以帮助到实力强大的你！`止于至善`  🧡🧡



# 🍵 编程语言

## 📌 C

* **【一】C 语言学习笔记**
  * [1 - 编程基础](C/C语言学习笔记-CH01-编程基础.md)
  * [2 - 基本语法](C/C语言学习笔记-CH02-基本语法.md)
  * [3 - 数组](C/C语言学习笔记-CH03-数组.md)
  * [4 - 指针](C/C语言学习笔记-CH04-指针.md)
  * [5 - 字符串](C/C语言学习笔记-CH05-字符串.md)
  * [6 - 结构类型](C/C语言学习笔记-CH06-结构类型.md)
  * [7 - 程序结构](C/C语言学习笔记-CH07-程序结构.md)
  * [8 - 文件](C/C语言学习笔记-CH08-文件.md)
  * [9 - 位运算](C/C语言学习笔记-CH09-位运算.md)
  * [10 - 链表](C/C语言学习笔记-CH10-链表.md)
* [**【二】上机题代码练习**](C/C-Code.md)
* [**【三】南开 100 题 C 语言版**](C/南开100题C语言版.md)

## ☁️ C++

* [C++ 学习笔记](C++/C++Notes.md)

## ☕️ Java

* [Java 入门基础编程笔记](/Java/Java-Base-Notes.md)



## 🐍 Python

* [👒 欢迎进入 Python 王国](Python/) >> Python 编程练习推荐平台：[https://python123.io](https://python123.io) 提供在线编程实践。
* **【一】Python 入门学习笔记**
  * [0 - 6 小时 Python 入门](Python/6小时Python入门/6小时Python入门) >> 推荐先跟这篇文档敲一遍，对 Python 有了初步认识后，再根据下面章节深入学习：
  * [1 - Python 基础](Python/Python入门学习笔记/CH1-Python基础)
  * [2 - 函数](Python/Python入门学习笔记/CH2-函数.md)
  * [3 - 高级特性](Python/Python入门学习笔记/CH3-高级特性.md)
  * [4 - 函数式编程](Python/Python入门学习笔记/CH4-函数式编程.md)
  * [5 - 模块](Python/Python入门学习笔记/CH5-模块.md)
  * [6 - 面向对象编程](Python/Python入门学习笔记/CH6-面向对象编程.md)
  * [7 - 面向对象高级编程](Python/Python入门学习笔记/CH7-面向对象高级编程.md)
  * [8 - 错误、调试和测试](Python/Python入门学习笔记/CH8-错误、调试和测试.md)
  * [9 - IO 编程](Python/Python入门学习笔记/CH9-IO编程.md)
  * [10 - 进程和线程](Python/Python入门学习笔记/CH10-进程和线程.md)
  * [11 - 正则表达式](Python/Python入门学习笔记/CH11-正则表达式.md)
* **【二】Python 数据分析**  >> 推荐使用工具 [Jupyter notebook](Python/Jupyter-notebook使用指南)
  * [1 - 准备工作](Python/Python数据分析/CH01-准备工作.md)
  * [2 - Python 语法基础，IPython 和 Jupyter Notebooks](Python/Python数据分析/CH02-Python语法基础和IPython以及JupyterNotebooks.md)
  * [3 - Python 的数据结构、函数和文件](/Python/Python数据分析/CH03-Python的数据结构、函数和文件.md)
  * [4 - NumPy 基础：数组和矢量计算](/Python/Python数据分析/CH04-NumPy基础：数组和矢量计算.md)
  * [5 - Pandas 入门](/Python/Python数据分析/CH05-pandas入门.md)
  * [6 - 数据加载、存储与文件格式](/Python/Python数据分析/CH06-数据加载、存储与文件格式.md)
  * [7 - 数据清洗和准备](/Python/Python数据分析/CH07-数据清洗和准备.md)
  * [8 - 数据规整：聚合、合并和重塑](/Python/Python数据分析/CH08-数据规整：聚合、合并和重塑.md)
  * [9 - 绘图和可视化](/Python/Python数据分析/CH09-绘图和可视化.md)
  * [10 - 数据聚合与分组运算](/Python/Python数据分析/CH10-数据聚合与分组运算.md)
  * [11 - 时间序列](/Python/Python数据分析/CH11-时间序列.md)
  * [12 - pandas高级应用](/Python/Python数据分析/CH12-pandas高级应用.md)
  * [13 - Python 建模库介绍](/Python/Python数据分析/CH13-Python建模库介绍.md)
  * [14 - 数据分析案例](/Python/Python数据分析/CH14-数据分析案例.md)
  * [附录 A：NumPy 高级应用](/Python/Python数据分析/附录A-NumPy高级应用.md)
  * [附录 B：更多关于 IPython 的内容](/Python/Python数据分析/附录B-更多关于IPython的内容.md)



## 🥭 Golang

?> 🔊 本部分由 [苍茫误此生](https://cangmang.xyz) 提供，感谢博主的分享 🐝

* **【一】Go 语言基础篇**
  * [1 - Go 语言介绍与安装](Golang/Golang入门笔记-CH01-Go语言介绍与安装.md)
  * [2 - Go 语言基本语法和结构](Golang/Golang入门笔记-CH02-Go语言基本语法和结构.md)
  * [3 - Go 语言基本数据类型](/Golang/Golang入门笔记-CH03-Go语言基本数据类型.md)
  * [4 - Go 语言流程控制](/Golang/Golang入门笔记-CH04-Go语言流程控制.md)
  * [5 - Go 数组和切片](/Golang/Golang入门笔记-CH05-数组和切片.md)
  * [6 - Map](/Golang/Golang入门笔记-CH06-Map.md)
  * [7 - 结构体和方法](/Golang/Golang入门笔记-CH07-结构体和方法.md)
  * [8 - 接口](/Golang/Golang入门笔记-CH08-接口.md)
  * [9 - 反射](/Golang/Golang入门笔记-CH09-反射.md)
  * [10 - 函数高级特性](/Golang/Golang入门笔记-CH10-函数高级特性.md)
  * [11 - 错误处理](/Golang/Golang入门笔记-CH11-错误处理.md)
  * [12 - 并发](/Golang/Golang入门笔记-CH12-并发.md)



# 🚀 计算机基础

## ⏳ 算法与数据结构

* [算法与数据结构学习笔记（C 语言版）](计算机专业课/算法与数据结构/算法与数据结构笔记.md)

## 📜 数据库

* [数据库原理及应用学习笔记](计算机专业课/数据库/数据库原理及应用.md)



## 🐼 数理统计

* [1 - 抽样和抽样分布](/计算机专业课/数理统计/CH01-抽样和抽样分布.md)



## 📘 微机原理

* [微机原理学习笔记](计算机专业课/微机原理/微机原理笔记.md)

## ⏰ 计算机组成原理

* [【零】参考资料](计算机专业课/计算机组成原理/参考资料.md)
* [【一】计算机系统概论](计算机专业课/计算机组成原理/一、计算机系统概论.md)



## 🔋 软件工程

* [软件工程学习笔记](计算机专业课/软件工程/软件工程学习笔记.md)

## 💭 设计模式

* [【零】参考资料](计算机专业课/设计模式/参考资料.md)
* [【一】介绍](计算机专业课/设计模式/一、介绍.md)
* [【二】面向对象设计原则](计算机专业课/设计模式/二、面向对象设计原则.md)



# 🐋 刷题 OJ

* [🍉 刷题在行动，加油！](OJ/README.md)



# 🥼 前端学习

## 🥉 Vue.js

* [IDEA 搭建 Vue 项目 Demo](FrontEnd/Vue/idea-to-vue.md)
* [Vue.js 基础入门笔记](FrontEnd/Vue/vue-base-notes.md)

# 📝   面试有招

> 一场生死较量，努力展示自己的才能的时刻！

* 技术面试

  * 来这里看看：[🚀 技术面试题](interview/README.md)

* 研究生复试
  * [🌼 英语面试和口语](PostgraduateExam/english-interview-speaking.md)  >>  考核形式：听力 + 面试
  * [🔨 计算机专业面试](PostgraduateExam/计算机专业面试.md)  >>  考核形式：笔试 + 面试，考察专业基础课和专业技能
  * [👀 计算机专业英语](PostgraduateExam/计算机专业英语.md)  >>  考查计算机专业英语，考查形式：翻译计算机行业前景文章（相关，看具体情况）
  * 笔试是 C 高级程序设计，可以看看这个：[💎](C/高级程序设计复试笔试准备题.md)



# 🎨 论文投稿

* [🎉 分享免费下载论文的网站](ToolBox/ShareToFreeDownloadPapers.md)
* [🎉 分享如何在论文中画漂亮的插图](ToolBox/分享如何在论文中画插图.md)
* [🎉 LaTex 语法使用指南](ToolBox/LaTex使用指南.md)
* [🎉 如何在期刊上发表论文](论文方面/如何在期刊上发表论文.md)
* [🎉 SCI 论文写作攻略](论文方面/SCI论文写作攻略.md)
* [🎉 EndNote 使用教程](/论文方面/EndNote使用教程.md)

## 📑 论文期刊阅读

* [🎉 文献期刊阅读平台推荐](ToolBox/ShareToFreeDownloadPapers.md)
  * [【一】知网了解行业趋势](https://www.cnki.net/)
  * [【二】计算机研究与发展](http://crad.ict.ac.cn/CN/1000-1239/home.shtml)
  * [【三】上海研发公共服务平台](http://www.sstir.cn/)
  * [【四】论文阅读平台个人推荐版](ToolBox/ShareToFreeDownloadPapers.md)
  * [【五】科学知识平台（X - MOL）](https://www.x-mol.com/)
  * [【六】安全内参期刊阅读](https://www.secrss.com/)
* [🎉 论文阅读计划 - 每周阅读总结系列](https://wugenqiang.github.io/PaperSummary/)



# 🐝 生物信息学

* **【一】生信基础**
  * [0 - 生信入门提纲](https://share.mubu.com/doc/3vY2uU7ad0q)
  * [1 - 生物化学学习笔记](https://share.mubu.com/doc/2-jteI75xgq)
  * [4 - 生物信息学学习笔记](https://share.mubu.com/doc/eGqzCLtQ0q)
* **【二】文献阅读**



# 🔨 工具 CooL

* [🔨 ToolBox 实用工具库](ToolBox/Tools.md)
* [🔨 写博客必备软件推荐](ToolBox/写博客必备神器.md)
* [🔨 Latex 语法使用指南](ToolBox/LaTex使用指南)



# 🎅 赞赏作者

我能想到最浪漫的事，就是我喝咖啡你付钱~😆😏 ❤️ 打赏地址：[https://wugenqiang.js.org/sponsor/](https://wugenqiang.js.org/sponsor/)

<div ><img src="https://wugenqiang.gitee.io/notebook/images/pay/wechat-pay.png" width="200" height="200" /></div>
```

### 说明

`[]`内填写页面展示的名称，`()`内填写跳转路径`xxx/xxx/xxx.md`。

本地写好的.md笔记上传到Github后，页面会自动渲染更新。

# 4 感谢

见过的相当不错的GitHub Pages页面了，简约而不失美观，感谢大佬！

[ETS' NoteBook - By Mr.Wu - 微信公众号：码客趣分享 🌹](https://notebook.js.org/#/README)