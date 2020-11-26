---
layout: post
title: VideoTrack 的约束条件
author: poplark
tags: typescript plugin
excerpt: 为啥 VideoTrack 的约束条件不生效
---

## 为啥 VideoTrack 的约束条件不生效？

一般PC上摄像头的分辨率都能支持到 720P，我们创建一条 720P 的视频流，并拿到视频轨道，可以用以下代码实现：

```typescript
let vt: MediaStreamTrack;

const constraints: MediaStreamConstraints = {
  video: {
    width: 1280,
    height: 720,
    frameRate: 20
  }
}

navigator
  .mediaDevices
  .getUserMedia(constraints)
  .then((stream) => {
    vt = stream.getVideoTracks()[0];
  })
  .catch((err) => {
    console.log(`创建流失败`);
  });
```

一切正常的情况下，如果想改变这条视频轨道的分辨率，可以这样实现：

```typescript
vt.applyConstraints({width: 640, height: 360, frameRate: 20});
```

同样的，**一般情况下**，如果刚开始创建时使用的是 360P 的分辨率，后面改成 720P 的，这样也可以：

```typescript
vt.applyConstraints({width: 1280, height: 720, frameRate: 20});
```

这里也说了，是**一般情况下**，这个**一般情况**是限制在没有其他流占用摄像头。

有这样一个场景，`页面中展示摄像头的画面，假设这个画面对应的流是 A，另有个设置功能，譬如设置弹出框，里面可以调节摄像头分辨率、多个摄像头时还可以切换，那么这里需要有个预览流，假设是 B`，在这种场景下，如果创建 A 时视频使用的约束条件为 `{ width: 640, height: 360, frameRate: 20 }` 的话，在创建 B 时，如果使用的约束条件为 `{width: 1280, height: 720, frameRate: 20}`，那么这时候，你将会发现 B 的视频的分辨率并不会是 `720P` 的，这就出现了 **VideoTrack 的约束条件不生效** 的现象。

上面的场景再调整一下，假设创建 A 时视频使用的约束条件为 `{ width: 640, height: 360, frameRate: 20 }` 的话，在创建 B 时使用的约束条件也为 `{width: 640, height: 360, frameRate: 20}`，那么假如你现在想改变 A 的分辨率 `a.applyConstraints({width: 1280, height: 720, frameRate: 20})`，这时候，你将会发现 A 的视频的分辨率也不会是 `720P` 的，这也出现了 **VideoTrack 的约束条件不生效** 的现象。

简单解释一下，如果已经有流按某一约束条件访问了摄像头，那么后面再创建流时，将受第一条流的约束条件影响。而且如果摄像头并不为一条流独占（整个浏览器没有创建其他流，不单单是同一个页面内），它将无法重启摄像头，进而**有可能**无法改变分辨率之类的设置。这里的**有可能**会发生在分辨率从小改成大，而从大改到小则没影响，我理解这类似于软解，不重启摄像头，直接软件处理降低了分辨率。
