## 简介

欢迎使用腾讯云游戏多媒体引擎 SDK 。为方便 Android 开发者调试和接入腾讯云游戏多媒体引擎产品 API，这里向您介绍适用于 Android 开发的接入技术文档。

## SDK初始化  

### 初始化
通过 TMGContext 完成初始化。    

> 函数原型 

```
public static ITMGContext GetInstance(Context context)
```

|参数     | 类型         |意义|
| ------------- |:-------------:|-------------|
| context    |Context |应用程序上下文对象|


> 示例代码  

```
import com.tencent.TMG.ITMGContext; 
TMGContext.getInstance(this);
```

### 注册回调
将回调函数注册给 SDK，用于接受回调的信息。
首先需要声明一个回调函数。
> 函数原型
```
public abstract static class ITMGDelegate {
        public ITMGDelegate() {
        }

        public void OnEvent(ITMGContext.ITMG_MAIN_EVENT_TYPE type, Intent data) {
        }
}
```
> 示例代码 
```
private ITMGContext.ITMGDelegate itmgDelegate = null;
```
在构造函数中重写这个回调函数，对回调的参数进行处理。

|参数     | 类型         |意义|
| ------------- |:-------------:| ------------- |
| type    	|ITMGContext.ITMG_MAIN_EVENT_TYPE 	|回调的事件类型				|
| data    	|Intent 消息类型  						|回调的相关信息，事件数据	|

回调事件列表：

|消息     | 消息代表的意义   
| ------------- |:-------------:|
|ITMG_MAIN_EVENT_TYPE_ENTER_ROOM    				       |进入音视频房间消息
|ITMG_MAIN_EVENT_TYPE_EXIT_ROOM    				         	|退出音视频房间消息
|ITMG_MAIN_EVENT_TYPE_ROOM_DISCONNECT    		       |房间因为网络等原因断开消息
|ITMG_MAIN_EVENT_TYPE_CHANGE_ROOM_TYPE				|房间类型变化事件
|ITMG_MAIN_EVENT_TYPE_ENABLE_MIC    				       |打开麦克风消息
|ITMG_MAIN_EVENT_TYPE_DISABLE_MIC    				       |关闭麦克风消息
|ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER				       |打开扬声器消息
|ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER				       |关闭扬声器消息
|ITMG_MAIN_EVENT_TYPE_ACCOMPANY_FINISH			       |伴奏结束消息
|ITMG_MAIN_EVNET_TYPE_USER_UPDATE					  	|房间成员更新消息
|ITMG_MAIN_EVNET_TYPE_PTT_RECORD_COMPLETE		       |PTT 录音完成
|ITMG_MAIN_EVNET_TYPE_PTT_UPLOAD_COMPLETE		       |上传 PTT 完成
|ITMG_MAIN_EVNET_TYPE_PTT_DOWNLOAD_COMPLETE	       |下载 PTT 完成
|ITMG_MAIN_EVNET_TYPE_PTT_PLAY_COMPLETE			       |播放 PTT 完成
|ITMG_MAIN_EVNET_TYPE_PTT_SPEECH2TEXT_COMPLETE	   	|语音转文字完成

Data 列表：

|消息     | Data         |例子|
| ------------- |:-------------:|------------- |
| ITMG_MAIN_EVENT_TYPE_ENTER_ROOM    				|result; error_info					|{"error_info":"","result":0}
| ITMG_MAIN_EVENT_TYPE_EXIT_ROOM    				|result; error_info  					|{"error_info":"","result":0}
| ITMG_MAIN_EVENT_TYPE_ROOM_DISCONNECT    		|result; error_info  					|{"error_info":"waiting timeout, please check your network","result":0}
| ITMG_MAIN_EVENT_TYPE_CHANGE_ROOM_TYPE    		|result; error_info; new_room_type	|{"error_info":"","new_room_type":0,"result":0}
| ITMG_MAIN_EVENT_TYPE_ENABLE_MIC    				|result; error_info  					|{"error_info":"","result":0}
| ITMG_MAIN_EVENT_TYPE_DISABLE_MIC    				|result; error_info  					|{"error_info":"","result":0}
| ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER    			|result; error_info  					|{"error_info":"","result":0}
| ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER    			|result; error_info  					|{"error_info":"","result":0}
| ITMG_MAIN_EVENT_TYPE_SPEAKER_NEW_DEVICE		|result; error_info  					|{"deviceID":"{0.0.0.00000000}.{a4f1e8be-49fa-43e2-b8cf-dd00542b47ae}","deviceName":"扬声器 (Realtek High Definition Audio)","error_info":"","isNewDevice":true,"isUsedDevice":false,"result":0}
| ITMG_MAIN_EVENT_TYPE_SPEAKER_LOST_DEVICE    		|result; error_info  					|{"deviceID":"{0.0.0.00000000}.{a4f1e8be-49fa-43e2-b8cf-dd00542b47ae}","deviceName":"扬声器 (Realtek High Definition Audio)","error_info":"","isNewDevice":false,"isUsedDevice":false,"result":0}
| ITMG_MAIN_EVENT_TYPE_MIC_NEW_DEVICE    			|result; error_info  					|{"deviceID":"{0.0.1.00000000}.{5fdf1a5b-f42d-4ab2-890a-7e454093f229}","deviceName":"麦克风 (Realtek High Definition Audio)","error_info":"","isNewDevice":true,"isUsedDevice":true,"result":0}
| ITMG_MAIN_EVENT_TYPE_MIC_LOST_DEVICE    			|result; error_info 					|{"deviceID":"{0.0.1.00000000}.{5fdf1a5b-f42d-4ab2-890a-7e454093f229}","deviceName":"麦克风 (Realtek High Definition Audio)","error_info":"","isNewDevice":false,"isUsedDevice":true,"result":0}
| ITMG_MAIN_EVNET_TYPE_USER_UPDATE    				|user_list;event_id					|{"event_id":1,"user_list":["0"]}
| ITMG_MAIN_EVNET_TYPE_PTT_RECORD_COMPLETE 		|result; file_path  					|{"filepath":"","result":0}
| ITMG_MAIN_EVNET_TYPE_PTT_UPLOAD_COMPLETE 		|result; file_path;file_id  				|{"file_id":"","filepath":"","result":0}
| ITMG_MAIN_EVNET_TYPE_PTT_DOWNLOAD_COMPLETE	|result; file_path;file_id  				|{"file_id":"","filepath":"","result":0}
| ITMG_MAIN_EVNET_TYPE_PTT_PLAY_COMPLETE 			|result; file_path  					|{"filepath":"","result":0}
| ITMG_MAIN_EVNET_TYPE_PTT_SPEECH2TEXT_COMPLETE	|result; file_path;file_id				|{"file_id":"","filepath":"","result":0}

> 示例代码  
```
itmgDelegate= new ITMGContext.ITMGDelegate() {
            @Override
 			public void OnEvent(ITMGContext.ITMG_MAIN_EVENT_TYPE type, Intent data) {
                }
        };
```
**即所有回调函数都会通过 OnEvent 函数进行处理。**  

将回调函数注册给 SDK，要在进房之前设置。
> 函数原型 
```
ITMGContext public int SetTMGDelegate(ITMGDelegate delegate)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| delegate    |ITMGDelegate |SDK 回调函数|
> 示例代码  
```
TMGContext.GetInstance(this).SetTMGDelegate(itmgDelegate);
```

### 设置基本信息
获取相关信息，由腾讯云控制台申请，详情见[游戏多媒体引擎接入指引](https://github.com/TencentMediaLab/GME/blob/master/GME%20Introduction.md)。
此函数需要来自腾讯云控制台的 SdkAppId 号码作为参数，再加上 Id，这个 Id 是唯一标识一个用户，规则由 App 开发者自行制定，App 内不重复即可（目前只支持 INT64）。
> 函数原型 
```
ITMGContext public int SetAppInfo(String sdkAppId, String openID)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| sdkAppId    	|String  |来自腾讯云控制台的 SdkAppId 号码			|
| openID    		|String  |唯一标识一个用户，规则由 App 开发者自行制定，目前只支持大于10000的数字类型|
> 示例代码  
```
ITMGContext.GetInstance(this).SetAppInfo(sdkAppId, identifier);
```

### 设置版本信息
设置版本信息，用于查 Log 信息及 Bug 时使用。方便后台统计, 策略调整等（不设置不影响功能）。
> 函数原型
```
ITMGContext public void SetAppVersion(String sAppVersion)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| sAppVersion    |String  |版本号|
> 示例代码  
```
ITMGContext.GetInstance(this).SetAppVersion(appVersion);
```

### 获取版本号
获取 SDK 版本号，用于分析。
> 函数原型
```
ITMGContext public void GetSDKVersion()
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetSDKVersion();
```

### 实时语音鉴权信息
接下来是生成 AuthBuffer，用于相关功能的加密和鉴权，相关参数获取及详情见[游戏多媒体引擎接入指引](https://github.com/TencentMediaLab/GME/blob/master/GME%20Introduction.md)。  
>注意：在加入房间之前需要 AuthBuffer 作为参数。

该函数返回值为 Byte[] 类型。
> 函数原型
```
AuthBuffer public native byte[] genAuthBuffer(int sdkAppId, int roomId, String identifier, String key, int expTime, int authBits)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| appId    		|int   	|来自腾讯云控制台的 SdkAppId 号码		|
| roomId    		|int   	|要加入的房间名							|
| identifier    	|String   |用户标识								|
| key    			|string   	|来自腾讯云控制台的密钥					|
| expTime    		|int   	|authBuffer 超时时间						|
| authBits    		|int    	|权限									|

>关于权限  
>
AUTH_BITS_ALL 代表拥有全部权限，建议实时用户、主播使用，AUTH_BITS_RECV 代表下行权限，建议纯听众、观众使用，不能使用startAccompany。

> 示例代码  
```
import com.tencent.av.sig.AuthBuffer;//头文件
long nExpUTCTime = 1800 + System.currentTimeMillis() / 1000L;
byte[] authBuffer=AuthBuffer.getInstance().genAuthBuffer(Integer.parseInt(sdkAppId), Integer.parseInt(strRoomID),identifier, key, (int)nExpUTCTime, (int) ITMGContext.ITMG_AUTH_BITS_DEFAULT);
```

### 设置最大混音路数
最后是设置最大混音路数（同时听到多少人讲话），在进房前调用，不调用默认为 6 路混音。
> 函数原型
```
ITMGContext void SetRecvMixStreamCount(int nCount)
```

|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| nCount    |int   |混音路数，默认为6|
> 示例代码  
```
IQAVContext.GetInstance(this).SetRecvMixStreamCount(nCount);
```

## 实时语音房间事件接口


### 加入房间
用生成的鉴权信息进房，会收到消息为 ITMG_MAIN_EVENT_TYPE_ENTER_ROOM 的回调。
>注意:
>1、加入房间默认不打开麦克风及扬声器。
>2、在 EnterRoom 函数调用之前要先调用 SetAppInfo 函数及 SetAppVersion 函数进行相关信息的设置。

> 函数原型
```
ITMGContext public abstract void  EnterRoom(int relationId, int roomType, byte[] authBuffer)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| relationId 	|int		|房间号				|
| roomType 	|int		|房间音频类型		|
| authBuffer	|byte[]	|鉴权码				|

|音频类型     	|含义|参数|
| ------------- |------------ | ---- |
| ITMG_ROOM_TYPE_FLUENCY			|流畅音质	|1
| ITMG_ROOM_TYPE_STANDARD			|标准音质	|2
| ITMG_ROOM_TYPE_HIGHQUALITY		|高清音质	|3

> 示例代码  
```
ITMGContext.GetInstance(this).EnterRoom(Integer.parseInt(relationId),roomType, authBuffer);    
```

### 加入房间事件的回调
加入房间完成后会判断是否有注册回调函数，如果有，通讯管理器 TMGContext 会调用相应事件的回调函数。例如加入房间事件就会调用 onEnterRoomComplete 函数，退出房间会调用 onExitRoomComplete 函数。
> 代码说明
```
public void onEnterRoomComplete(int nRet, String strErrMsg) {         
            if(TMGContext.this.mTmgDelegate != null) {
                Intent it= TMGCallbackHelper.GetCallBackIntent(nRet, strErrMsg);
                TMGContext.this.mTmgDelegate.OnEvent(ITMG_MAIN_EVENT_TYPE.ITMG_MAIN_EVENT_TYPE_ENTER_ROOM, it);
            } else {
                TMGContext.this.HandleUnExceptionEnd(nRet, strErrMsg);
            }
        }
```
> 示例代码  
```
public void OnEvent(ITMGContext.ITMG_MAIN_EVENT_TYPE type, Intent data) {
	int nErrCode =data.getIntExtra("result" , -1);
	if (nErrCode == AVError.AV_OK)
		{
			//进入房间成功
		}
	}
```


### 判断是否已经进入房间
通过调用此接口可以判断是否已经进入房间，返回值为 bool 类型。
> 函数原型  
```
ITMGContext public boolean IsRoomEntered()
```
> 示例代码  
```
ITMGContext.GetInstance(this).IsRoomEntered();
```

### 退出房间
通过调用此接口可以退出所在房间。
> 函数原型  
```
ITMGContext public void ExitRoom()
```
> 示例代码  
```
ITMGContext.GetInstance(this).ExitRoom();
```

### 退出房间回调
退出房间事件调用完成后，会调用函数 OnEvent，事件消息为ITMG_MAIN_EVENT_TYPE_EXIT_ROOM，在 OnEvent 函数中对事件消息进行判断。
> 示例代码  
```
public void OnEvent(ITMGContext.ITMG_MAIN_EVENT_TYPE type, Intent data) {
	if (ITMGContext.ITMG_MAIN_EVENT_TYPE.ITMG_MAIN_EVENT_TYPE_EXIT_ROOM == type)
        {
            //收到退房成功事件
        }
}
```

### 修改用户房间音频类型
此接口用于修改用户房间音频类型，结果参见回调事件，事件类型为 ITMG_MAIN_EVENT_TYPE_CHANGE_ROOM_TYPE。
> 函数原型  
```
IITMGContext TMGRoom public void ChangeRoomType(int nRoomType)
```


|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| nRoomType    |int    |希望房间切换成的类型，房间音频类型参考 EnterRoom 接口|

> 示例代码  
```
ITMGContext.GetInstance(this).GetRoom().ChangeRoomType(nRoomType);
```


### 获取用户房间音频类型
此接口用于获取用户房间音频类型，返回值为房间音频类型，房间音频类型参考 EnterRoom 接口。

> 函数原型  
```
IITMGContext TMGRoom public  int GetRoomType()
```

> 示例代码  
```
ITMGContext.GetInstance(this).GetRoom().GetRoomType();
```


### 房间类型完成回调
房间类型设置完成后，回调的事件消息为 ITMG_MAIN_EVENT_TYPE_CHANGE_ROOM_TYPE，返回的参数为 result、error_info 及 new_room_type，new_room_type 代表的信息如下，在 OnEvent 函数中对事件消息进行判断。

|事件子类型     | 代表参数   |含义|
| ------------- |:-------------:|-------------
| ITMG_ROOM_CHANGE_EVENT_ENTERROOM		|1 	|表示在进房的过程中，自带的音频类型与房间不符合，被修改为所进入房间的音频类型	|
| ITMG_ROOM_CHANGE_EVENT_START			|2	|表示已经在房间内，音频类型开始切换（例如调用 ChangeRoomType 接口后切换音频类型 ）|
| ITMG_ROOM_CHANGE_EVENT_COMPLETE		|3	|表示已经在房间，音频类型切换完成|
| ITMG_ROOM_CHANGE_EVENT_REQUEST			|4	|表示房间成员调用 ChangeRoomType 接口，请求切换房间音频类型|	


> 示例代码  
```
public void OnEvent(ITMGContext.ITMG_MAIN_EVENT_TYPE type, Intent data) {
	if (ITMGContext.ITMG_MAIN_EVENT_TYPE.ITMG_MAIN_EVENT_TYPE_CHANGE_ROOM_TYPE == type)
        {
		//对房间类型事件进行处理
	 }
}
```

### 成员状态变化
该事件在状态变化才通知，状态不变化的情况下不通知。如需实时获取成员状态，请在上层收到通知时缓存，事件消息为 ITMG_MAIN_EVNET_TYPE_USER_UPDATE，参数 intent 包含两个信息，event_id 及 user_list，在 OnEvent 函数中对事件消息进行判断。


|event_id     | 含义         |应用侧维护内容|
| ------------- |:-------------:|-------------|
|ITMG_EVENT_ID_USER_ENTER    				|有成员进入房间			|应用侧维护成员列表		|
|ITMG_EVENT_ID_USER_EXIT    				|有成员退出房间			|应用侧维护成员列表		|
|ITMG_EVENT_ID_USER_HAS_AUDIO    		|有成员发送音频包		|应用侧维护通话成员列表	|
|ITMG_EVENT_ID_USER_NO_AUDIO    			|有成员停止发送音频包	|应用侧维护通话成员列表	|

> 示例代码  
```
public void OnEvent(ITMGContext.ITMG_MAIN_EVENT_TYPE type, Intent data) {
	if (ITMGContext.ITMG_MAIN_EVENT_TYPE.ITMG_MAIN_EVNET_TYPE_USER_UPDATE == type)
        {
		//更新成员状态
		int nEventID = data.getIntExtra("event_id", 0);
		String[] identifierList =data.getStringArrayExtra("user_list");
		 switch (nEventID)
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
		    case ITMG_EVENT_ID_USER_HAS_CAMERA_VIDEO:
			    //有成员开启摄像头
			    break;
		    case ITMG_EVENT_ID_USER_NO_CAMERA_VIDEO:
			    //有成员关闭摄像头
			    break;
		    default:
			    break;
 		    }
	}
}
```

### 获取诊断信息
获取音视频通话的实时通话质量的相关信息。该函数主要用来查看实时通话质量、排查问题等，业务侧可以忽略。
> 函数原型  
```
IITMGContext TMGRoom public String GetQualityTips() 
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetRoom().GetQualityTips();
```

## 实时语音音频接口

### 暂停音频引擎的采集和播放
调用此接口暂停音频引擎的采集和播放，只在进房后有效。
在 EnterRoom 函数调用成功之后之后就会占用麦克风权限，期间其他程序无法进行麦克风采集。
注意：调用 EnableMic(false) 无法释放麦克风占用。
如果确实需要释放麦克风，请调用 PauseAudio 函数。调用 PauseAudio 函数后会整个暂停引擎，调用 ResumeAudio 函数可恢复音频采集。
> 函数原型  
```
ITMGContext ITMGAudioCtrl public int PauseAudio()
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioCtrl().PauseAudio();
```

### 恢复音频引擎的采集和播放
调用此接口恢复音频引擎的采集和播放，只在进房后有效。
> 函数原型  
```
ITMGContext ITMGAudioCtrl public int ResumeAudio()
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioCtrl().ResumeAudio();
```

### 开启、关闭音频数据黑名单逻辑
开启黑名单时每次调用黑名单函数，黑名单将被重置为新的成员列表，而不是累加。需要定制所需接收的音频数据才调用，不调用则默认接收房间内所有音频数据。返回值为 0 表示调用失败。
> 函数原型  
```
ITMGContext TMGRoom public int SetAudioBlackList(String[] identifierList)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| identifierList    |String[]     |黑名单列表|
> 示例代码  
```
ITMGContext.GetInstance(this).GetRoom().SetAudioBlackList(users);
```




### 麦克风开启关闭事件
此接口用来开启及关闭麦克风。
>注意:加入房间默认不打开麦克风及扬声器。

> 函数原型  
```
ITMGContext public void EnableMic(boolean isEnabled)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| isEnabled    |boolean     |如果需要关闭麦克风，则传入的参数为 false，如果打开麦克风，则参数为 true|
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioCtrl().EnableMic(true);
ITMGContext.GetInstance(this).GetAudioCtrl().EnableMic(false);
```

### 麦克风事件的回调
麦克风事件的回调调用函数 OnEvent，事件消息为 ITMG_MAIN_EVENT_TYPE_ENABLE_MIC， ITMG_MAIN_EVENT_TYPE_DISABLE_MIC，在 OnEvent 函数中对事件消息进行判断。
传递的参数 intent 包含两个信息，一个是 audio_state，另一个是 audio_errcode。
> 示例代码  
```
public void OnEvent(ITMGContext.ITMG_MAIN_EVENT_TYPE type, Intent data) {
	if (ITMGContext.ITMG_MAIN_EVENT_TYPE.ITMG_MAIN_EVENT_TYPE_ENABLE_MIC == type)
        {
		nErrCode = data.getIntExtra("audio_errcode", 0);
		if (nErrCode == AVError.AV_OK)
            		{
				//打开麦克风成功
			}
	}
	if (ITMGContext.ITMG_MAIN_EVENT_TYPE.ITMG_MAIN_EVENT_TYPE_DISABLE_MIC == type)
        {
		nErrCode = data.getIntExtra("audio_errcode", 0);
		if (nErrCode == AVError.AV_OK)
            		{
				//关闭麦克风成功
			}
	}
}
```
### 麦克风状态获取
此接口用于获取麦克风状态，返回值 0 为关闭麦克风状态，返回值 1 为打开麦克风状态，返回值 2 为麦克风设备正在操作中，返回值 4 为设备没初始化好。
> 函数原型  
```
ITMGContext TMGAudioCtrl public int GetMicState() 
```
> 示例代码  
```
int micState = ITMGContext.GetInstance(this).GetAudioCtrl().GetMicState();
```

### 获取麦克风实时音量
此接口用于获取麦克风实时音量，返回值为 int 类型。
> 函数原型  
```
ITMGContext TMGAudioCtrl public int GetMicLevel() 
```
> 示例代码  
```
int micLevel = ITMGContext.GetInstance(this).GetAudioCtrl().GetMicLevel();
```

### 设置麦克风的软件音量
此接口用于设置麦克风的软件音量。参数 volume 用于设置麦克风的软件音量，当数值为 0 的时候表示静音，当数值为 100 的时候表示音量不增不减，默认数值为 100。
> 函数原型  
```
ITMGContext TMGAudioCtrl public int SetMicVolume(int volume) 
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| volume    |int      |设置音量，范围 0 到 150|
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioCtrl().SetMicVolume(volume);
```
###  获取麦克风的软件音量
此接口用于获取麦克风的软件音量。返回值为一个int类型数值。
> 函数原型  
```
ITMGContext TMGAudioCtrl public int GetMicVolume()
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioCtrl().GetMicVolume();
```

### 扬声器开启关闭事件
此接口用于设置扬声器开启关闭。
> 函数原型  
```
ITMGContext public void EnableSpeaker(boolean isEnabled)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| isEnabled    |boolean       |如果需要关闭扬声器，则传入的参数为 false，如果打开扬声器，则参数为 true|
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioCtrl().EnableSpeaker(true);
ITMGContext.GetInstance(this).GetAudioCtrl().EnableSpeaker(false);
```

### 扬声器事件的回调
扬声器事件的回调调用函数 OnEvent，事件消息为 ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER， ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER。
传递的参数 intent 包含两个信息，一个是 audio_state，另一个是 audio_errcode。
> 示例代码  
```
public void OnEvent(ITMGContext.ITMG_MAIN_EVENT_TYPE type, Intent data) {
	if (ITMGContext.ITMG_MAIN_EVENT_TYPE.ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER == type)
        {
		nErrCode = data.getIntExtra("audio_errcode", 0);
		if (nErrCode == AVError.AV_OK)
            		{
				//打开扬声器成功
			}
	}
	if (ITMGContext.ITMG_MAIN_EVENT_TYPE.ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER == type)
        {
		nErrCode = data.getIntExtra("audio_errcode", 0);
		if (nErrCode == AVError.AV_OK)
            		{
				//关闭扬声器成功
			}
	}
}
```

### 扬声器状态获取
此接口用于扬声器状态获取。返回值 0 为关闭扬声器状态，返回值 1 为打开扬声器状态，返回值 2 为扬声器设备正在操作中，返回值 4 为设备没初始化好。
> 函数原型  
```
ITMGContext TMGAudioCtrl public int GetSpeakerState() 
```

> 示例代码  
```
int micState = ITMGContext.GetInstance(this).GetAudioCtrl().GetSpeakerState();
```

### 获取扬声器实时音量
此接口用于获取扬声器实时音量。返回值为 int 类型数值，表示扬声器实时音量。
> 函数原型  
```
ITMGContext TMGAudioCtrl public int GetSpeakerLevel() 
```

> 示例代码  
```
int SpeakLevel = ITMGContext.GetInstance(this).GetAudioCtrl().GetSpeakerLevel();
```

### 设置扬声器的软件音量
此接口用于设置扬声器的软件音量。
>注意：参数 volume 用于设置扬声器的软件音量，当数值为 0 的时候表示静音，当数值为 100 的时候表示音量不增不减，默认数值为 100。

> 函数原型  
```
ITMGContext TMGAudioCtrl public int SetSpeakerVolume(int volume) 
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| volume    |int        |设置音量，范围 0 到 150|
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioCtrl().SetSpeakerVolume(volume);
```

### 获取扬声器的软件音量
此接口用于获取扬声器的软件音量。返回值为 int 类型数值，代表扬声器的软件音量。
>注意：Level 是实时音量，Volume 是扬声器的软件音量，最终声音音量相当于 Level*Volume%。举个例子：实时音量是数值是 100 的话，此时Volume的数值是 60，那么最终发出来的声音数值也是 60。

> 函数原型  
```
ITMGContext TMGAudioCtrl public int GetSpeakerVolume()
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioCtrl().GetSpeakerVolume();
```


### 启动耳返
此接口用于启动耳返。
> 函数原型  
```
ITMGContext TMGAudioCtrl public int EnableLoopBack(boolean enable)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| enable    |boolean         |设置是否启动|
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioCtrl().EnableLoopBack(true);
```

## 实时语音伴奏相关接口

### 开始播放伴奏
调用此函数开始播放伴奏。支持 m4a、AAC、wav、mp3 一共四种格式。
注意：1、调用此 API，音量会重置。
2、下行权限不能启用此 API。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int StartAccompany(String filePath, boolean loopBack, int loopCount) 
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    	|String    	|播放伴奏的路径					|
| loopBack  	|boolean    	|是否混音发送，一般都设置为 true，即其他人也能听到伴奏	|
| loopCount	|int    		|循环次数，数值为 -1 表示无限循环	|
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().StartAccompany(filePath,true,loopCount,duckerTimeMs);
```

### 播放伴奏的回调
开始播放伴奏完成后，回调函数调用 OnEvent，事件消息为 ITMG_MAIN_EVENT_TYPE_ACCOMPANY_FINISH，在 OnEvent 函数中对事件消息进行判断。
传递的参数 intent 包含两个信息，一个是 result，另一个是 file_path。
> 示例代码  
```
public void OnEvent(ITMGContext.ITMG_MAIN_EVENT_TYPE type, Intent data) {
	if (ITMGContext.ITMG_MAIN_EVENT_TYPE.ITMG_MAIN_EVENT_TYPE_ACCOMPANY_FINISH == type)
        {
		//播放伴奏的事件回调
	}
}
```

### 停止播放伴奏
调用此接口停止播放伴奏。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int StopAccompany(int duckerTimeMs)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| duckerTimeMs    |int             |淡出时间|

> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().StopAccompany(duckerTimeMs);
```

### 伴奏是否播放完毕
如果播放完毕，返回值为 true，如果没播放完，返回值为 false。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public boolean IsAccompanyPlayEnd() 
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().IsAccompanyPlayEnd();
```


### 暂停播放伴奏
调用此接口暂停播放伴奏。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int PauseAccompany()
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().PauseAccompany();
```


### 重新播放伴奏
此接口用于重新播放伴奏。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int ResumeAccompany()
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().ResumeAccompany();
```

### 设置自己是否可以听到伴奏
此接口用于设置自己是否可以听到伴奏。
> 函数原型  

```
ITMGContext TMGAudioEffectCtrl public int EnableAccompanyPlay(boolean enable)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| enable    |BOOL             |是否能听到|
> 示例代码  

```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().EnableAccompanyPlay(true);
```

### 设置他人是否也可以听到伴奏
设置他人是否也可以听到伴奏。
> 函数原型  

```
ITMGContext TMGAudioEffectCtrl public int EnableAccompanyLoopBack(boolean enable)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| enable    |BOOL             |是否能听到|

> 示例代码  

```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().EnableAccompanyLoopBack(true);
```



### 设置伴奏音量
设置 DB 音量，为线性音量，默认值为 100，数值大于 100 音量增益，数值小于 100 音量减益，值域为 0到200。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int SetAccompanyVolume(int vol)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| vol    |int             |音量数值|

> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().SetAccompanyVolume(Volume);
```

### 获取播放伴奏的音量
此接口用于获取 DB 音量。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int GetAccompanyVolume()
```
> 示例代码  
```
string currentVol = "VOL: " + ITMGContext.GetInstance(this).GetAudioEffectCtrl().GetAccompanyVolume();
```

### 获得伴奏播放进度
以下两个函数用于获得伴奏播放进度。需要注意：Current / Total = 当前循环次数，Current % Total = 当前循环播放位置。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public long GetAccompanyFileTotalTimeByMs()
ITMGContext TMGAudioEffectCtrl public long GetAccompanyFileCurrentPlayedTimeByMs()
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().GetAccompanyFileTotalTimeByMs();
ITMGContext.GetInstance(this).GetAudioEffectCtrl().GetAccompanyFileCurrentPlayedTimeByMs();
```


### 设置播放进度
此接口用于设置播放进度。。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int SetAccompanyFileCurrentPlayedTimeByMs(long time)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| time    |long                |播放进度|

> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().SetAccompanyFileCurrentPlayedTimeByMs(time);
```


## 实时语音音效相关接口




### 播放音效
此接口用于播放音效。参数中音效 id 需要 App 侧进行管理，唯一标识一个独立文件。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int PlayEffect(int soundId, String filePath, boolean loop) 
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId    	|int    		|音效 id|
| filePath    	|String		|音效路径|
| loop    		|boolean	|是否重复播放|
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().PlayEffect(soundId,filePath,loop);
```


### 暂停播放音效
此接口用于暂停播放音效。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int PauseEffect(int soundId)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId    |int                    |音效 id|

> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().PauseEffect(soundId);
```

### 暂停所有音效
调用此接口暂停所有音效。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int PauseAllEffects()
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().PauseAllEffects();
```

### 重新播放音效
此接口用于重新播放音效。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int ResumeEffect(int soundId)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId    |int                    |音效 id|
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().ResumeEffect(soundId);
```



### 重新播放所有音效
调用此接口重新播放所有音效。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int ResumeAllEffects()
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().ResumeAllEffects();
```

### 停止播放音效
此接口用于停止播放音效。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int StopEffect(int soundId)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId    |int                    |音效 id|
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().StopEffect(soundId);
```

### 停止播放所有音效
调用此接口停止播放所有音效。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int StopAllEffects()
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().StopAllEffects();
```

### 变声特效
调用此接口设置变声特效。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl  public int setVoiceType(int type);
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| type    |int                    |表示本端音频变声类型|


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
ITMGContext.GetInstance(this).GetAudioEffectCtrl().setVoiceType(0);
```



### 获取播放音效的音量
获取播放音效的音量，为线性音量，默认值为 100，数值大于 100 为增益效果，数值小于 100 为减益效果。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int GetEffectsVolume()
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().GetEffectsVolume();
```


### 设置播放音效的音量
调用此接口设置播放音效的音量。
> 函数原型  
```
ITMGContext TMGAudioEffectCtrl public int SetEffectsVolume(int volume)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| volume    |int                    |音量数值|

> 示例代码  
```
ITMGContext.GetInstance(this).GetAudioEffectCtrl().SetEffectsVolume(Volume);
```






## 离线语音接入




### 1.离线语音技术接入初始化
初始化需要传入鉴权 access token 给 TLS 相关函数。鉴权的获取详细流程见[游戏多媒体引擎密钥文档](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/GME%20Key%20Manual.md)。  
。
> 函数原型  
```
TlsSig public String getTLSSig(int sdkAppId, String openID, String key) 
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| sdkAppId  	|int           	|来自腾讯云控制台的 SdkAppId 号码|
| openID    	|String   	|唯一标识一个用户，规则由 App 开发者自行制定|
| key    		|String      	|来自腾讯云控制台的鉴权|

```
ITMGPTT public int ApplyAccessToken(String accessToken)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| accessToken    |String                       |getTLSSig 函数返回的 accessToken|
> 示例代码  
```
private String GetAccessToken(String appid,String userid){
	String key ="";//填入自己的鉴权
	return TlsSig.getInstance().getTLSSig(Integer.parseInt(appid),userid,key);
}

//调用
String sig = GetAccessToken(sdkAppId,identifier);
	if (sig!=null){
		ITMGContext.GetInstance(this).GetPTT().ApplyAccessToken(sig);
	}else {
		Log.e("Sig","SigError");
	}
```

### 限制最大语音信息时长
限制最大语音消息的长度，最大支持 60 秒。
> 函数原型  
```
ITMGContext TMGPTT public void SetMaxMessageLength(int msTime)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| msTime    |int                    |语音时长|
> 示例代码  
```
ITMGContext.GetInstance(this).GetPTT().SetMaxMessageLength(msTime);
```


### 启动录音
此接口用于启动录音。
> 函数原型  
```
ITMGContext TMGPTT public void StartRecording(String fileDir)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| fileDir    |String                     |存放的语音路径|
> 示例代码  
```
ITMGContext.GetInstance(this).GetPTT().StartRecording(fileDir);
```

### 启动录音的回调
启动录音完成后的回调调用函数 OnEvent，事件消息为 ITMG_MAIN_EVNET_TYPE_PTT_RECORD_COMPLETE， 在 OnEvent 函数中对事件消息进行判断。
传递的参数包含两个信息，一个是 result，另一个是 file_path。

> 示例代码  
```
public void OnEvent(ITMGContext.ITMG_MAIN_EVENT_TYPE type, Intent data) {
	if (ITMGContext.ITMG_MAIN_EVENT_TYPE.ITMG_MAIN_EVNET_TYPE_PTT_RECORD_COMPLETE == type)
        	{
            		//录音回调
        	}
}
```

### 停止录音
此接口用于停止录音。
> 函数原型  
```
ITMGContext TMGPTT public int StopRecording()
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetPTT().StopRecording();
```



### 取消录音
调用此接口取消录音。
> 函数原型  
```
ITMGContext TMGPTT public int CancelRecording()
```
> 示例代码  
```
ITMGContext.GetInstance(this).GetPTT().CancelRecording();
```

### 上传语音文件
此接口用于上传语音文件。
> 函数原型  
```
ITMGContext TMGPTT public void UploadRecordedFile(String filePath)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    |String                      |上传的语音路径|
> 示例代码  
```
ITMGContext.GetInstance(this).GetPTT().UploadRecordedFile(filePath);
```


### 上传语音完成的回调
上传语音完成后，事件消息为 ITMG_MAIN_EVNET_TYPE_PTT_UPLOAD_COMPLETE， 在 OnEvent 函数中对事件消息进行判断。
传递的参数包含三个信息，result，file_path 和 file_id。
```
public void OnEvent(ITMGContext.ITMG_MAIN_EVENT_TYPE type, Intent data) {
	if(ITMGContext.ITMG_MAIN_EVENT_TYPE.ITMG_MAIN_EVNET_TYPE_PTT_UPLOAD_COMPLETE== type)
       	 {
           	//上传语音成功
       	 }
}
```


### 下载语音文件
此接口用于下载语音文件。
> 函数原型  
```
ITMGContext TMGPTT public void DownloadRecordedFile(String fileID, String downloadFilePath)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| fileID    			|String                      |文件的url路径	|
| downloadFilePath 	|String                      |文件的下载路径	|
> 示例代码  
```
ITMGContext.GetInstance(this).GetPTT().DownloadRecordedFile(url,path);
```


### 下载语音文件完成回调
下载语音完成后，事件消息为 ITMG_MAIN_EVNET_TYPE_PTT_DOWNLOAD_COMPLETE， 在 OnEvent 函数中对事件消息进行判断。
传递的参数包含三个信息，result、file_path 和 file_id。
```
public void OnEvent(ITMGContext.ITMG_MAIN_EVENT_TYPE type, Intent data) {
	if(ITMGContext.ITMG_MAIN_EVNET_TYPE_PTT_DOWNLOAD_COMPLETE== type)
        {
            //下载成功        
	}
}
```



### 播放语音
此接口用于播放语音。
> 函数原型  
```
ITMGContext TMGPTT public int PlayRecordedFile(String downloadFilePath) 
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| downloadFilePath    |String                      |文件的路径|
> 示例代码  
```
ITMGContext.GetInstance(this).GetPTT().PlayRecordedFile(downloadFilePath);
```


### 播放语音的回调
播放语音的回调，事件消息为 ITMG_MAIN_EVNET_TYPE_PTT_PLAY_COMPLETE， 在 OnEvent 函数中对事件消息进行判断。
传递的参数包含两个信息，一个是 result，另一个是 file_path。
```
public void OnEvent(ITMGContext.ITMG_MAIN_EVENT_TYPE type, Intent data) {
	if(ITMGContext.ITMG_MAIN_EVNET_TYPE_PTT_PLAY_COMPLETE== type)
       	 	{
			//播放语音的回调 
		}
}
```




### 停止播放语音
此接口用于停止播放语音。
> 函数原型  
```
ITMGContext TMGPTT public int StopPlayFile()
```

> 示例代码  
```
ITMGContext.GetInstance(this).GetPTT().StopPlayFile();
```



### 获取语音文件的大小
通过此接口，获取语音文件的大小。
> 函数原型  
```
ITMGContext TMGPTT public int GetFileSize(String filePath)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    |String                     |语音文件的路径|
> 示例代码  
```
ITMGContext.GetInstance(this).GetPTT().GetFileSize(path);
```

### 获取语音文件的时长
此接口用于获取语音文件的时长。。
> 函数原型  
```
ITMGContext TMGPTT public int GetVoiceFileDuration(String filePath)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    |String                     |语音文件的路径|
> 示例代码  
```
ITMGContext.GetInstance(this).GetPTT().GetVoiceFileDuration(path);
```


### 将指定的语音文件翻译成文字
此接口用于将指定的语音文件翻译成文字。
> 函数原型  
```
ITMGContext TMGPTT public int SpeechToText(String fileID)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| fileID    |String                     |语音文件 url|
> 示例代码  
```
ITMGContext.GetInstance(this).GetPTT().SpeechToText(fileID);
```

### 翻译回调
将指定的语音文件翻译成文字的回调，事件消息为 ITMG_MAIN_EVNET_TYPE_PTT_SPEECH2TEXT_COMPLETE， 在 OnEvent 函数中对事件消息进行判断。
传递的参数包含三个信息，result、file_path 和 text，其中 text 为翻译的文本。
```
public void OnEvent(ITMGContext.ITMG_MAIN_EVENT_TYPE type, Intent data) {
	if(ITMGContext.ITMG_MAIN_EVNET_TYPE_PTT_SPEECH2TEXT_COMPLETE == type)
       	 {
            //成功翻译语音文件       
	 }
}
```