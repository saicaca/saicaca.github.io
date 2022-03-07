---
title: 在油管 VTuber 录播上回放B站转播弹幕的油猴脚本
tags:
  - JavaScript
  - 前端
id: '43'
categories:
  - - uncategorized
date: 2020-06-14 21:07:20
---

最近习惯在作业时挂个管人打游戏的录播在旁边当背景。又不想放熟肉，因为觉得熟肉不专注地看就没什么意义；但由于日语水平 N95，放生肉的话听不懂，油管弹幕也看不懂又感到些许寂寞。于是有了把B站转播的弹幕带到油管上回放的想法 ，这种奇怪的需求怕不是全世界独一份，就写了这么个脚本。

## 当前功能

![](https://zroyi.files.wordpress.com/2020/06/image.png?w=899)

当前的功能很简单，就是把弹幕打到视频上。

![](https://zroyi.files.wordpress.com/2020/06/image-1.png?w=245)

开启的方法是点击播放器右下方的 DANMAKU 按钮，点击后会查找下载并显示当时B站转播的弹幕。

![](https://zroyi.files.wordpress.com/2020/06/image-3.png?w=101)

成功的话按钮会变成控制延迟的步进器，考虑到转播可能会有长短不一的延迟，可能需要针对具体情况进行微调。

## 实现

这东西是边学 JS 边写的，有些实现可能非常蠢。

### 数据来源

写这个脚本的第一个问题是弹幕数据哪里来。一开始还打算自己在服务器上录的，后来才知道早就有大佬在录了：[bilibili-vtuber-danmaku](github.com/dd-center/bilibili-vtuber-danmaku)。

于是问题还剩两个：根据油管频道ID获取B站的直播间ID、获取到某次直播的具体开始时间以便对准弹幕发送时间。

前者的解决方案是根据 [vdb.vtbs.moe](https://vdb.vtbs.moe/) 的数据创建油管频道ID到B站空间ID的字典，再调用B站的 API 获取直播间ID。后者我本来以为能在视频播放页面直接获取到，但发现油管实在太抠，只提供了日期，而没有具体到几时几分。尝试过用油管官方的 API 获取视频元数据，里面有「发布时间」这一项，但发现也对不上，猜测是直播结束 + 完成转码的时间。最后发现 [hiyoko.sonoj.net](https://hiyoko.sonoj.net/) 有，就用了。

### 弹幕

因为只需要简单的弹幕显示所以随便写了一下，用 transition 实现弹幕的滚动，过于简陋，连防重叠什么的都没有，以后有空再改。

读取时先采用二分查找在一天的弹幕文件中找到位置，一次读入一百条并设定定时发送，快发完了就读下一批。若监听到暂停、拖进度条的事件就清空定时任务，重新读取。

有个问题卡了我很久。本来当用户从一个视频点进另一个视频时脚本应该重新加载，但可能由于油管的实现比较高级，油猴不认为页面发生了跳转。尝试用监听 `popstate` 事件之类的方法也失败了。最后用一个歪门邪道解决了问题——监听网页 title 的变化（因为切换视频 title 一定会变）。

```
let titleEl = document.getElementsByTagName("title")[0];
let docEl = document.documentElement;

if (docEl &amp;&amp; docEl.addEventListener) {
    docEl.addEventListener("DOMSubtreeModified", function(evt) {
        let t = evt.target;
        if (t === titleEl  (t.parentNode &amp;&amp; t.parentNode === titleEl)) {
            initUi();
        }
    }, false);
} else {
    document.onpropertychange = function() {
        if (window.event.propertyName == "title") {
            initUi();
        }
    };
}
```

不过网页 Title 变化也不一定是页面切换，也可能是收到了新通知之类的原因。所以再检测一下 url 有没有发生改变，若 url 没变就当无事发生。

```
if (window.location.href == url  window.location.href.indexOf("youtube.com/watch") &lt; 0) {
    return;
}
url = window.location.href;
```

## TODO

\[ \] 弹幕防重叠

\[ \] 同传弹幕显示框

\[ \] 根据播放器宽度调整弹幕速度