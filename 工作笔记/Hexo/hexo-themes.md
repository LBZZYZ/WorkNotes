---
title: hexo-主题配置
date: 2021-05-26 00:38:08
tags: hexo
categories: hexo
description: 如果你想知道如何对hexo & NexT主题进行个性化配置，看这篇就对了！
---
# 前言
本文以NextT为例介绍如何对hexo主题进行配置，其主题配置文件在```themes\next\_config.yml```中。

# 风格选择
```bash
# ---------------------------------------------------------------
# Scheme Settings
# ---------------------------------------------------------------

# Schemes
#scheme: Muse
#scheme: Mist
#scheme: Pisces
scheme: Gemini
```
这是设置了Mist风格的效果
![theme-mist](https://cdn.staticaly.com/gh/LBZZYZ/PicX@master/Blog/theme-mist.2z3v7co1zfq0.webp)

## 深色模式
目前设置之后无效，还要再确认下原因。
```bash
# Dark Mode
darkmode: true
```

# 菜单设置
```bash
# ---------------------------------------------------------------
# Menu Settings
# ---------------------------------------------------------------

# Usage: `Key: /link/ || icon`
# Key is the name of menu item. If the translation for this item is available, the translated text will be loaded, otherwise the Key name will be used. Key is case-senstive.
# Value before `||` delimiter is the target link, value after `||` delimiter is the name of Font Awesome icon.
# When running the site in a subdirectory (e.g. yoursite.com/blog), remove the leading slash from link value (/archives -> archives).
# External url should start with http:// or https://
menu:
  home: / || fa fa-home
  #about: /about/ || fa fa-user
  #tags: /tags/ || fa fa-tags
  categories: /categories/ || fa fa-th
  archives: /archives/ || fa fa-archive
  #schedule: /schedule/ || fa fa-calendar
  #sitemap: /sitemap.xml || fa fa-sitemap
  #commonweal: /404/ || fa fa-heartbeat

# Enable / Disable menu icons / item badges.
menu_settings:
  icons: true #是否显示小图标
  badges: true #显示tags/categories..的数量
```

## 如何添加一个tag?
1. 创建一个tag页面

```bash
hexo new page "tags"
```
此命令会在source目录下新建一个tags目录，并在tags目录下新建一个index.md。

2. 编辑tags/index.md

```bash
---
title: "tags"
type: tags
layout: "tag"
---
```

注意layout字段，此字段的布局为您当前使用主题下的layout文件，如您在layout下有一个名为tag.swig的文件，则可以配置```layout: "tag"```。

3. 编辑项目的配置文件

```bash
#Directory
tag_dir: tags
```

4. 编辑主题配置文件

```bash
tags: /tags
```

# 侧边栏头像
```bash
avatar:
  url: #/images/avatar.gif
  rounded: false # 如果此项为true，则头像为圆形显示。
  rotated: false # 如果此项为true，当鼠标浮在头像上时，头像会转动。
```

url字段有两种配置方法：

| 方法 | 说明 |
| ---  | --- |
| 网络绝对url | 例如 http://example.com/avatar.png |
|本地图片| 将头像存放在主题文件下的source/images下，配置时可以写url : /images/avatar.jpg |


# 侧边栏社交项
配置方法：关键字: 链接 || 图标
下面是NexT配置文件模板给出的配置实例。
```bash
social:
  #GitHub: https://github.com/yourname || fab fa-github
  #E-Mail: mailto:yourname@gmail.com || fa fa-envelope
  #Weibo: https://weibo.com/yourname || fab fa-weibo
  #Google: https://plus.google.com/yourname || fab fa-google
  #Twitter: https://twitter.com/yourname || fab fa-twitter
  #FB Page: https://www.facebook.com/yourname || fab fa-facebook
  #StackOverflow: https://stackoverflow.com/yourname || fab fa-stack-overflow
  #YouTube: https://youtube.com/yourname || fab fa-youtube
  #Instagram: https://instagram.com/yourname || fab fa-instagram
  #Skype: skype:yourname?call|chat || fab fa-skype

```

NexT还给出了三个额外的social配置项
```bash
social_icons:
  enable: true         #是否显示图标
  icons_only: false    #是否只显示图标，不显示文字
  transition: false    #
```

# 设置站点信息
编辑站点配置文件的Site字段，即```站点根目录/_config.yml```。
```bash
# Site
title: LBZ'blog
subtitle: ''
description: '学而不思则罔，思而不学则殆'
keywords:
author: 李柄志
language: zh-CN
timezone: ''
```

# 鼠标点击动画
https://blog.csdn.net/qq_42889280/article/details/103087564


# 不显示全文内容
首先在主题配置文件中将此项设置为true。
```bash
# Automatically excerpt description in homepage as preamble text.
excerpt_description: true
```

然后有两种方法可以设置部分显示：
1. 在文章表头字段添加description字段，加入后只会显示description字段的内容
2. 在想要显示的内容前加入<!--More-->，这样可以显示该字段之前的全部内容。


[参考链接](https://blog.csdn.net/yueyue200830/article/details/104470646)


# 网站图标

在主题配置文件中配置以下字段即可更新自己的网站图标。

把下载好的图标放到主题目录下面的images目录，并配置相应的文件名即可。

分享两个下载图标的网站：
1. [阿里巴巴矢量图标库](https://www.iconfont.cn/collections/detail?cid=29)。
2. [Font Awesome](https://fontawesome.com/v5.15/icons?d=gallery&p=2)
   
[本站使用的图标库](https://www.iconfont.cn/collections/detail?spm=a313x.7781069.1998910419.d9df05512&cid=30312)

```bash
favicon:
  small: /images/favicon-16x16-next.png               #网站小图标，在标签页显示
  medium: /images/favicon-32x32-next.png              #网站中图标
  apple_touch_icon: /images/apple-touch-icon-next.png #收藏夹显示的图标
  safari_pinned_tab: /images/logo.svg
  #android_manifest: /images/manifest.json
  #ms_browserconfig: /images/browserconfig.xml
```


# 页脚设置
```bash
footer:
  # 指定网站建立的年份，如果此字段为空白，则使用当前年份。
  since: 2020

  # 在年份和copyright之间的图标。
  icon:
    # Icon name in Font Awesome. See: https://fontawesome.com/icons
    name: fa fa-anchor
    # If you want to animate the icon, set it to true.
    animated: true
    # 指定图标颜色。
    color: "#7fb80e"

  # 指定copyright的文字，如果不指定，则使用主题配置文件中的arthur字段。
  copyright: Bingzhi.Li, All rights reserved.

  # Powered by Hexo & NexT
  powered: true

  # 网站备案信息. 查询网址: https://beian.miit.gov.cn, http://www.beian.gov.cn
  beian:
    enable: false
    icp:
    # The digit in the num of gongan beian.
    gongan_id:
    # The full num of gongan beian.
    gongan_num:
    # The icon for gongan beian. See: http://www.beian.gov.cn/portal/download
    gongan_icon_url:
```


