---
layout: post
title: iOS--更新Xcode后Alcartraz不兼容的解决办法
excerpt: "Alcatraz兼容新版Xcode方法"
modified: 2015-12-19
tags: [Xcode, Alcartraz]
comments: true
image:
  feature: sample-image-1.jpg
  credit: WeGraphics
  creditlink: http://wegraphics.net/downloads/free-ultimate-blurred-background-pack/
---

<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->
	
平时开发的时候经常会用到Xcode插件，我一直用Alcatraz来管理Xcode的插件，近期Xcode经常更新每次更新完Alcartraz就无法使用。这时可以修改Alcatraz使其兼容新版本。

新版的Xcode无法运行Alcatraz的主要原因是Alcatraz的配置文件中没有新版Xcode的DVTPlugInCompatibilityUUID，只有在Alcatraz的作者兼容了新版本后这个id才会被添加进去，不过在Xcode没有大的改动情况下作为临时方法可以先自己将新版本的id添加进去。

首先要获得新版的Xcode的DVTPlugInCompatibilityUUID，在终端(Terminal)中的输入如下命令：

> defaults read /Applications/Xcode.app/Contents/Info DVTPlugInCompatibilityUUID

随便一提Xcode7.1.1的DVTPlugInCompatibilityUUID是7265231C-39B4-402C-89E1-16167C4CC990。

要修改Alcatraz首先要进入Alcatraz所在的目录，打开Finder，在Finder中选择`前往`，通常情况下资源库是隐藏的这时就需要按住`option`键然后在下面的列表中选中资源库。
Alcartraz所在的目录路径是：

> ~/Library/Application Support/Developer/Shared/Xcode/Plugins

右键选中Alcatraz显示包内容，打开Contents目录下的Info.plist文件，展开其中DVTPlugInCompatibilityUUIDs这个array把之前的DVTPlugInCompatibilityUUID添加进去。最后重启Xcode就搞定了。
