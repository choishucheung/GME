## 简介
欢迎使用腾讯云游戏多媒体引擎 SDK 。为方便开发者调试和接入腾讯云游戏多媒体引擎产品 API，这里向您介绍适用于开发的接口升级技术文档(GME 2.0 升级 GME 2.1)。

## 接口删除
- ### 离线语音删除 TLS 鉴权相关接口。
QAVSig GenSig

IQAVPTT ApplyAccessToken

- ### 实时语音鉴权删除参数：expTime（超时时间）和authBits（权限）
Android：AuthBuffer public native byte[] genAuthBuffer(int sdkAppId, int roomId, String identifier, String key)

|参数     | 类型         |意义|
| ------------- |:-------------:|-------------|
| appId    		|int   		|来自腾讯云控制台的 SdkAppId 号码		|
| roomId    		|int   		|房间号，只支持32位				|
| openID    	|String 	|用户标识					|
| key    		|string 	|来自腾讯云控制台的密钥				|

- ### 离线语音鉴权校验增加
#### 增加接口 ApplyPTTAuthbuffer (byte[] authBuffer)
authBuffer 使用接口 genAuthBuffer 进行生成，其中参数 roomId 必须填 0。

- ### EnableMic 及 EnableSpeaker 相关回调删除

## 主要接口更新
这次更新保留接口 EnableMic 及 EnableSpeaker 接口，同时增加 EnableAudioCaptureDevice、EnableAudioSend、EnableAudioPlayDevice、EnableAudioRecv 四个接口。
调用 EnableMic 相当于同时调用 EnableAudioCaptureDevice 及 EnableAudioSend，调用 EnableSpeaker 相当于同时调用 EnableAudioPlayDevice 及 EnableAudioRecv。


- ### 增加接口：开启/关闭采集设备及开启/关闭音频上行
#### ITMGContext ITMGAudioCtrl public int EnableAudioCaptureDevice
#### ITMGContext ITMGAudioCtrl public int EnableAudioSend

EnableAudioCaptureDevice 控制采集设备，EnableAudioSend 控制 SDK 音频上行。

- 如果客户端为娱乐主播类型，建议只进房调用 EnableAudioCaptureDevice(true) && EnabledAudioPlayDevice(true) 一次，点击时只调用 EnableAudioSend/Recv 来控制音频流。
- 如果客户端为游戏端，建议在原接口 EnableMic 部分同时调用 EnableAudioCaptureDevice(true) && EnabledAudioPlayDevice(true) 。

> 函数原型  
```
ITMGAudioCtrl int EnableAudioPlayDevice(bool isEnabled)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------|
| isEnabled    |bool     |如果需要打开采集设备，则传入的参数为 true，如果关闭采集设备，则参数为 false|
  


此接口用于打开/关闭音频上行。如果采集设备已经打开，那么会发送采集到的音频数据。如果采集设备没有打开，那么仍旧无声。采集设备的打开关闭参见接口 EnableAudioCaptureDevice。

> 函数原型  
```
ITMGAudioCtrl int EnableAudioSend(bool isEnabled)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------|
| isEnabled    |bool     |如果需要打开音频上行，则传入的参数为 true，如果关闭音频上行，则参数为 false|


- ### 增加接口：状态获取
#### ITMGContext ITMGAudioCtrl public bool IsAudioCaptureDeviceEnabled
#### ITMGContext ITMGAudioCtrl public bool IsAudioSendEnabled
返回值为 bool ，需注意。

此接口用于采集设备状态获取。
> 函数原型  
```
ITMGAudioCtrl bool IsAudioCaptureDeviceEnabled()
```

此接口用于音频上行状态获取。
> 函数原型  
```
ITMGAudioCtrl bool IsAudioSendEnabled()
```

- ### 修改接口：开启/关闭播放设备及打开/关闭音频下行
#### ITMGContext ITMGAudioCtrl public int EnableAudioPlayDevice
#### ITMGContext ITMGAudioCtrl public int EnableAudioRecv
EnableAudioPlayDevice 控制播放设备，EnableAudioRecv 控制 SDK 音频下行。

此接口用于开启关闭播放设备。
> 函数原型  
```
ITMGAudioCtrl EnableAudioPlayDevice(bool isEnabled)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------|
| isEnabled    |bool        |如果需要关闭播放设备，则传入的参数为 false，如果打开播放设备，则参数为 true|

此接口用于打开/关闭音频下行。如果播放设备已经打开，那么会播放房间里其他人的音频数据。如果播放设备没有打开，那么仍旧无声。播放设备的打开关闭参见接口 参见EnableAudioPlayDevice。

> 函数原型  
```
ITMGAudioCtrl int EnableAudioRecv(bool isEnabled)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------|
| isEnabled    |bool     |如果需要打开音频下行，则传入的参数为 true，如果关闭音频下行，则参数为 false|

- ### 修改接口：播放设备状态
#### ITMGContext ITMGAudioCtrl public bool IsAudioPlayDeviceEnabled
#### ITMGContext ITMGAudioCtrl public bool IsAudioRecvEnabled
返回值为 bool ，需注意。

此接口用于播放设备状态获取。
> 函数原型  
```
ITMGAudioCtrl bool IsAudioPlayDeviceEnabled()
```

此接口用于音频下行状态获取。
> 函数原型  
```
ITMGAudioCtrl bool IsAudioRecvEnabled()
```






