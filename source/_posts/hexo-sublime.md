---
title: sublime3编写hexo
date: 2018-07-18 23:31:04
tags:
    - hexo
    - sublime 3
    - markdown
    - plugins
categories: hexo
---

# 插件安装
## 安装MarkdownEditing
ctrl+shift+p唤出sublime控制台，输入“package install”，再输入“markdown ed”点击安装MarkdownEditing，插件需要重启才会生效

除了高亮以外,插件还提供插入图片，超链接等功能
快捷键
插入图片：win+shift+k
插入链接：win+ctrl+k

并且提供了几个"code snippet"（代码段）

mdi+tab
```
![Alt text](/path/to/img.jpg "Optional title")
```
mdl+tab
``` 
[contents](link)
```
## 安装hexo插件用于图片上传和显示
1 把主页配置文件_config.yml 里的post_asset_folder:这个选项设置为true

2 在你的hexo目录下执行这样一句话
```
$ npm install hexo-asset-image --save
```
这是下载安装一个可以上传本地图片的插件，来自dalao：[dalao的git](https://github.com/CodeFalling/hexo-asset-image)

3 等待一小段时间后，再运行hexo n "xxxx"来生成md博文时，/source/_posts文件夹内除了xxxx.md文件还有一个同名的文件夹
4 最后在xxxx.md中想引入图片时，先把图片复制到xxxx这个文件夹中，然后只需要在xxxx.md中按照markdown的格式引入图片：
```
![你想输入的替代文字](xxxx/图片名.jpg)
```
注意： xxxx是这个md文件的名字，也是同名文件夹的名字。只需要有文件夹名字即可，不需要有什么绝对路径。你想引入的图片就只需要放入xxxx这个文件夹内就好了，很像引用相对路径。

5 最后检查一下，
``` 
$ hexo g
```
生成页面后，进入public\2017\02\26\index.html文件中查看相关字段，可以发现，html标签内的语句是
```
<img src="2017/02/26/xxxx/图片名.jpg">
```
，而不是
```
<img src="xxxx/图片名.jpg">
```
这很重要，关乎你的网页是否可以真正加载你想插入的图片。

## 安装OmniMarkupPreviewer
同样使用ctrl+shift+p package install 安装OmniMarkupPreviewer
重启后生效，可以预览和生成html

可以在Prefernces->key bindings 中设置alt+m快捷键，快速预览
```
{ "keys": ["alt+m"], "command": "markdown_preview", "args": {"target": "browser", "parser":"markdown"} }
```
