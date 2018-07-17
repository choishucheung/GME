## 简介
欢迎使用腾讯云游戏多媒体引擎 SDK 。为方便开发者调试和接入腾讯云游戏多媒体引擎产品 API，这里向您介绍适用于开发的接口升级技术文档(GME 2.0 升级 GME 2.1)。


## 主要接口更新

### 修改接口：开启关闭采集设备
#### 原接口：ITMGContext ITMGAudioCtrl public int EnableMic
#### 现接口：ITMGContext ITMGAudioCtrl public int EnableAudioCaptureDevice
#### 现接口：ITMGContext ITMGAudioCtrl public int EnableAudioSend

原接口拆为两个接口，EnableAudioCaptureDevice 控制采集设备，EnableAudioSend 控制 SDK 音频上行。
替换方法：在原接口 EnableMic 替换为新接口：
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


### 修改接口：采集设备状态
#### 原接口：ITMGContext ITMGAudioCtrl public int GetMicState
#### 现接口：ITMGContext ITMGAudioCtrl public bool IsAudioCaptureDeviceEnabled
#### 现接口：ITMGContext ITMGAudioCtrl public bool IsAudioSendEnabled
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

### 修改接口：开启关闭播放设备
#### 原接口：ITMGContext ITMGAudioCtrl public int EnableSpeaker
#### 现接口：ITMGContext ITMGAudioCtrl public int EnableAudioPlayDevice
#### 现接口：ITMGContext ITMGAudioCtrl public int EnableAudioRecv
原接口拆为两个接口，EnableAudioPlayDevice 控制播放设备，EnableAudioRecv 控制 SDK 音频下行。

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

### 修改接口：播放设备状态
#### 原接口：ITMGContext ITMGAudioCtrl public int GetSpeakerState
#### 现接口：ITMGContext ITMGAudioCtrl public bool IsAudioPlayDeviceEnabled
#### 现接口：ITMGContext ITMGAudioCtrl public bool IsAudioRecvEnabled
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



## 其他接口更新

### 离线语音上传语音文件
#### 增加参数 authBuffer。

此接口用于上传语音文件。鉴权码的生成参考接口 GenAuthBuffer。
> 函数原型  
```
IQAVPTT int UploadRecordedFile (string filePath, byte[] authBuffer)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------|
| filePath    		|string   	|上传的语音路径	|
| authBuffer 	|Byte[] 	|鉴权码			|


### 离线语音下载语音文件
#### 增加参数 authBuffer。
此接口用于下载语音文件。鉴权码的生成参考接口 GenAuthBuffer。
> 函数原型  
```
IQAVPTT DownloadRecordedFile (string fileID, string downloadFilePath, byte[] authBuffer)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------|
| fileID    |string                       |文件的url路径|
| downloadFilePath    |string                       |文件的本地保存路径|
| authBuffer 	|Byte[] 	|鉴权码					|