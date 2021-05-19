---
layout: post
title: 从 HTMLAudioElement 中获取 Audio Track
author: poplark
tags: HTMLAudioElement AudioTrack
excerpt: 不同浏览器获取 AudioTrack 的方式
---

## 问题来源

创建 Audio 对象后，希望从中拿到音频，并用于其他地方，如通过 WebRTC 推到服务器，等等。

## 不同浏览器上的获取方式不同

### 1. 大部分浏览器

通过 audio 的 `captureStream` API 来获取到 MediaStream，进而获取到 AudioTrack，如：

```js
const audio = new Audio(xxx.MP3);
audio.addEventLisenter('loadeddata', () => {
  console.log(audio.captureStream().getAudioTracks());
});
```

通过查看 [captureStream](https://caniuse.com/?search=captureStream)，可以发现这种方式适用于大部分浏览器。

### 2. Firefox

Firefox 也类似，但 `captureStream` API 名称需要加前缀 `moz`，即 `mozCaptureStream`;

```js
const audio = new Audio(xxx.MP3);
audio.addEventLisenter('loadeddata', () => {
  console.log(audio.mozCaptureStream().getAudioTracks());
});
```

### 3. Safari（ macOS 及 iOS ）及老的 Edge 浏览器（非 chromium 内核）

这个比较特殊，audio 对象是可以直接拿到 audioTracks，

```js
const audio = new Audio(xxx.MP3);
audio.addEventLisenter('loadeddata', () => {
  console.log(audio.audioTracks);
});
```

注：不需要播放，只需要在 loadeddata 事件触发后，就可以拿到音轨。
