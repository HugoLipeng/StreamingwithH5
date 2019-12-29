# 示例

访问这里查看播放器示例 Demo http://sdk-release.qnsdk.com/qiniu-web-player-examples/basic.html

# 引入
将 `qplayer-web-player.js` 直接引入您的站点即可, 下载地址：https://sdk-release.qnsdk.com/qiniu-web-player-1.0.0.js

> iOS 端 UC 等浏览器因为其自身魔改了浏览器标准的 video 实现，在使用的过程中可能出现无法预期的情况，如果需要兼容此类浏览器，建议开发者单独定制开发。

# 基本使用

## 创建一个播放器

只要指定资源的播放地址和页面上的一个元素，SDK 就会自动在这个元素下面创建相应的播放器组件，目前支持的资源格式是 `mp4`、`webm` 和 `m3u8`。

```javascript
const player = new QPlayer({
  url: "http://xxx.xxxx.xxx.m3u8",
  container: document.getElementById("xxx"),
});
```

其中构造参数的完整的配置见下

```typescript
QPlayerConfig {
  url: string; // 视频源，目前支持 mp4、webm 和 m3u8
  container: HTMLElement; // 一个 HTML 元素作为播放器的容器，播放器默认使用容器的宽高
  autoplay?: boolean; // 是否自动播放
  muted?: boolean; // 是否静音
  poster?: string; // 封面图片
  hls?: {
    DRMKey?: Uint8Array; // 七牛 HLS DRM 加密密钥
  };
  isLive?: boolean; // 是否是直播流, 这个开启后播放器将不会渲染进度条和时间
  loggerLevel?: number; // log 等级，0 info，1 debug， 2 只看错误，3 关闭 log，默认为 1
}
```

> 关于自动播放的注意事项：目前主流浏览器（Firefox/Chrome/Safari）都有各种规则禁止页面在没有用户交互前提下自动播放有声的媒体资源，所以即使这里设置了自动播放，也不能保证 100% 自动播放成功。关于自动播放的细节可以参考这篇 Chrome 的对外声明 https://developers.google.com/web/updates/2017/09/autoplay-policy-changes

## 播放器对象成员

### player.currentTime
当前播放的时间，单位是秒

### player.totalTime
视频总时长，在 ready 事件触发前这个值都是 0，单位是秒

### player.isPlay
当前是否在播放状态

### player.isPause
当前是否在暂停状态

### player.isEnded
当前是否在结束状态

### player.isMuted
当前是静音状态

### player.volume
当前的音量，范围 0-1

### player.isLoop
当前是否开启了循环播放

## 播放器对象方法
### player.play()
播放视频源

### player.pause()
暂停视频源

### player.seek(time)
跳转到指定的时间点 `time`，`time` 的单位是秒

### player.toggleFullscreen()
切换到全屏/非全屏模式

### player.toggleMute()
切换静音状态

### player.toggleLoop()
切换循环播放状态

### player.setVolume(value)
设置音量， value 需要在 0 - 1 之间

### player.setPlaybackRate(rate)
倍速播放，rate 为指定的倍率，需要 > 0

### player.destory()
释放当前播放器

# 进阶功能
## 播放器事件
SDK 基于 [events](https://github.com/Gozala/events) 库实现了事件模型，通过以下方法注册绑定事件

```javascript
// 注册 ready 的监听函数
player.on("ready", handleReady);

// 注册 ready 的监听函数，但是只会触发一次
player.once("ready", handleReady);

// 移除 ready 特定的监听函数
player.off("ready", handleReady);

// 移除 ready 的所有监听函数
myRoom.removeAllListeners("ready");
```

|事件名称|事件介绍|事件参数|参数说明|
|----|----|----|----|
|ready|播放器准备完毕，可以播放|()|无|
|fullscreenchange|全屏状态改变|(isFullScreen: boolean)|是否全屏|
|error|播放过程中出现错误|(e)|错误对象，包含了错误代码|
|play|播放器开始播放|()|无|
|pause|播放器暂停|()|无|
|ended|播放结束|()|无|
|timeupdate|播放时间更新|(currentTime: number, totalTime: number)|分别为当前播放时间和总时间|
|durationchange|总播放时间更新|(totalTime: number)|总时间|
|loading|正在加载|()|无|
|seeked|播放器跳转完成|()|无|
|volumechange|播放器音量改或者 mute 状态改变|(volume: number, isMuted: boolean)|分别是当前音量和是否静音|

## 多码率媒体资源
SDK 允许用户同时传入不同码率的资源文件地址，然后在视图上手动切换码率。

```javascript
const player = new QPlayer({
  qualityList: [{
    url: "http://pkeamo5ex.bkt.clouddn.com/snow_miku_qualities/starnight_snow_240p.mp4",
    name: "低清240P",
  }, {
    url: "http://pkeamo5ex.bkt.clouddn.com/snow_miku_qualities/starnight_snow_480p.mp4",
    name: "标清480P",
  }, {
    url: "http://pkeamo5ex.bkS.clouddn.com/snow_miku_qualities/starnight_snow_720p.mp4",
    name: "高清720P",
  }, {
    url: "http://pkeamo5ex.bkt.clouddn.com/snow_miku_qualities/starnight_snow_1080p.mp4",
    name: "超清1080P",
  }],
});
```

## 自定义视图
SDK 的默认视图提供了一些配置选项用于给用户自定义视图，通过在创建播放器的参数中指定，例如下面的代码自定义了播放器的开始播放按钮

```javascript
const player = new QPlayer({
  url: "xxxx",
  container: xxxx,
  defaultViewConfig: {
    playIcon: "https://xxx.xxx.xxx/play.png",
  },
})
```

完整的 `defaultViewConfig` 见下：

```typescript
DefaultViewConfig {
  playIcon: string,                   // 播放按钮
  pauseIcon: string,                  // 暂停按钮
  fullscreenIcon: string,             // 全屏按钮
  fullscreenExitIcon: string,         // 退出全屏按钮
  volumeIcon: string,                 // 音量按钮
  volumeOffIcon: string,              // 静音按钮
  settingsIcon: string,               // 设置按钮
  backIcon: string,                   // 返回按钮
  closeIcon: string,                  // 关闭按钮

  inactiveTimeout: number,            // 控件隐藏的延迟
  noControls: boolean,                // 是否不渲染控件
  showControls: boolean,              // 是否一直显示控件

  playerBarBackground: string,        // 进度条背景 CSS
  playerBarPlayedBackground: string,  // 已播放进度条背景 CSS
  playerBarBufferedBackground: string,// 已缓存进度条背景 CSS
}
```

## 播放 G.711 音频格式的 HLS
为了使浏览器可以播放 G.711 音频格式的 HLS 资源，SDK 需要额外加载一个外部依赖库。开发者在使用这个功能时，必须先将这个外部依赖库的地址传入到 SDK 之中。
   
G711 依赖库的下载地址 [https://sdk-release.qnsdk.com/aac_wasm.wasm]https://sdk-release.qnsdk.com/aac_wasm.wasm

> 请不要直接使用这个地址传入 SDK，我们不保证这个地址长期有效，推荐您将这个依赖库下载后存放在自己网站的存储空间中。

```javascript
const player = new QPlayer({
    url: "http://linking.qiniuapi.com/v1/device/resource/playback.m3u8?dtoken=JAwTPb8dmrbiwt89Eaxa4VsL4_xSIYJoJh4rQfOQ:9pfdXeBlYFUv_G-DlNaCk3gQvUQ=:eyJhcHBpZCI6IjNubTR4MDl3cWFnN2giLCJkZXZpY2UiOiJsbWt0ZXN0MSIsImRlYWRsaW5lIjoxNTg1MzczMzMxLCJyYW5kb20iOjE1NTQyNjkzMzE1MzcsInN0YXRlbWVudCI6W3siYWN0aW9uIjoibGlua2luZzp2b2QifSx7ImFjdGlvbiI6Imxpbmtpbmc6c3RhdHVzIn1dfQ==&start=1554269187&end=1554269219",
    hls: {
      // 指明该资源为 G711 HLS
      isG711: true,
    },
    // G711 外部依赖库的地址
    g711Wasm: "http://sdk-release.qnsdk.com/aac_wasm.wasm",
});
```
