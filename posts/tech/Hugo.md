---
title : 'Hugo配置与使用'
date : 2024-08-03T22:30:13+08:00
lastmod: 2024-09-01T22:20:13+08:00
description : "Hugo配置与使用" 
image : img/cat.jpg
draft : false    
categories : ["Hugo"]
tags : ["Hugo"]
# password : leetcode
---

# Hugo配置与使用

下载PaperMod主题：

```bash
git clone https://github.com/adityatelange/hugo-PaperMod themes/PaperMod --depth=1
```

生成public内容时清空文件夹

```
hugo -F --cleanDestinationDir
```

新建post

```bash
hugo new content posts/my-first-post.md
```

完成Git push流程

```bash
echo "# showguan.github.io" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/showguan/showguan.github.io.git
git push -u origin main
```

```bash
git remote add origin https://github.com/showguan/showguan.github.io.git
git branch -M main
git push -u origin main
```

强制`push`到仓库

```bash
git push -f -u origin main
```

```
git remote add origin <远程代码库的URL>
```

```bash
第六步：在上面创建的github文件中，放入要上传的代码，然后按照上面的指令开始操作，执行指令

1、git init      （建立本地仓库）

2、git add  *  (将代码添加到本地仓库，《*是添加全部代码，代码全部更新》)

3、git commit -m "first commit"  (提交到本地缓冲，《引号里说明提交了什么东西，说白了就是注释》）
```

## Git代理管理

```bash
//http || https
git config --global http.proxy 127.0.0.1:10809
git config --global https.proxy 127.0.0.1:10809

//sock5代理
git config --global http.proxy socks5 127.0.0.1:10808
git config --global https.proxy socks5 127.0.0.1:10808

```

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

## 最终的原因：

它控制着在提交和检出文件时是否自动地将行结束符转换为不同的格式。

```bash
git config --global core.autocrlf false
```

## Front Matter

```yaml
---
title: "我的第一篇文章"
subtitle: ""
date: 2020-03-04T15:58:26+08:00
lastmod: 2020-03-04T15:58:26+08:00
draft: true
author: ""
authorLink: ""
description: ""
license: ""

tags: []
categories: []
hiddenFromHomePage: false

featured_image: ""
featured_image_preview: ""

toc: false
autoCollapseToc: true
math: true
mapbox:
    accessToken: ""
    lightStyle: ""
    darkStyle: ""
    navigation: true
    geolocate: true
    scale: true
    fullscreen: true
lightgallery: true
linkToMarkdown: true
share:
  enable: true
comment: true
---
```

```yaml
---
title: "My 1st post"
date: 2020-09-15T11:30:03+00:00
# weight: 1
# aliases: ["/first"]
tags: ["first"]
author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Desc Text."
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/<path_to_repo>/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---
```



## 不蒜子流量统计

```
<script async src="https://cdn.dusays.com/bsz.js"></script>
本文总阅读量 <span id="busuanzi_value_page_pv"></span> 次
本文总访客量 <span id="busuanzi_value_page_uv"></span> 人
本站总访问量 <span id="busuanzi_value_site_pv"></span> 次
本站总访客数 <span id="busuanzi_value_site_uv"></span> 人
```

## config.yaml

```yaml
baseURL: https://kyxie.github.io/
languageCode: en-ca # zh-cn
title: Kunyang's Blog
theme: PaperMod

enableInlineShortcodes: true
enableEmoji: true # allowing using emoji
enableRobotsTXT: true

hasCJKLanguage: true

buildDrafts: false
buildFuture: false
buildExpired: false

#googleAnalytics: UA-123-45

paginate: 15 # articles per page

minify:
    disableXML: true
    # minifyOutput: true

permalinks:
    post: "/:title/"
    # post: "/:year/:month/:day/:title/"

defaultContentLanguage: en # prefer language
defaultContentLanguageInSubdir: true

languages:
    en:
        languageName: "English🇨🇦"
        contentDir: content/English
        weight: 1
        profileMode:
            enabled: true
            title: Kyrie Xie
            subtitle: "Welcome to my site!"
            # imageUrl: "Avatar.png"
            imageUrl: "Selfie.png"
            imageTitle: Selfie
            imageWidth: 150
            imageHeight: 150
            buttons:
                - name: About
                  url: about
                - name: Project
                  url: projects
                - name: Resume
                  url: resume.pdf

        menu:
            main:
                - identifier: About
                  name: About
                  url: about
                  weight: 1
                - identifier: Projects
                  name: Project
                  url: projects
                  weight: 2
                - identifier: Resume
                  name: Resume
                  url: resume.pdf
                  weight: 3
                - identifier: Blog
                  name: Blogs
                  url: blog
                  weight: 4
                - identifier: Search
                  name: Search
                  url: search
                  weight: 5

    zh:
        languageName: "中文🇨🇳"
        contentDir: content/Chinese
        weight: 2
        profileMode:
            enabled: true
            title: 谢坤洋
            subtitle: "欢迎来到我的网站！"
            # imageUrl: "Avatar.png"
            imageUrl: "Selfie.png"
            imageTitle: Selfie
            imageWidth: 150
            imageHeight: 150
            buttons:
                - name: 关于
                  url: about
                - name: 博客
                  url: blog
                - name: 简历
                  url: resume.pdf

        menu:
            main:
                - identifier: About
                  name: 关于
                  url: about
                  weight: 1
                - identifier: Projects
                  name: 项目
                  url: projects
                  weight: 2
                - identifier: Resume
                  name: 简历
                  url: resume.pdf
                  weight: 3
                - identifier: Blog
                  name: 博客
                  url: blog
                  weight: 4
                - identifier: Comments
                  name: 留言板
                  url: comment/comments
                  weight: 5
                - identifier: Links
                  name: 友链
                  url: links
                  weight: 6
                - identifier: Search
                  name: 搜索
                  url: search
                  weight: 7

outputs:
    home:
        - HTML
        - RSS
        - JSON

params:
    env: production # to enable google analytics, opengraph, twitter-cards and schema.
    title: Kyrie
    author: Kyrie

    defaultTheme: dark # defaultTheme: light or dark
    disableThemeToggle: false
    DateFormat: "2006-01-02"
    ShowShareButtons: true
    ShowReadingTime: true
    # disableSpecialistPost: true
    disableScrollToTop: false
    displayFullLangName: true
    ShowPostNavLinks: true
    ShowBreadCrumbs: true
    ShowCodeCopyButtons: true
    hideFooter: false # hide foot
    ShowWordCounts: true
    VisitCount: true

    ShowLastMod: true

    ShowToc: true
    TocOpen: true

    comments: true

    socialIcons:
        - name: instagram
          url: "https://www.instagram.com/_kyxie/"
        - name: github
          url: "https://github.com/kyxie"
        - name: linkedin
          url: "https://www.linkedin.com/in/kunyang-kyrie-xie-557270194/"
        # - name: qq
        #   url: "qq.jpg"
        - name: wechat
          url: "wechat.jpg"
        - name: weibo
          url: "https://weibo.com/u/5687393723"
        - name: bilibili
          url: "https://space.bilibili.com/156752850?spm_id_from=333.1007.0.0"
        - name: strava
          url: "https://www.strava.com/athletes/62343657"
        - name: email
          url: "mailto:kyriexie@outlook.com"

    assets:
        favicon: "k-48.png"
        favicon16x16: "k-48.png"
        favicon32x32: "k-48.png"
        apple_touch_icon: "k-48.png"
        safari_pinned_tab: "k-48.png"

    label:
        text: "Home"

    fuseOpts:
        isCaseSensitive: false
        shouldSort: true
        location: 0
        distance: 1000
        threshold: 1
        minMatchCharLength: 0
        keys: ["title", "permalink", "summary"]

    twikoo:
        version: 1.4.11

taxonomies:
    category: categories
    tag: tags
    series: series

markup:
    goldmark:
        renderer:
            unsafe: true
    highlight:
        # anchorLineNos: true
        codeFences: true
        guessSyntax: true
        lineNos: true
        # noClasses: false
        style: monokai
        lineNos: false

privacy:
    vimeo:
        disabled: false
        simple: true

    twitter:
        disabled: false
        enableDNT: true
        simple: true

    instagram:
        disabled: false
        simple: true

    youtube:
        disabled: false
        privacyEnhanced: true

services:
    instagram:
        disableInlineCSS: true
    twitter:
        disableInlineCSS: true

```

```yaml
languages:
    en:
      languageName: "English"
      # contentDir: content/english
      weight: 1
      profileMode:
        enabled: true
        title: (〃'▽'〃)
        subtitle: "🧨学而时习之，不亦说乎？有朋自远方来，不亦乐乎？<br/>👏🏼欢迎光临素履(Sulv)的博客<br/>👇联系方式"
        imageUrl: "img/Q.gif" #图片放在static/img/Q.gif
        imageTitle: 
        imageWidth: 150
        imageHeight: 150
        buttons:
          - name: 👨🏻‍💻技术
            url: posts/tech
          - name: 📕阅读
            url: posts/read
          - name: 🏖生活
            url: posts/life
      menu:
        main:
          - identifier: search
            name: 🔍搜索
            url: search
            weight: 1
          - identifier: home
            name: 🏠主页
            url: /
            weight: 2
          - identifier: posts
            name: 📚文章
            url: posts
            weight: 3
          - identifier: archives
            name: ⏱时间轴
            url: archives/
            weight: 20
          - identifier: tags
            name: 🔖标签
            url: tags
            weight: 40
          - identifier: about
            name: 🙋🏻‍♂️关于
            url: about
            weight: 50
          - identifier: links
            name: 🤝友链
            url: links
            weight: 60
```

```yaml
enableInlineShortcodes: true #允许内联短码
enableEmoji: true # 允许使用 Emoji 表情，建议 true
enableRobotsTXT: true # 允许爬虫抓取到搜索引擎，建议 true

hasCJKLanguage: true # 自动检测是否包含 中文日文韩文 如果文章中使用了很多中文引号的话可以开启

buildDrafts: false
buildFuture: false
buildExpired: false

#googleAnalytics: UA-123-45 # 谷歌统计
# Copyright: Sulv

paginate: 15    # 每页显示的文章数

minify:
    disableXML: true
    # minifyOutput: true

permalinks: #浏览器链接显示方式
  post: "/:title/"
  # post: "/:year/:month/:day/:title/"

defaultContentLanguage: en # 最顶部首先展示的语言页面
defaultContentLanguageInSubdir: true

outputs:
    home:
        - HTML
        - RSS
        - JSON

params:
    env: production # to enable google analytics, opengraph, twitter-cards and schema.
    # description: "这是一个纯粹的博客......"
    author: Sulv
    # author: ["Me", "You"] # multiple authors
    defaultTheme: auto  # defaultTheme: light or  dark 
    disableThemeToggle: false
    DateFormat: "2006-01-02"
    ShowShareButtons: true
    ShowReadingTime: true
    # disableSpecialistPost: true
    displayFullLangName: true
    ShowPostNavLinks: true
    ShowBreadCrumbs: true
    ShowCodeCopyButtons: true
    hideFooter: false # 隐藏页脚
    ShowWordCounts: true
    VisitCount: true

    ShowLastMod: true #显示文章更新时间

    ShowToc: true # 显示目录
    TocOpen: true # 自动展开目录

    comments: true
    
    socialIcons:
        - name: github
          url: "https://github.com/xyming108"
        - name: twitter
          url:  "img/twitter.png"
        - name: facebook
          url: "https://www.facebook.com/profile.php?id=100027782410997"
        - name: instagram
          url: "img/instagram.png"
        - name: QQ
          url: "img/qq.png"
        - name: WeChat
          url: "img/wechat.png"
        - name: email
          url: "mailto:1931559710@qq.com"
        - name: RSS
          url: "index.xml"

    # editPost:
    #     URL: "https://github.com/adityatelange/hugo-PaperMod/tree/exampleSite/content"
    #     Text: "Suggest Changes" # edit text
    #     appendFilePath: true # to append file path to Edit link

    label:
      text: "Sulv's Blog"
      #icon: "img/Q.gif"
      icon: "https://www.sulvblog.cn/Q.gif"
      iconHeight: 35

    # analytics:  #谷歌分析
    #     google:
    #         SiteVerificationTag: "XYZabc"

    assets:
        favicon: "img/Q.gif"
        favicon16x16: "img/Q.gif"
        favicon32x32: "img/Q.gif"
        apple_touch_icon: "Q.gif"
        safari_pinned_tab: "Q.gif"

    # cover:
    #     hidden: true # hide everywhere but not in structured data
    #     hiddenInList: true # hide on list pages and home
    #     hiddenInSingle: true # hide on single page

    fuseOpts:
        isCaseSensitive: false
        shouldSort: true
        location: 0
        distance: 1000
        threshold: 1
        minMatchCharLength: 0
        keys: ["title", "permalink", "summary"]

    twikoo:
      version: 1.4.11

taxonomies:
    category: categories
    tag: tags
    series: series

markup:
    goldmark:
        renderer:
            unsafe: true # HUGO 默认转义 Markdown 文件中的 HTML 代码，如需开启的话
    highlight:
        # anchorLineNos: true
        codeFences: true  
        guessSyntax: true
        lineNos: true
        # noClasses: false
        # style: monokai
        style: darcula

        # codeFences：代码围栏功能，这个功能一般都要设为 true 的，不然很难看，就是干巴巴的-代码文字，没有颜色。
        # guessSyntax：猜测语法，这个功能建议设置为 true, 如果你没有设置要显示的语言则会自动匹配。
        # hl_Lines：高亮的行号，一般这个不设置，因为每个代码块我们可能希望让高亮的地方不一样。
        # lineNoStart：行号从编号几开始，一般从 1 开始。
        # lineNos：是否显示行号，我比较喜欢显示，所以我设置的为 true.
        # lineNumbersInTable：使用表来格式化行号和代码,而不是 标签。这个属性一般设置为 true.
        # noClasses：使用 class 标签，而不是内嵌的内联样式

privacy:
    vimeo:
        disabled: false
        simple: true

    twitter:
        disabled: false
        enableDNT: true
        simple: true

    instagram:
        disabled: false
        simple: true

    youtube:
        disabled: false
        privacyEnhanced: true

services:
    instagram:
        disableInlineCSS: true
    twitter:
        disableInlineCSS: true
```





## 完整发布流程

```shell
hugo -F --cleanDestinationDir
```

将备份文件夹内的文件复制到发布文件夹内

```shell
Copy-Item -Path AddFile -Destination ../kennems/public -Recurse
```

将备份文件复制到发布文件夹内：

```shell
Copy-Item -Path "AddFile\*" -Destination "." -Recurse

```

## post文件夹

这里是
主页 -》📚文章 下面的分类

![image-20240830150302697](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20240830150302697.png)

## 自定义CSS

![image-20240830162346488](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20240830162346488.png)

这个文件夹放置各种自定义CSS样式，分开放不会有优先级问题。

## 配置封面图

```yaml
cover: 
    image: img/cat.jpg
    # 设置图片的小标题
    alt: "图片"
    #caption: "图片"
    hidden: true        # 文章页面隐藏封面图片
```

## 自定义底部categories

在 `layouts/_default/single.html` 或 `layouts/_default/_markup/render-link.html` 中自定义底部分类显示。

使用 CSS 自定义样式：

```css
.post-categories {
    margin-top: 1rem;
    padding: 0.5rem 1rem;
    background: var(--entry);
    border-radius: 8px;
}

.post-categories a {
    margin-right: 0.5rem;
    padding: 0.25rem 0.75rem;
    background: var(--tertiary);
    border-radius: 4px;
    font-size: 0.875rem;
    color: var(--secondary);
    text-decoration: none;
}

.post-categories a:hover {
    background: var(--highlight);
    color: var(--primary);
}
```

在 `config.yaml` 中配置分类显示：

```yaml
params:
    ShowCategories: true
    ShowCategoryCount: true
```
