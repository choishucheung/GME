## 简介

欢迎使用腾讯云游戏多媒体引擎 SDK 。为方便 Unity 开发者调试和接入腾讯云游戏多媒体引擎产品 API，这里向您介绍适用于 Unity 开发的接入技术文档。

## 目录
[SDK初始化](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/Unity%20Developer%20Manual/Unity%20SDK%20Developer%20Manual.md#sdk%E5%88%9D%E5%A7%8B%E5%8C%96)

[实时语音房间事件接口](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/Unity%20Developer%20Manual/Unity%20SDK%20Developer%20Manual.md#%E5%AE%9E%E6%97%B6%E8%AF%AD%E9%9F%B3%E6%88%BF%E9%97%B4%E4%BA%8B%E4%BB%B6%E6%8E%A5%E5%8F%A3)

[实时语音音频接口](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/Unity%20Developer%20Manual/Unity%20SDK%20Developer%20Manual.md#%E5%AE%9E%E6%97%B6%E8%AF%AD%E9%9F%B3%E9%9F%B3%E9%A2%91%E6%8E%A5%E5%8F%A3)

[实时语音伴奏相关接口](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/Unity%20Developer%20Manual/Unity%20SDK%20Developer%20Manual.md#%E5%AE%9E%E6%97%B6%E8%AF%AD%E9%9F%B3%E4%BC%B4%E5%A5%8F%E7%9B%B8%E5%85%B3%E6%8E%A5%E5%8F%A3)

[实时语音音效相关接口](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/Unity%20Developer%20Manual/Unity%20SDK%20Developer%20Manual.md#%E5%AE%9E%E6%97%B6%E8%AF%AD%E9%9F%B3%E9%9F%B3%E6%95%88%E7%9B%B8%E5%85%B3%E6%8E%A5%E5%8F%A3)

[离线语音](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/Unity%20Developer%20Manual/Unity%20SDK%20Developer%20Manual.md#%E7%A6%BB%E7%BA%BF%E8%AF%AD%E9%9F%B3)

## SDK初始化  
### 设置 App 信息
获取相关信息，由腾讯云控制台申请，详情见[游戏多媒体引擎接入指引](https://github.com/TencentMediaLab/GME/blob/master/GME%20Introduction.md)。
此接口需要来自腾讯云控制台的 SdkAppId 号码作为参数，再加上 openId，这个 openId 是唯一标识一个用户，规则由 App 开发者自行制定，App 内不重复即可（目前只支持 INT64）。
> 函数原型
```
IQAVContext SetAppInfo(string sdkAppID, string openID)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| sdkAppId    |string  |来自腾讯云控制台的 SdkAppId 号码|
| openID    |string  |唯一标识一个用户，规则由 App 开发者自行制定，目前只支持大于10000的数字类型|
> 示例代码  
```
int ret = IQAVContext.GetInstance().SetAppInfo(str_appId, str_userId);
	if (ret != QAVError.OK) {
		return;
	}
```

### 设置版本信息
设置版本信息，用于查 Log 信息及 Bug 时使用。方便后台统计, 策略调整等（不设置不影响功能）。
> 函数原型
```
ITMGContext abstract void SetAppVersion(string sAppVersion)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| sAppVersion    |NSString  |版本号|
> 示例代码  
```
IQAVContext.GetInstance().SetAppVersion(sAppVersion);
```

### 获取版本号
获取 SDK 版本号，用于分析。
> 函数原型
```
ITMGContext  abstract string GetVersion()
```
> 示例代码  
```
IQAVContext.GetInstance().GetVersion();
```
### 实时语音鉴权信息
接下来是生成 AuthBuffer，用于相关功能的加密和鉴权，相关参数获取及详情见[游戏多媒体引擎接入指引](https://github.com/TencentMediaLab/GME/blob/master/GME%20Introduction.md)。  
>注意：在加入房间之前需要 AuthBuffer 作为参数。

该函数返回值为 NSData 类型。
> 函数原型
```
QAVAuthBuffer GenAuthBuffer(int appId, int roomId, string identifier, string key, int expTime, uint authBits)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| appId    	|int   	|来自腾讯云控制台的 SdkAppId 号码	|
| roomId    	|int   	|要加入的房间名						|
| identifier 	|string  	|用户标识							|
| key    		|string  	|来自腾讯云控制台的密钥				|
| expTime   	|int  	|authBuffer 超时时间					|
| authBits    	|uint     	|权限								|

>关于权限  
>
ITMG_AUTH_BITS_ALL 代表拥有全部权限，建议实时用户、主播使用，ITMG_AUTH_BITS_RECV 代表下行权限，建议纯听众、观众使用，不能使用startAccompany。

> 示例代码  
```
byte[] GetAuthBuffer(string appId, string userId, int roomId, uint authBits)
    {
	TimeSpan t = DateTime.UtcNow - new DateTime(1970, 1, 1, 0, 0, 0, 0);
	double timeStamp = t.TotalSeconds;
	return QAVAuthBuffer.GenAuthBuffer(int.Parse(appId), roomId, userId, "a495dca2482589e9", (int)timeStamp + 1800, authBits);
}
byte[] authBuffer = this.GetAuthBuffer(str_appId,, str_userId, roomId, recvOnly ? IQAVContext.AUTH_BITS_RECV : IQAVContext.AUTH_BITS_ALL);
```

### 设置最大混音路数
最后是设置最大混音路数（同时听到多少人讲话），在进房前调用，不调用默认为 6 路混音。
> 函数原型
```
ITMGContext int SetMaxMicCount(int nCount)
```

|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| nCount    |int   |混音路数，默认为6|
> 示例代码  
```
IQAVContext.GetInstance().SetMaxMicCount(nCount);
```

## 实时语音房间事件接口
### 加入房间
用生成的鉴权信息进房，会收到消息为 ITMG_MAIN_EVENT_TYPE_ENTER_ROOM 的回调。
小队语音详细接入细节请查阅[小队语音接入文档](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/GME%20TeamAudio%20Manual.md)。
>注意:
>1、加入房间默认不打开麦克风及扬声器。
>2、在 EnterRoom 函数调用之前要先调用 SetAppInfo 函数及 SetAppVersion 函数进行相关信息的设置。

> 函数原型
```
ITMGContext EnterRoom(int relationId, int roomType, byte[] authBuffer)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| relationId		|int    	|房间号					|
| roomType 	|ITMGRoomType		|房间音频类型		|
| authBuffer 	|Byte[] 	|鉴权					|

|ITMGRoomType     	|含义|参数|
| ------------- |------------ | ---- |
| ITMG_ROOM_TYPE_FLUENCY			|流畅音质	|1
| ITMG_ROOM_TYPE_STANDARD			|标准音质	|2
| ITMG_ROOM_TYPE_HIGHQUALITY		|高清音质	|3

> 示例代码  
```
IQAVContext.GetInstance().EnterRoom(roomId, roomType, authBuffer);
```

### 加入房间事件的回调
加入房间后，需要通过委托函数进行回调。其中包含两个信息：result 及 error_info。
>函数原型
```
委托函数：
public delegate void QAVEnterRoomComplete(int result, string error_info);
事件函数：
public abstract event QAVEnterRoomComplete OnEnterRoomCompleteEvent;
```

> 示例代码
```
对事件进行监听：
IQAVContext.GetInstance().OnEnterRoomCompleteEvent += new QAVEnterRoomComplete(OnEnterRoomComplete);

监听处理：
void OnEnterRoomComplete(int err, string errInfo)
    {
	if (err != 0) {
	    ShowWarnning (string.Format ("join room failed, err:{0}, errInfo:{1}", err, errInfo));
	    return;
	}else{
	    ShowWarnning (string.Format ("当前语音房间id:{0},请在别的终端加入这个房间进行通话",roomId ));
    }
}
```

### 判断是否已经进入房间
通过调用此接口可以判断是否已经进入房间，返回值为 BOOL 类型。
> 函数原型  
```
ITMGContext abstract bool IsRoomEntered()
```
> 示例代码  
```
IQAVContext.GetInstance().IsRoomEntered();
```

### 退出房间
通过调用此接口可以退出所在房间。
> 函数原型  
```
ITMGContext ExitRoom()
```
> 示例代码  
```
IQAVContext.GetInstance().ExitRoom();
```

### 退出房间回调
退出房间完成回调，通过委托传递消息。
> 函数原型  
```
委托函数：
public delegate void QAVExitRoomComplete();
事件函数：
public abstract event QAVExitRoomComplete OnExitRoomCompleteEvent; 
```
> 示例代码  
```
对事件进行监听：
IQAVContext.GetInstance().OnExitRoomCompleteEvent += new QAVExitRoomComplete(OnExitRoomComplete);
监听处理：
void OnExitRoomComplete(){
    //退出房间后的处理
}
```

### 获取用户房间音频类型
此接口用于获取用户房间音频类型，返回值为房间音频类型，房间音频类型参考 EnterRoom 接口。

> 函数原型  
```
ITMGContext ITMGRoom public  int GetRoomType()
```

> 示例代码  
```
IQAVContext.GetInstance().GetRoom().GetRoomType(1);
```

### 修改用户房间音频类型
此接口用于修改用户房间音频类型，结果参见回调事件，事件类型为 ITMG_MAIN_EVENT_TYPE_CHANGE_ROOM_TYPE。
> 函数原型  
```
ITMGContext ITMGRoom public void ChangeRoomType(ITMGRoomType roomtype)
```

|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| roomtype    |ITMGRoomType    |希望房间切换成的类型，房间音频类型参考 EnterRoom 接口|

> 示例代码  
```
IQAVContext.GetInstance().GetRoom().ChangeRoomType(1);
```



### 主动修改房间类型回调
主动设置房间类型，房间类型设置完成后，通过委托传递消息。

|返回的参数     | 意义  
| ------------- |:-------------:|
| result    |0 是代表成功
| error_info    |如果失败，会传递相关错误信息

```
委托函数：
public delegate void QAVOnChangeRoomtypeCallback(int result, string error_info);

事件函数：
public abstract event QAVCallback OnChangeRoomtypeCallback; 
```
> 示例代码  
```
对事件进行监听：
IQAVContext.GetInstance().OnChangeRoomtypeCallback += new QAVOnChangeRoomtypeCallback(OnChangeRoomtypeCallback);
监听处理：
void OnChangeRoomtypeCallback(){
    //房间类型设置完成后的处理
}
```

### 房间类型修改通知
房间类型设置完成后，通过委托传递修改通知，通知其他房间的用户房间类型修改，返回的是房间类型，参考 EnterRoom 接口。

```
委托函数：
public delegate void QAVOnRoomTypeChangedEvent(int roomtype);

事件函数：
public abstract event QAVOnRoomTypeChangedEvent OnRoomTypeChangedEvent;	
```
> 示例代码  
```
对事件进行监听：
IQAVContext.GetInstance().OnRoomTypeChangedEvent += new QAVOnRoomTypeChangedEvent(OnRoomTypeChangedEvent);
监听处理：
void OnRoomTypeChangedEvent(){
    //房间类型改变后的处理
}
```


	
### 成员状态变化
该事件在状态变化才通知，状态不变化的情况下不通知。如需实时获取成员状态，请在上层收到通知时缓存。

|event_id     | 含义         |应用侧维护内容|
| ------------- |:-------------:|-------------|
|ITMG_EVENT_ID_USER_ENTER    				|有成员进入房间			|应用侧维护成员列表		|
|ITMG_EVENT_ID_USER_EXIT    				|有成员退出房间			|应用侧维护成员列表		|
|ITMG_EVENT_ID_USER_HAS_AUDIO    		|有成员发送音频包		|应用侧维护通话成员列表	|
|ITMG_EVENT_ID_USER_NO_AUDIO    			|有成员停止发送音频包	|应用侧维护通话成员列表	|

> 示例代码  
```
委托函数：
public delegate void QAVEndpointsUpdateInfo(int eventID, int count, [MarshalAs(UnmanagedType.LPArray, SizeParamIndex = 1)]string[] identifierList);
事件函数：
public abstract event QAVEndpointsUpdateInfo OnEndpointsUpdateInfoEvent;
```
>示例代码
```
对事件进行监听：
IQAVContext.GetInstance().OnEndpointsUpdateInfoEvent += new QAVEndpointsUpdateInfo(OnEndpointsUpdateInfo);
监听处理：
void OnEndpointsUpdateInfo(int eventID, int count, string[] identifierList)
{
    //进行处理
		//开发者对参数进行解析，得到信息 event_id及 user_list
		    switch (eventID)
 		    {
 		    case ITMG_EVENT_ID_USER_ENTER:
  			    //有成员进入房间
  			    break;
 		    case ITMG_EVENT_ID_USER_EXIT:
  			    //有成员退出房间
			    break;
		    case ITMG_EVENT_ID_USER_HAS_AUDIO:
			    //有成员开启麦克风
			    break;
		    case ITMG_EVENT_ID_USER_NO_AUDIO:
			    //有成员关闭麦克风
			    break;
		  
		    default:
			    break;
 		    }
		break;
}

```

### 获取诊断信息
获取音视频通话的实时通话质量的相关信息。该接口主要用来查看实时通话质量、排查问题等，业务侧可以忽略。
> 函数原型  
```
IQAVRoom GetQualityTips()
```
> 示例代码  
```
string tips = IQAVContext.GetInstance().GetRoom().GetQualityTips();
```

## 实时语音音频接口

### 暂停音频引擎的采集和播放
调用此接口暂停音频引擎的采集和播放，只在进房后有效。
在 EnterRoom 函数调用成功之后之后就会占用麦克风权限，期间其他程序无法进行麦克风采集。
注意：调用 EnableMic(false) 无法释放麦克风占用。
如果确实需要释放麦克风，请调用 PauseAudio 函数。调用 PauseAudio 函数后会整个暂停引擎，调用 ResumeAudio 函数可恢复音频采集。
> 函数原型  
```
ITMGAudioCtrl abstract int PauseAudio()
```
> 示例代码  
```
IQAVContext.GetInstance ().GetAudioCtrl ().PauseAudio();
```

### 恢复音频引擎的采集和播放
调用此接口恢复音频引擎的采集和播放，只在进房后有效。
> 函数原型  
```
ITMGAudioCtrl abstract int ResumeAudio()
```
> 示例代码  
```
IQAVContext.GetInstance ().GetAudioCtrl ().ResumeAudio();
```

### 开启、关闭音频数据黑名单逻辑
开启黑名单时每次调用黑名单函数，黑名单将被重置为新的成员列表，而不是累加。需要定制所需接收的音频数据才调用，不调用则默认接收房间内所有音频数据。返回值为 0 表示调用失败。
> 函数原型  
```
ITMGContext GetRoom UnrequestAudioList(string[] identifierList)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| identifierList    |string[]      |黑名单列表|
> 示例代码  
```
IQAVContext.GetInstance().GetRoom ().UnrequestAudioList (identifierList);
```

### 麦克风开启关闭事件
此接口用来开启及关闭麦克风。
>注意:加入房间默认不打开麦克风及扬声器。

> 函数原型  
```
ITMGAudioCtrl EnableMic(bool isEnabled)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| isEnabled    |boolean     |如果需要打开麦克风，则传入的参数为 YES，如果关闭麦克风，则参数为 NO|
> 示例代码  
```
打开麦克风
IQAVContext.GetInstance().GetAudioCtrl().EnableMic(true);
关闭麦克风
IQAVContext.GetInstance().GetAudioCtrl().EnableMic(false);
```

### 麦克风状态获取
此函数获取麦克风状态。返回值 0 为关闭麦克风状态，返回值 1 为打开麦克风状态，返回值 2 为麦克风设备正在操作中，返回值 4 为设备没初始化好。
> 函数原型  
```
ITMGAudioCtrl GetMicState()
```
> 示例代码  
```
micToggle.isOn = IQAVContext.GetInstance().GetAudioCtrl().GetMicState() != 0;
```

### 获取麦克风实时音量
此接口用于获取麦克风实时音量，返回值为 int 类型。
> 函数原型  
```
ITMGAudioCtrl -(int)GetMicLevel
```
> 示例代码  
```
IQAVContext.GetInstance().GetAudioCtrl().GetMicLevel();
```

### 设置麦克风的软件音量
此接口用于设置麦克风的软件音量。参数 volume 用于设置麦克风的软件音量，当数值为 0 的时候表示静音，当数值为 100 的时候表示音量不增不减，默认数值为 100。
> 函数原型  
```
ITMGAudioCtrl SetMicVolume(int volume)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| volume    |int      |设置音量，范围 0 到 150|
> 示例代码  
```
int micVol = (int)(value * 100);
IQAVContext.GetInstance().GetAudioCtrl().SetMicVolume (micVol);
```

### 获取麦克风的软件音量
此接口用于获取麦克风的软件音量。返回值为一个int类型数值。
> 函数原型  
```
ITMGAudioCtrl GetMicVolume()
```
> 示例代码  
```
IQAVContext.GetInstance().GetAudioCtrl().GetMicVolume();
```

### 扬声器开启关闭事件
此接口用于设置扬声器开启关闭。
> 函数原型  
```
ITMGAudioCtrl EnableSpeaker(bool isEnabled)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| isEnabled    |bool        |如果需要关闭扬声器，则传入的参数为 NO，如果打开扬声器，则参数为 YES|
> 示例代码  
```
打开扬声器
IQAVContext.GetInstance().GetAudioCtrl().EnableSpeaker(true);
关闭扬声器
IQAVContext.GetInstance().GetAudioCtrl().EnableSpeaker(false);
```


### 扬声器状态获取
此接口用于扬声器状态获取。返回值 0 为关闭扬声器状态，返回值 1 为打开扬声器状态，返回值 2 为扬声器设备正在操作中，返回值 4 为设备没初始化好。
> 函数原型  
```
ITMGAudioCtrl GetSpeakerState()
```

> 示例代码  
```
speakerToggle.isOn = IQAVContext.GetInstance().GetAudioCtrl().GetSpeakerState() != 0;
```

### 获取扬声器实时音量
此接口用于获取扬声器实时音量。返回值为 int 类型数值，表示扬声器实时音量。
> 函数原型  
```
ITMGAudioCtrl GetSpeakerLevel()
```

> 示例代码  
```
IQAVContext.GetInstance().GetAudioCtrl().GetSpeakerLevel();
```

### 设置扬声器的软件音量
此函数用于设置扬声器的软件音量。
>注意：参数 volume 用于设置扬声器的软件音量，当数值为 0 的时候表示静音，当数值为 100 的时候表示音量不增不减，默认数值为 100。

> 函数原型  
```
ITMGAudioCtrl SetSpeakerVolume(int volume)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| vol    |int        |设置音量，范围 0 到 150|
> 示例代码  
```
int speVol = (int)(value * 100);
IQAVContext.GetInstance().GetAudioCtrl().SetSpeakerVolume(speVol );
```

### 获取扬声器的软件音量
此接口用于获取扬声器的软件音量。返回值为 int 类型数值，代表扬声器的软件音量。
>注意：Level 是实时音量，Volume 是扬声器的软件音量，最终声音音量相当于 Level*Volume%。举个例子：实时音量是数值是 100 的话，此时Volume的数值是 60，那么最终发出来的声音数值也是 60。

> 函数原型  
```
ITMGAudioCtrl GetSpeakerVolume()
```
> 示例代码  
```
IQAVContext.GetInstance().GetAudioCtrl().GetSpeakerVolume();
```


### 启动耳返
此接口用于启动耳返。
> 函数原型  

```
ITMGContext GetAudioCtrl EnableLoopBack(bool enable)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| enable    |bool         |设置是否启动|
> 示例代码  

```
IQAVContext.GetInstance().GetAudioCtrl().EnableLoopBack(true);
```


## 实时语音伴奏相关接口

### 开始播放伴奏
调用此函数开始播放伴奏。支持 m4a、AAC、wav、mp3 一共四种格式。
注意：1、调用此 API，音量会重置。
2、下行权限不能启用此 API。
> 函数原型  
```
IQAVAudioEffectCtrl int StartAccompany(string filePath, bool loopBack, int loopCount, int duckerTimeMs)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    |string            |播放伴奏的路径|
| loopBack    |bool          |是否混音发送，一般都设置为 true ，即其他人也能听到伴奏|
| loopCount    |int          |循环次数，数值为 -1 表示无限循环|
> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().StartAccompany(filePath,true,loopCount,duckerTimeMs);
```

### 播放伴奏的回调
伴奏播放结束时的回调，通过委托传递消息。
> 函数原型  
```
委托函数：
public delegate void QAVAccompanyFileCompleteHandler(int code, string filepath);
事件函数：
public abstract event QAVAccompanyFileCompleteHandler OnAccompanyFileCompleteHandler;
```
>示例代码
```
对事件进行监听：
IQAVContext.GetInstance().GetAudioEffectCtrl().OnAccompanyFileCompleteHandler += new QAVAccompanyFileCompleteHandler(OnAccomponyFileCompleteHandler);
监听处理：
void OnAccomponyFileCompleteHandler(int code, string filepath){
    //播放伴奏的回调处理
}
```

### 停止播放伴奏
调用此函数停止播放伴奏。
> 函数原型  
```
IQAVAudioEffectCtrl int StopAccompany(int duckerTimeMs)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| duckerTimeMs    |int             |淡出时间|

> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().StopAccompany(duckerTimeMs);
```

### 伴奏是否播放完毕
如果播放完毕，返回值为 true，如果没播放完，返回值为 false。
> 函数原型  
```
IQAAudioEffectCtrl abstract bool IsAccompanyPlayEnd();
```
> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().IsAccompanyPlayEnd();
```


### 暂停播放伴奏
调用此接口暂停播放伴奏。
> 函数原型  
```
IQAAudioEffectCtrl abstract int PauseAccompany()
```
> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().PauseAccompany();
```



### 重新播放伴奏
此接口用于重新播放伴奏。
> 函数原型  
```
IQAAudioEffectCtrl abstract int ResumeAccompany()
```
> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().ResumeAccompany();
```

### 设置自己是否可以听到伴奏
此接口用于设置自己是否可以听到伴奏。
> 函数原型  
```
IQAAudioEffectCtrl abstract int EnableAccompanyPlay(bool enable)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| enable    |bool             |是否能听到|
> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().EnableAccompanyPlay(true);
```

### 设置他人是否也可以听到伴奏
设置他人是否也可以听到伴奏。
> 函数原型  
```
IQAAudioEffectCtrl abstract int EnableAccompanyLoopBack(bool enable)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| enable    |bool             |是否能听到|

> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().EnableAccompanyLoopBack(true);
```


### 设置伴奏音量
设置 DB 音量，默认值为 100，数值大于 100 增益，数值小于 100减益，值域为 0 到 200。
> 函数原型  
```
IQAAudioEffectCtrl abstract int SetAccompanyVolume(int vol)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| vol    |int             |音量数值|

> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().SetAccompanyVolume(vol);
```

### 获取播放伴奏的音量
此接口用于获取 DB 音量。
> 函数原型  
```
IQAAudioEffectCtrl abstract int GetAccompanyVolume()
```
> 示例代码  
```
string currentVol = "VOL: " + IQAVContext.GetInstance().GetAudioEffectCtrl().GetAccompanyVolume();
```

### 获得伴奏播放进度
以下两个接口用于获得伴奏播放进度。需要注意：Current / Total = 当前循环次数，Current % Total = 当前循环播放位置。
> 函数原型  
```
IQAAudioEffectCtrl abstract uint GetAccompanyFileTotalTimeByMs()
IQAAudioEffectCtrl abstract int GetAccompanyFileCurrentPlayedTimeByMs()
```
> 示例代码  
```
Sstring current = "Current: " + IQAVContext.GetInstance().GetAudioEffectCtrl().GetAccompanyFileCurrentPlayedTimeByMs() + " ms";
string total = "Total: " + IQAVContext.GetInstance().GetAudioEffectCtrl().GetAccompanyFileTotalTimeByMs() + " ms";
```


### 设置播放进度
此接口用于设置播放进度。
> 函数原型  
```
IQAAudioEffectCtrl abstract uint SetAccompanyFileCurrentPlayedTimeByMs(uint time)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| time    |uint                |播放进度，以毫秒为单位|

> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().SetAccompanyFileCurrentPlayedTimeByMs(time);
```


## 实时语音音效相关接口

### 播放音效
此接口用于播放音效。参数中音效 id 需要 App 侧进行管理，唯一标识一个独立文件。
> 函数原型  
```
IQAAudioEffectCtrl abstract int PlayEffect(int soundId, string filePath, bool loop = false, double pitch = 1.0f, double pan = 0.0f, double gain = 1.0f)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId    	|int      	|音效 id|
| filePath    	|string 	|音效路径|
| loop    		|bool   	|是否重复播放|
| pitch    	|double	|保留字段|
| pan    		|double	|保留字段|
| gain    		|double	|保留字段|
> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().PlayEffect(soundId,filePath,true,1.0,0,1.0);
```

### 暂停播放音效
此接口用于暂停播放音效。
> 函数原型  
```
IQAAudioEffectCtrl abstract int PauseEffect(int soundId)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId    |int                    |音效 id|

> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().PauseEffect(soundId);
```

### 暂停所有音效
调用此接口暂停所有音效。
> 函数原型  
```
IQAAudioEffectCtrl abstract int PauseAllEffects()
```
> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().PauseAllEffects();
```

### 重新播放音效
此接口用于重新播放音效。
> 函数原型  
```
IQAAudioEffectCtrl abstract int ResumeEffect(int soundId)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId    |int                    |音效 id|
> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().ResumeEffect(soundId);
```



### 重新播放所有音效
调用此接口重新播放所有音效。
> 函数原型  
```
IQAAudioEffectCtrl abstract int ResumeAllEffects()
```
> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().ResumeAllEffects();
```

### 停止播放音效
此接口用于停止播放音效。
> 函数原型  
```
IQAAudioEffectCtrl abstract int StopEffect(int soundId)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId    |int                    |音效 id|
> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().StopEffect(soundId);
```

### 停止播放所有音效
调用此接口停止播放所有音效。
> 函数原型  
```
IQAAudioEffectCtrl abstract int StopAllEffects()
```
> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().StopAllEffects(soundId); 
```

### 变声特效
调用此接口设置变声特效。
> 函数原型  
```
IQAAudioEffectCtrl int setVoiceType(int type)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| type    |int                    |表示本端音频变声类型|


|类型参数     |参数代表|意义|
| ------------- |-------------|------------- |
|VOICE_TYPE_ORIGINAL_SOUND  	|0	|原声
|VOICE_TYPE_LOLITA    			|1	|萝莉
|VOICE_TYPE_UNCLE  			|2	|大叔
|VOICE_TYPE_INTANGIBLE    		|3	|空灵
|VOICE_TYPE_KINDER_GARTEN    	|4	|幼稚园
|VOICE_TYPE_HEAVY_GARTEN    	|5	|重机器
|VOICE_TYPE_OPTIMUS_PRIME    	|6	|擎天柱
|VOICE_TYPE_CAGED_ANIMAL    	|7	|困兽
|VOICE_TYPE_DIALECT    			|8	|土掉渣/歪果仁/方言
|VOICE_TYPE_METAL_ROBOT    	|9	|金属机器人
|VOICE_TYPE_DEAD_FATBOY    	|10	|死肥仔

> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().setVoiceType(0); 
```

### 获取播放音效的音量
获取播放音效的音量，为线性音量，默认值为 100，数值大于 100 为增益效果，数值小于 100 为减益效果。
> 函数原型  
```
IQAAudioEffectCtrl abstract int GetEffectsVolume()
```
> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().GetEffectsVolume();
```


### 设置播放音效的音量
调用此接口设置播放音效的音量。
> 函数原型  
```
IQAAudioEffectCtrl abstract int SetEffectsVolume(int volume)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| volume    |int                    |音量数值|

> 示例代码  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().SetEffectsVolume(volume);
```







## 离线语音

### 离线语音技术接入初始化
初始化需要传入鉴权 access token 给 TLS 相关函数。鉴权的获取详细流程见[游戏多媒体引擎接入指引](https://github.com/TencentMediaLab/GME/blob/master/GME%20Introduction.md)。
Error 参数用于传递错误信息，比如参数填错了：appid 填 0、key 为空、identifier 为空之类的情况都会返回错误。
> 函数原型  
```
QAVSig GenSig(int appId, string identifier, string privateKey)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| appId    |int                    |来自腾讯云控制台的 SdkAppId 号码|
| identifier    |string                      |唯一标识一个用户，规则由 App 开发者自行制定|
| privateKey    |string                      |来自腾讯云控制台的鉴权|

```
IQAVPTT ApplyAccessToken(string accessToken)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| accessToken    | string|Gensig 函数返回的 accessToken|
> 示例代码  
```
string GetAccessToken(string appId, string openid, string userId)
	{
		string key = 云后台获取的鉴权;
		return QAVSig.GenSig(int.Parse(appId), userId, key);
	}

string sig = this.GetAccessToken(appId, openid, userId);
		if (sig != null) {
			IQAVContext.GetInstance().GetPttCtrl().ApplyAccessToken(sig);
			//成功
		} else {
			//失败
		}
```

### 限制最大语音信息时长
限制最大语音消息的长度，最大支持 60 秒。
> 函数原型  
```
IQAVPTT int SetMaxMessageLength(int msTime)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| msTime    |int                    |语音时长|
> 示例代码  
```
IQAVContext.GetInstance().GetPttCtrl().SetMaxMessageLength(60000); 
```


### 启动录音
此接口用于启动录音。
> 函数原型  
```
IQAVPTT int StartRecording(string fileDir)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| fileDir    |string                      |存放的语音路径|
> 示例代码  
```
string recordPath = Application.persistentDataPath + string.Format ("/{0}.silk", sUid++);
int ret = IQAVContext.GetInstance().GetPttCtrl().StartRecording(recordPath);
```

### 启动录音的回调
录音完成的回调，通过委托传递消息。
> 函数原型  
```
委托函数：
public delegate void QAVRecordFileCompleteCallback(int code, string filepath); 
事件函数：
public abstract event QAVRecordFileCompleteCallback OnRecordFileComplete;
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| code    |string                      |当 code 为 0 时，录制完成|
| filepath    |string                      |录制的存放地址|

> 示例代码  
```
对事件进行监听：
IQAVContext.GetInstance().GetPttCtrl().OnRecordFileComplete += mInnerHandler;
监听处理：
void mInnerHandler(int code, string filepath){
    //启动录音的回调
}
```

### 停止录音
此接口用于停止录音。
> 函数原型  
```
IQAVPTT int StopRecording()
```
> 示例代码  
```
IQAVContext.GetInstance().GetPttCtrl().StopRecording();
```

### 取消录音
调用此接口取消录音。
> 函数原型  

```
IQAVPTT int CancelRecording()
```
> 示例代码  

```
IQAVContext.GetInstance().GetPttCtrl().CancelRecording();
```

### 上传语音文件
此接口用于上传语音文件。
> 函数原型  
```
IQAVPTT int UploadRecordedFile (string filePath)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    |string                      |上传的语音路径|
> 示例代码  
```
IQAVContext.GetInstance().GetPttCtrl().UploadRecordedFile(filePath); 
```


### 上传语音完成的回调
上传语音完成的回调，通过委托传递消息。
> 函数原型  
```
委托函数：
public delegate void QAVUploadFileCompleteCallback(int code, string filepath, string fileid);
事件函数：
public abstract event QAVUploadFileCompleteCallback OnUploadFileComplete; 
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| code    |int                       |当code为0时，录制完成|
| filepath    |string                      |录制的存放地址|
| fileid    |string                      |文件的url路径|
> 示例代码  
```
对事件进行监听：
IQAVContext.GetInstance().GetPttCtrl().OnUploadFileComplete += mInnerHandler;
监听处理：
void mInnerHandler(int code, string filepath, string fileid){
    //上传语音完成的回调
}
```


### 下载语音文件
此接口用于下载语音文件。
> 函数原型  
```
IQAVPTT DownloadRecordedFile (string fileID, string downloadFilePath)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| fileID    |string                       |文件的url路径|
| downloadFilePath    |string                       |文件的下载路径|
> 示例代码  
```
IQAVContext.GetInstance().GetPttCtrl().DownloadRecordedFile(fileId, filePath);
```


### 下载语音文件完成回调
下载语音文件完成回调，通过委托传递消息。
> 函数原型  
```
委托函数：
public delegate void QAVDownloadFileCompleteCallback(int code, string filepath, string fileid);
事件函数：
public abstract event QAVDownloadFileCompleteCallback OnDownloadFileComplete;
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| code    |int                       |当code为0时，录制完成|
| filepath    |string                      |录制的存放地址|
| fileid    |string                      |文件的url路径|
> 示例代码  
```
对事件进行监听：
IQAVContext.GetInstance().GetPttCtrl().OnDownloadFileComplete += mInnerHandler;
监听处理：
void mInnerHandler(int code, string filepath, string fileid){
    //下载语音文件完成回调
}
```



### 播放语音
此接口用于播放语音。
> 函数原型  
```
IQAVPTT PlayRecordedFile (string downloadFilePath)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| downloadFilePath    |string                       |文件的路径|
> 示例代码  
```
IQAVContext.GetInstance().GetPttCtrl().PlayRecordedFile(filePath); 
```


### 播放语音的回调
播放语音的回调，通过委托传递消息。
> 函数原型  
```
委托函数：
public delegate void QAVPlayFileCompleteCallback(int code, string filepath);
事件函数：
public abstract event QAVPlayFileCompleteCallback OnPlayFileComplete;
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| code    |int                       |当code为0时，录制完成|
| filepath    |string                      |录制的存放地址|

> 示例代码  
```
对事件进行监听：
IQAVContext.GetInstance().GetPttCtrl().OnPlayFileComplete += mInnerHandler;
监听处理：
void mInnerHandler(int code, string filepath){
    //播放语音的回调
}
```




### 停止播放语音
此接口用于停止播放语音。
> 函数原型  
```
IQAVPTT int StopPlayFile()
```

> 示例代码  
```
IQAVContext.GetInstance().GetPttCtrl().StopPlayFile();
```



### 获取语音文件的大小
通过此接口，获取语音文件的大小。
> 函数原型  
```
IQAVPTT GetFileSize(string filePath) 
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    |string                      |语音文件的路径|
> 示例代码  
```
int fileSize = IQAVContext.GetInstance().GetPttCtrl().GetFileSize(filepath);
```

### 获取语音文件的时长
此接口用于获取语音文件的时长。
> 函数原型  
```
IQAVPTT int GetVoiceFileDuration(string filePath)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    |string                      |语音文件的路径|
> 示例代码  
```
int fileDuration = IQAVContext.GetInstance().GetPttCtrl().GetVoiceFileDuration(filepath);
```


### 将指定的语音文件翻译成文字
此接口用于将指定的语音文件翻译成文字。
> 函数原型  
```
IQAVPTT int SpeechToText(String fileID)
IQAVPTT int SpeechToText(String fileID,String language)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| fileID    |string                      |语音文件的 url|
| language    |string                      |语言id，参数为“KR-KR”,“ZH-ZH”|

> 示例代码  
```
IQAVContext.GetInstance().GetPttCtrl().SpeechToText(fileID);
```

### 翻译回调
将指定的语音文件翻译成文字，通过委托传递消息。
> 函数原型  
```
委托函数：
public delegate void QAVSpeechToTextCallback(int code, string fileid, string result);
事件函数：
public abstract event QAVSpeechToTextCallback OnSpeechToTextComplete;
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| code    |int                       |当code为0时，录制完成|
| filepath    |string                      |录制的存放地址|
| result    |string                      |转换的文本结果|
> 示例代码  
```
对事件进行监听：
IQAVContext.GetInstance().GetPttCtrl().OnSpeechToTextComplete += mInnerHandler;
监听处理：
void mInnerHandler(int code, string fileid, string result){
    //播放语音的回调
}
```