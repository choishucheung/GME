## 简介

欢迎使用腾讯云游戏多媒体引擎 SDK 。为方便开发者调试和接入腾讯云游戏多媒体引擎产品 API，这里向您介绍 SDK 常见的问题及解答。以下为一些常见的技术问题解答及常见概念的理解，本文档将持续补充。


## 官网下载及修改 Demo 相关问题 
#### Q：请问游戏多媒体引擎 Demo 及 SDK 在哪里下载？
A：请在[腾讯云游戏多媒体引擎官网](https://cloud.tencent.com/product/tmg?idx=1)下载。目前官网上有 Unity 引擎 Demo，Cocos2D 引擎 Demo，Android 原生开发 Demo 及 iOS 原生开发 Demo。

#### Q：游戏多媒体引擎 Demo 下载后如果要换为自己申请的账号可以吗？
A：	1、可以，需要在控制台获取两个号码，分别为 sdkappid、权限密钥。
2、如果需要用到离线语音还需要获得 TLS 签名密钥。以上号码具体获取方法参见[接入指引](https://cloud.tencent.com/document/product/607/10782)。
3、实时语音用自己的appid的话需要在AVChatViewController中的GetAuthBuffer中修改实时语音的key。

#### Q：Unity 的 Demo 可以在编辑器模式下运行吗？
A：暂时不可以，后续版本会进行修复。建议导出后尝试。

#### Q：在使用 Demo 的时候报错：errinfo=priv map info error。
A：相关进房参数出现错误，请检查 sdkappid、权限密钥及 accountType 是否已经替换。

#### Q：在使用 Demo 的时候，离线语音功能无法使用。
A：首先检查控制台中，【应用管理】->【离线语音设置】->【语音消息设置】->【编辑】，将语音消息设置为打开。
如果依旧无法使用，请联系 GME 相关技术人员。

## SDK功能相关问题
#### Q：Android的so库有armv8的吗？
A：支持，请咨询 GME 相关技术人员。

#### Q：为什么进房会返回 Http Invalid id？
A：目前接口上参数 userid 的类型虽然是 string 类型，但实际我们内部会把 string 类型强转为 uint64 类型。 如果转换失败或者转换后的结果小于 10000，则会进房失败并且返回 Http Invalid id。
如果你们的帐号是从 0 开始的，那么建议你们帐号加上 10000。例如你们的帐号是999的话，输入的数字为 10999。

#### Q：用户权限为audience，是否有上行权限？
A：audience 没有上行，只是纯观众，不能使用startAccompany。

#### Q：请问麦克风权限占用具体情况？
A：在 EnterRoom 函数调用成功之后之后就会占用麦克风权限，期间其他程序无法进行麦克风采集。
调用 EnableMic(false) 无法释放麦克风占用。
如果确实需要释放麦克风，请调用 PauseAudio。调用 PauseAudio 后会整个暂停引擎，调用 ResumeAudio 可恢复。

#### Q：使用sdk的时候，登模拟器放不了歌，也用不了电脑的声卡。
A：模拟器不支持mp3。

#### Q：SDK 是否支持听筒播放声音？
A：实时游戏音视频 SDK 不支持听筒。

#### Q：SDK 支持哪些格式的本地音频文件？
A：m4a、AAC、wav、mp3 一共四种格式。


## SDK获取日志及排查问题
#### Q：如何取得Android 日志？
A：在 tencent/imsdklogs/包名/  这个目录里有 QAVSDK_带日期.log，这个文件为日志文件。

#### Q：声音卡顿的主要原因
A：1. 音乐卡：主播使用外放的设备播音乐，然后通过另一个手机采集并主播（这里会必现卡顿，可建议主播带耳机）
2. 网络卡：上行丢包率过高或者上行延时波动较大情况下 观众会听到卡顿

















