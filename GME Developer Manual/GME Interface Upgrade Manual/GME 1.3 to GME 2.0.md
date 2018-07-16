## 简介
欢迎使用腾讯云游戏多媒体引擎 SDK 。为方便开发者调试和接入腾讯云游戏多媒体引擎产品 API，这里向您介绍适用于开发的接口升级技术文档(GME 1.3 升级 GME 2.0)。


## 主要接口更新

### 修改接口：初始化 SDK
#### 原接口：ITMGContext public int SetAppInfo
#### 现接口：ITMGContext public int Init
> 函数原型 
```
ITMGContext public int Init(String sdkAppId, String openID)
```

|参数     | 类型         |意义|
| ------------- |:-------------:|-------------|
| sdkAppId    	|String  |来自腾讯云控制台的 SdkAppId 号码						|
| openID    	|String  |OpenID 只支持 Int64 类型（转为string传入），必须大于 10000，用于标识用户 	|

> 示例代码 

```
ITMGContext.GetInstance(this).Init(sdkAppId, openID);
```


### 修改接口：加入房间
#### 原接口：第二个参数为role，进房角色
#### 现接口：第二个参数为roomTye，房间音频类型
> 函数原型
```
ITMGContext public abstract void  EnterRoom(int relationId, int roomType, byte[] authBuffer)
```

|参数     | 类型         |意义|
| ------------- |:-------------:|-------------|
| relationId 	|int		|房间号，只支持32位	|
| roomType 	|int		|房间音频类型		|
| authBuffer	|byte[]	|鉴权码				|

|音频类型     	|含义|参数|音量类型|控制台推荐采样率设置|适用场景|
| ------------- |------------ | ---- |---- |---- |---- |
| ITMG_ROOM_TYPE_FLUENCY			|流畅音质	|1|扬声器：通话音量；耳机：媒体音量	|如对音质无特殊需求，16K采样率即可；					|流畅优先、超低延迟实时语音，应用在游戏内开黑场景，适用于FPS、MOBA等类型的游戏；	|							
| ITMG_ROOM_TYPE_STANDARD			|标准音质	|2|扬声器：通话音量；耳机：媒体音量	|根据对音质的需求，可以选择16k/48k采样率				|音质较好，延时适中，适用于狼人杀、棋牌等休闲游戏的实时通话场景；	|												
| ITMG_ROOM_TYPE_HIGHQUALITY		|高清音质	|3|扬声器：媒体音量；耳机：媒体音量	|为了保证最佳效果，建议控制台设置48k采样率的高音质配置	|超高音质，延时相对大一些，适用于音乐舞蹈类游戏以及语音社交类APP；适用于播放音乐、线上K歌等有高音质要求的场景；	|

- 如对音量类型或场景有特殊需求，请联系一线客服反馈；
- 控制台采样率设置会直接影响游戏语音效果，请在[控制台](https://console.cloud.tencent.com/gamegme)上再次确认采样率设置是否符合项目使用场景。

> 示例代码  
```
ITMGContext.GetInstance(this).EnterRoom(Integer.parseInt(relationId),roomType, authBuffer);    
```

### 新增接口：修改房间音频类型
（注意：GME 2.0 SDK中已无 role 概念，与 role 有关的接口都删除了）
> 函数原型  
```
IITMGContext TMGRoom public void ChangeRoomType(int nRoomType)
```


|参数     | 类型         |意义|
| ------------- |:-------------:|-------------|
| nRoomType    |int    |希望房间切换成的类型，房间音频类型参考 EnterRoom 接口|

> 示例代码  
```
ITMGContext.GetInstance(this).GetRoom().ChangeRoomType(nRoomType);
```


### 设置回调函数
#### 原接口：ITMGContext public int TMGDelegate
#### 现接口：ITMGContext public int SetTMGDelegate
> 函数原型 
```
ITMGContext public int SetTMGDelegate(ITMGDelegate delegate)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------|
| delegate    |ITMGDelegate |SDK 回调函数|
> 示例代码  
```
TMGContext.GetInstance(this).SetTMGDelegate(itmgDelegate);
```





## 其他接口更新
### 新增变声特效能力
调用此接口设置变声特效。
> 函数原型  
```
TMGAudioEffectCtrl int setVoiceType(int type)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------|
| type    |int                    |表示本端音频变声类型|


|类型参数     |参数代表|意义|
| ------------- |-------------|------------- |
|VOICE_TYPE_ORIGINAL_SOUND  	|0	|原声			|
|VOICE_TYPE_LOLITA    			|1	|萝莉			|
|VOICE_TYPE_UNCLE  			|2	|大叔			|
|VOICE_TYPE_INTANGIBLE    		|3	|空灵			|
|VOICE_TYPE_KINDER_GARTEN    	|4	|幼稚园			|
|VOICE_TYPE_HEAVY_GARTEN    	|5	|重机器			|
|VOICE_TYPE_OPTIMUS_PRIME    	|6	|擎天柱			|
|VOICE_TYPE_CAGED_ANIMAL    	|7	|困兽			|
|VOICE_TYPE_DIALECT    			|8	|土掉渣/歪果仁/方言	|
|VOICE_TYPE_METAL_ROBOT    	|9	|金属机器人		|
|VOICE_TYPE_DEAD_FATBOY    	|10	|死肥仔			|

> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().setVoiceType(0);
```


### 新增 3D 音效能力
详细使用方法见[3D音效文档](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/GME%20Spatializer%20Manual.md)。