## 简介
欢迎使用腾讯云游戏多媒体引擎 SDK 。为方便 iOS 开发者调试和接入腾讯云游戏多媒体引擎产品 API，这里向您介绍适用于 iOS 开发的接入技术文档。

**此文档对应GME sdk version：1.3.0.37965。**

## SDK初始化  

### 获取单例
GME 以单例的形式提供，所有调用都从 ITMGContext 开始。异步操作的结果或者内部状态的改变都通过 ITMGDelegate 传给应用。    
> 函数原型

```
ITMGContext ITMGDelegate <NSObject>
```
> 示例代码  

```
ITMGContext* _context = [ITMGContext GetInstance];
_context.TMGDelegate =self;
```

### 消息传递
GME 的消息通过 ITMGDelegate 传给应用，消息类型参考 ITMG_MAIN_EVENT_TYPE，消息内容为一个字典，不同的事件类型，消息内容也会不一样。
> 函数原型

```
- (void)OnEvent:(ITMG_MAIN_EVENT_TYPE)eventType data:(NSDictionary*)data
```
> 示例代码

```
-(void)OnEvent:(ITMG_MAIN_EVENT_TYPE)eventType data:(NSDictionary *)data{
    	NSLog(@"OnEvent:%lu,data:%@",(unsigned long)eventType,data);
		switch (eventType) {
			//对eventType进行判断
			}
	}
```

>消息列表：

|消息     | 消息代表的意义   
| ------------- |:-------------:|
|ITMG_MAIN_EVENT_TYPE_ENTER_ROOM    				       |进入音视频房间消息
|ITMG_MAIN_EVENT_TYPE_EXIT_ROOM    				         	|退出音视频房间消息
|ITMG_MAIN_EVENT_TYPE_ROOM_DISCONNECT    		       |房间因为网络等原因断开消息
|ITMG_MAIN_EVENT_TYPE_ENABLE_MIC    				       |打开麦克风消息
|ITMG_MAIN_EVENT_TYPE_DISABLE_MIC    				       |关闭麦克风消息
|ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER				       |打开扬声器消息
|ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER				       |关闭扬声器消息
|ITMG_MAIN_EVENT_TYPE_CHANGE_ROLE				      	|切换角色消息
|ITMG_MAIN_EVENT_TYPE_ACCOMPANY_FINISH			       |伴奏结束消息
|ITMG_MAIN_EVNET_TYPE_USER_UPDATE					  	|房间成员更新消息
|ITMG_MAIN_EVNET_TYPE_PTT_RECORD_COMPLETE		       |PTT 录音完成
|ITMG_MAIN_EVNET_TYPE_PTT_UPLOAD_COMPLETE		       |上传 PTT 完成
|ITMG_MAIN_EVNET_TYPE_PTT_DOWNLOAD_COMPLETE	       |下载 PTT 完成
|ITMG_MAIN_EVNET_TYPE_PTT_PLAY_COMPLETE			       |播放 PTT 完成
|ITMG_MAIN_EVNET_TYPE_PTT_SPEECH2TEXT_COMPLETE	   	|语音转文字完成

>Data 列表

|消息     | Data         |例子|
| ------------- |:-------------:|------------- |
| ITMG_MAIN_EVENT_TYPE_ENTER_ROOM    				|result; error_info		|{"error_info":"","result":0}
| ITMG_MAIN_EVENT_TYPE_EXIT_ROOM    				|result; error_info  		|{"error_info":"","result":0}
| ITMG_MAIN_EVENT_TYPE_ROOM_DISCONNECT    		|result; error_info  		|{"error_info":"waiting timeout, please check your network","result":0}
| ITMG_MAIN_EVENT_TYPE_ENABLE_MIC    				|result; error_info  		|{"error_info":"","result":0}
| ITMG_MAIN_EVENT_TYPE_DISABLE_MIC    				|result; error_info  		|{"error_info":"","result":0}
| ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER    			|result; error_info  		|{"error_info":"","result":0}
| ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER    			|result; error_info  		|{"error_info":"","result":0}
| ITMG_MAIN_EVENT_TYPE_SPEAKER_NEW_DEVICE		|result; error_info  		|{"deviceID":"{0.0.0.00000000}.{a4f1e8be-49fa-43e2-b8cf-dd00542b47ae}","deviceName":"扬声器 (Realtek High Definition Audio)","error_info":"","isNewDevice":true,"isUsedDevice":false,"result":0}
| ITMG_MAIN_EVENT_TYPE_SPEAKER_LOST_DEVICE    		|result; error_info  		|{"deviceID":"{0.0.0.00000000}.{a4f1e8be-49fa-43e2-b8cf-dd00542b47ae}","deviceName":"扬声器 (Realtek High Definition Audio)","error_info":"","isNewDevice":false,"isUsedDevice":false,"result":0}
| ITMG_MAIN_EVENT_TYPE_MIC_NEW_DEVICE    			|result; error_info  		|{"deviceID":"{0.0.1.00000000}.{5fdf1a5b-f42d-4ab2-890a-7e454093f229}","deviceName":"麦克风 (Realtek High Definition Audio)","error_info":"","isNewDevice":true,"isUsedDevice":true,"result":0}
| ITMG_MAIN_EVENT_TYPE_MIC_LOST_DEVICE    			|result; error_info 		|{"deviceID":"{0.0.1.00000000}.{5fdf1a5b-f42d-4ab2-890a-7e454093f229}","deviceName":"麦克风 (Realtek High Definition Audio)","error_info":"","isNewDevice":false,"isUsedDevice":true,"result":0}
| ITMG_MAIN_EVNET_TYPE_USER_UPDATE    				|user_list;  event_id		|{"event_id":1,"user_list":["0"]}
| ITMG_MAIN_EVNET_TYPE_PTT_RECORD_COMPLETE 		|result; file_path  		|{"filepath":"","result":0}
| ITMG_MAIN_EVNET_TYPE_PTT_UPLOAD_COMPLETE 		|result; file_path;file_id  	|{"file_id":"","filepath":"","result":0}
| ITMG_MAIN_EVNET_TYPE_PTT_DOWNLOAD_COMPLETE	|result; file_path;file_id  	|{"file_id":"","filepath":"","result":0}
| ITMG_MAIN_EVNET_TYPE_PTT_PLAY_COMPLETE 			|result; file_path  		|{"filepath":"","result":0}
| ITMG_MAIN_EVNET_TYPE_PTT_SPEECH2TEXT_COMPLETE	|result; file_path;file_id	|{"file_id":"","filepath":"","result":0}

### 设置 App 信息
获取相关信息，由腾讯云控制台申请，详情见[游戏多媒体引擎接入指引](https://github.com/TencentMediaLab/GME/blob/master/GME%20Introduction.md)。
此接口需要来自腾讯云控制台的 SdkAppId 号码作为参数，再加上 openId，这个 openId 是唯一标识一个用户，规则由 App 开发者自行制定，App 内不重复即可（目前只支持 INT64）。
> 函数原型

```
ITMGContext -(void)SetAppInfo:(NSString*)sdkAppID openID:(NSString*)openID
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| sdkAppId    	|NSString  |来自腾讯云控制台的 SdkAppId 号码				|
| openID    		|NSString  |唯一标识一个用户，规则由 App 开发者自行制定，目前只支持大于 10000 的数字类型|
> 示例代码  

```
[[ITMGContext GetInstance] SetAppInfo:SDKAPPID3RD openID:_openId];
```

### 设置版本信息
设置版本信息，用于查 Log 信息及 Bug 时使用。方便后台统计, 策略调整等（不设置不影响功能）。
> 函数原型

```
ITMGContext  -(void)SetAppVersion:(NSString*)appVersion
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| sAppVersion    |NSString  |版本号|
> 示例代码

```
[[ITMGContext GetInstance]SetAppVersion:appversion];
```

### 获取版本号
获取 SDK 版本号，用于分析。
> 函数原型

```
ITMGContext  -(NSString*)GetSDKVersion
```
> 示例代码  

```
[[ITMGContext GetInstance] GetSDKVersion];
```

### 实时语音鉴权信息
接下来是生成 AuthBuffer，用于相关功能的加密和鉴权，相关参数获取及详情见[游戏多媒体引擎接入指引](https://github.com/TencentMediaLab/GME/blob/master/GME%20Introduction.md)。  
>注意：在加入房间之前需要 AuthBuffer 作为参数。

该接口返回值为 NSData 类型。
> 函数原型

```
@interface QAVAuthBuffer : NSObject
+ (NSData*) GenAuthBuffer:(unsigned int)appId roomId:(unsigned int)roomId identifier:(NSString*)identifier  key:(NSString*)key expTime:(unsigned int)expTime authBits:(unsigned int) authBits;
@end
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| appId    		|int   		|来自腾讯云控制台的 SdkAppId 号码		|
| roomId    		|int  		|要加入的房间名							|
| identifier  		|NSString    	|用户标识								|
| key    			|NSString    	|来自腾讯云控制台的密钥					|
| expTime    		|int   		|authBuffer 超时时间						|
| authBits   	 	|uint64    	|权限									|

>关于权限  
>ITMG_AUTH_BITS_ALL 代表拥有全部权限，建议实时用户、主播使用，ITMG_AUTH_BITS_RECV 代表下行权限，建议纯听众、观众使用，不能使用startAccompany。

> 示例代码  

```
NSData* authBuffer =   [QAVAuthBuffer GenAuthBuffer:SDKAPPID3RD.intValue roomId:_roomId identifier:_openId key:AUTHKEY expTime:[[NSDate date] timeIntervalSince1970] + 3600 authBits:ITMG_AUTH_BITS_ALL];
```

### 设置最大混音路数
最后是设置最大混音路数（同时听到多少人讲话），在进房前调用，不调用默认为 6 路混音。
> 函数原型

```
ITMGContext -(void)SetRecvMixStreamCount:(int)count
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| nCount    |int   |混音路数，默认为 6|

> 示例代码  

```
[[ITMGContext GetInstance]SetRecvMixStreamCount:count];
```

### 设置后台播放声音
设置后台播放声音，在进房前调用，进房后且处于观众状态时可用，即角色 roleType="4" "6" 或者 "audience" "Raudience" 时。
同时，应用侧有如下两点需要注意：
1、退后台时没有暂停音频引擎的采集和播放（即 PauseAudio），
2、App 的 Info.plist 中，需要至少增加 key:Required background modes，string:App plays audio or streams audio/video using AirPlay。

> 函数原型
```
ITMGContext -(QAVResult)SetDefaultAudienceAudioCategory:(ITMG_AUDIO_CATEGORY)audioCategory
```

|类型     | 参数代表         |意义|
| ------------- |:-------------:|-------------
| ITMG_CATEGORY_AMBIENT    	|0	|退后台没有声音（默认）|
| ITMG_CATEGORY_PLAYBACK    	|1   	|退后台有声音	|
>注意 ：
>具体实现为修改 kAudioSessionProperty_AudioCategory，相关资料可参照 Apple 官方文档。


> 示例代码  
```
[[ITMGContext GetInstance]SetDefaultAudienceAudioCategory:ITMG_CATEGORY_AMBIENT];
```

## 实时语音房间事件接口

### 加入房间
用生成的鉴权信息进房，会收到消息为 ITMG_MAIN_EVENT_TYPE_ENTER_ROOM 的回调。
>注意:
>1、加入房间默认不打开麦克风及扬声器。
>2、在 EnterRoom 函数调用之前要先调用 SetAppInfo 函数及 SetAppVersion 函数进行相关信息的设置。
>关于角色的设置，在[游戏多媒体引擎角色说明](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/GME%20Role%20Manual.md)中有介绍。

> 函数原型

```
ITMGContext   -(void)EnterRoom:(int) relationId controlRole:(NSString*)role authBuffer:(NSData*)authBuffer
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| roomID		|int    		|房间号 	|
| controlRole    	|NSString    	|角色名称，按照需求设置，也可以询问接入技术人员获取|
| authBuffer    	|NSData    	|鉴权码						|
> 示例代码  

```
[[ITMGContext GetInstance] EnterRoom:_roomId controlRole:@"user" authBuffer:authBuffer];
```

### 加入房间事件的回调
加入房间完成后会发送信息 ITMG_MAIN_EVENT_TYPE_ENTER_ROOM，在 OnEvent 函数中进行判断。
> 代码说明

```
-(void)OnEvent:(ITMG_MAIN_EVENT_TYPE)eventType data:(NSDictionary *)data{
    NSLog(@"OnEvent:%lu,data:%@",(unsigned long)eventType,data);
    switch (eventType) {
        case ITMG_MAIN_EVENT_TYPE_ENTER_ROOM:
        {
            int result = ((NSNumber*)[data objectForKey:@"result"]).intValue;
            NSString* error_info = [data objectForKey:@"error_info"];
            //进入房间
        }
            break;
     }
}
```

### 判断是否已经进入房间
通过调用此接口可以判断是否已经进入房间，返回值为 BOOL 类型。
> 函数原型  

```
ITMGContext -(BOOL)IsRoomEntered
```
> 示例代码  

```
[[ITMGContext GetInstance] IsRoomEntered];
```

### 退出房间
通过调用此接口可以退出所在房间。
> 函数原型  

```
ITMGContext -(void)ExitRoom
```
> 示例代码

```
[[ITMGContext GetInstance] ExitRoom];
```

### 退出房间回调
退出房间完成回调，SDK 通过此回调通知 APP 成功退出了房间，事件为 ITMG_MAIN_EVENT_TYPE_EXIT_ROOM。

> 示例代码  

```
-(void)OnEvent:(ITMG_MAIN_EVENT_TYPE)eventType data:(NSDictionary *)data{
    NSLog(@"OnEvent:%lu,data:%@",(unsigned long)eventType,data);
    switch (eventType) {
        case ITMG_MAIN_EVENT_TYPE_EXIT_ROOM：
        {
	    //退出房间
        }
            break;
    }
}
```



### 角色设置
改变流控角色。该方法用于加入频道前设置用户角色，同时允许用户在加入频道后切换角色。
默认自动建6个角色，分别为：”esports””Rhost””Raudience””Werewolf””host””audience”。详细的角色说明请见[游戏多媒体引擎角色说明](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/GME%20Role%20Manual.md)。
> 函数原型  

```
ITMGContext GetRoom -(void)ChangeRole:(NSString*)role authBuffer:(NSData*)authBuffer
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| role    			|NSString     	|设置角色			|
| authBuffer    	|NSData    	|鉴权需要重新设置	|

>注意
流控角色意味着音视频编码参数的调整，所以需要再次调用音视频编码 API 重新设置鉴权（参考生成 AuthBuffer ）。
ChangeRole 只会改变自己的角色,  不可以改变其他人角色，主要会影响其他人听到自己的音质,   不会改变其他人上行的音质。
与房间无关,   房间没有角色概念,  房间可以理解为容器概念,  里面可以有各种音质的成员。

角色分别代表的通话质量：

|角色名称     | 适用场景         |关键特性|
| ------------- |:-------------:|-------------
| esports    	|适用于MOBA、竞技、射击类游戏     								|普通音质、极低延时	|
| Rhost    	|适用于 MMORPG 类游戏的指挥模式，只有指挥主播可上麦     		|高流畅、低延时		|
| Raudience	|适用于 MMORPG 类游戏的指挥模式，只有指挥主播可上麦     		|高流畅、低延时		|
| Werewolf 	|适用于狼人杀、休闲游戏等										|高音质、网络抗性强	|
| host  		|适用于 MMORPG 类游戏的主播模式，主播可与玩家进行语音视频互动	|高音质、网络抗性强	|
| audience  	|适用于 MMORPG 类游戏的主播模式，主播可与玩家进行语音视频互动	|高音质、网络抗性强	|

> 示例代码

```
[[[ITMGContext GetInstance]GetRoom ]ChangeRole:@"Playre"authBuffer:authBuffer];
```

### 角色设置完成回调
角色设置完成后，回调的事件消息为 ITMG_MAIN_EVENT_TYPE_CHANGE_ROLE，在 OnEvent 函数中对事件消息进行判断。
> 示例代码  

```
-(void)OnEvent:(ITMG_MAIN_EVENT_TYPE)eventType data:(NSDictionary *)data{
    NSLog(@"OnEvent:%lu,data:%@",(unsigned long)eventType,data);
    switch (eventType) {
        case ITMG_MAIN_EVENT_TYPE_CHANGE_ROLE：
        {
	    //角色设置完成
        }
            break;
    }
}
```

### 成员状态变化
该事件在状态变化才通知，状态不变化的情况下不通知。如需实时获取成员状态，请在上层收到通知时缓存，事件消息为 ITMG_MAIN_EVNET_TYPE_USER_UPDATE，包含两个信息，event_id 及 endpoints，在 OnEvent 函数中对事件消息进行判断。

|event_id     | 含义         |应用侧维护内容|
| ------------- |:-------------:|-------------|
|ITMG_EVENT_ID_USER_ENTER    				|有成员进入房间			|应用侧维护成员列表		|
|ITMG_EVENT_ID_USER_EXIT    				|有成员退出房间			|应用侧维护成员列表		|
|ITMG_EVENT_ID_USER_HAS_AUDIO    		|有成员发送音频包		|应用侧维护通话成员列表	|
|ITMG_EVENT_ID_USER_NO_AUDIO    			|有成员停止发送音频包	|应用侧维护通话成员列表	|
|ITMG_EVENT_ID_USER_HAS_CAMERA_VIDEO	|有成员开启摄像头		|应用侧维护通话成员列表	|
|ITMG_EVENT_ID_USER_NO_CAMERA_VIDEO	|有成员关闭摄像头		|应用侧维护通话成员列表	|

> 示例代码  

```
-(void)OnEvent:(ITMG_MAIN_EVENT_TYPE)eventType data:(NSDictionary *)data{
    NSLog(@"OnEvent:%lu,data:%@",(unsigned long)eventType,data);
    switch (eventType) {
        case ITMG_MAIN_EVNET_TYPE_USER_UPDATE:
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
		    case ITMG_EVENT_ID_USER_HAS_CAMERA_VIDEO:
			    //有成员开启摄像头
			    break;
		    case ITMG_EVENT_ID_USER_NO_CAMERA_VIDEO:
			    //有成员关闭摄像头
			    break;
		    default:
			    break;
 		    }
		break;
		}
    }
}
```

### 获取诊断信息
获取音视频通话的实时通话质量的相关信息。该接口主要用来查看实时通话质量、排查问题等，业务侧可以忽略。
> 函数原型  

```
GetAudioEffectCtrl GetRoom -(NSString*)GetQualityTips
```
> 示例代码  

```
[[[ITMGContext GetInstance]GetRoom ] GetQualityTips];
```

## 实时语音音频接口

### 暂停音频引擎的采集和播放
调用此接口暂停音频引擎的采集和播放，只在进房后有效。
在 EnterRoom 接口调用成功之后之后就会占用麦克风权限，期间其他程序无法进行麦克风采集。
注意：调用 EnableMic(false) 无法释放麦克风占用。
如果确实需要释放麦克风，请调用 PauseAudio 接口。调用 PauseAudio 接口后会整个暂停引擎，调用 ResumeAudio 函接口可恢复音频采集。
> 函数原型  

```
ITMGContext GetAudioCtrl -(QAVResult)PauseAudio
```
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioCtrl] PauseAudio];
```

### 恢复音频引擎的采集和播放
调用此接口恢复音频引擎的采集和播放，只在进房后有效。
> 函数原型  

```
ITMGContext GetAudioCtrl -(QAVResult)ResumeAudio
```
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioCtrl] ResumeAudio];
```

### 开启、关闭音频数据黑名单逻辑
开启黑名单时每次调用黑名单接口，黑名单将被重置为新的成员列表，而不是累加。需要定制所需接收的音频数据才调用，不调用则默认接收房间内所有音频数据。返回值为 0 表示调用失败。
> 函数原型  

```
ITMGContext GetRoom -(QAVResult)SetAudioBlackList:(NSArray*)identifierList
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| identifierList    |NSArray      |黑名单列表|
> 示例代码  

```
[[[ITMGContext GetInstance]GetRoom ] GetQualityTips];
```

### 麦克风开启关闭事件
此接口用来开启及关闭麦克风。
>注意:加入房间默认不打开麦克风及扬声器。

> 函数原型  

```
ITMGContext GetAudioCtrl -(void)EnableMic:(BOOL)enable
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| isEnabled    |boolean     |如果需要打开麦克风，则传入的参数为 YES，如果关闭麦克风，则参数为 NO|
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioCtrl] EnableMic:YES];
```

### 麦克风事件的回调
麦克风事件的回调调用函数 OnEvent，SDK 通过此回调通知 APP 成功调用了麦克风，事件消息为 ITMG_MAIN_EVENT_TYPE_ENABLE_MIC， ITMG_MAIN_EVENT_TYPE_DISABLE_MIC，在 OnEvent 函数中对事件消息进行判断。

> 示例代码  

```
-(void)OnEvent:(ITMG_MAIN_EVENT_TYPE)eventType data:(NSDictionary *)data{
    NSLog(@"OnEvent:%lu,data:%@",(unsigned long)eventType,data);
    switch (eventType) {
        case ITMG_MAIN_EVENT_TYPE_ENABLE_MIC：
        {
	    //打开麦克风
        }
            break;
	case ITMG_MAIN_EVENT_TYPE_DISABLE_MIC：
        {
	    //关闭麦克风
        }
            break;
    }
}
```

### 麦克风状态获取
此接口用于获取麦克风状态，返回值 0 为关闭麦克风状态，返回值 1 为打开麦克风状态，返回值 2 为麦克风设备正在操作中，返回值 4 为设备没初始化好。
> 函数原型  

```
ITMGContext GetAudioCtrl -(int)GetMicState
```
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioCtrl] GetMicState];
```

### 获取麦克风实时音量
此接口用于获取麦克风实时音量，返回值为 int 类型。
> 函数原型  

```
ITMGContext GetAudioCtrl -(int)GetMicLevel
```
> 示例代码  
```
[[[ITMGContext GetInstance] GetAudioCtrl] GetMicLevel];
```

### 设置麦克风的软件音量
此接口用于设置麦克风的软件音量。参数 volume 用于设置麦克风的软件音量，当数值为 0 的时候表示静音，当数值为 100 的时候表示音量不增不减，默认数值为 100。
> 函数原型 
 
```
ITMGContext GetAudioCtrl -(void)SetMicVolume:(int) volume
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| volume    |int      |设置音量，范围 0 到 150|
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioCtrl] SetMicVolume:100];
```

###  获取麦克风的软件音量
此接口用于获取麦克风的软件音量。返回值为一个int类型数值。
> 函数原型  

```
ITMGContext GetAudioCtrl -(int) GetMicVolume
```
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioCtrl] GetMicVolume];
```

### 扬声器开启关闭事件
此接口用于设置扬声器开启关闭。
> 函数原型  

```
ITMGContext GetAudioCtrl -(void)EnableSpeaker:(BOOL)enable
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| isEnabled    |boolean       |如果需要关闭扬声器，则传入的参数为 NO，如果打开扬声器，则参数为 YES|
> 示例代码  

```
打开扬声器
[[[ITMGContext GetInstance] GetAudioCtrl] EnableSpeaker:YES];
关闭扬声器
[[[ITMGContext GetInstance] GetAudioCtrl] EnableSpeaker:NO];
```

### 扬声器事件的回调
扬声器事件回调，SDK 通过此回调通知 APP 成功调用了扬声器，事件消息为 ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER， ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER。
> 示例代码  

```
-(void)OnEvent:(ITMG_MAIN_EVENT_TYPE)eventType data:(NSDictionary *)data{
    NSLog(@"OnEvent:%lu,data:%@",(unsigned long)eventType,data);
    switch (eventType) {
        case ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER：
        {
	    //打开扬声器
        }
            break;
	case ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER：
        {
	    //关闭扬声器
        }
            break;
    }
}
```

### 扬声器状态获取
此接口用于扬声器状态获取。返回值为 int 类型数值。返回值 0 为关闭扬声器状态，返回值 1 为打开扬声器状态，返回值 2 为扬声器设备正在操作中，返回值 4 为设备没初始化好。
> 函数原型  

```
ITMGContext GetAudioCtrl -(int)GetSpeakerState
```

> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioCtrl] GetSpeakerState];
```

### 获取扬声器实时音量
此接口用于获取扬声器实时音量。返回值为 int 类型数值，表示扬声器实时音量。
> 函数原型  

```
ITMGContext GetAudioCtrl -(int)GetSpeakerLevel
```

> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioCtrl] GetSpeakerLevel];
```

### 设置扬声器的软件音量
此接口用于设置扬声器的软件音量。
>注意：参数 volume 用于设置扬声器的软件音量，当数值为 0 的时候表示静音，当数值为 100 的时候表示音量不增不减，默认数值为 100。

> 函数原型  

```
ITMGContext GetAudioCtrl -(void)SetSpeakerVolume:(int)vol
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| vol    |int        |设置音量，范围 0 到 150|
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioCtrl] SetSpeakerVolume:100];
```

### 获取扬声器的软件音量
此接口用于获取扬声器的软件音量。返回值为 int 类型数值，代表扬声器的软件音量。
>注意：Level 是实时音量，Volume 是扬声器的软件音量，最终声音音量相当于 Level*Volume%。举个例子：实时音量是数值是 100 的话，此时Volume的数值是 60，那么最终发出来的声音数值也是 60。

> 函数原型  

```
ITMGContext GetAudioCtrl -(int)GetSpeakerVolume
```
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioCtrl] GetSpeakerVolume];
```


### 启动耳返
此接口用于启动耳返。
> 函数原型  

```
ITMGContext GetAudioCtrl -(QAVResult)EnableLoopBack:(BOOL)enable
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| enable    |boolean         |设置是否启动|
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioCtrl] EnableLoopBack:YES];
```


## 实时语音伴奏相关接口

### 开始播放伴奏
调用此接口开始播放伴奏。支持 m4a、AAC、wav、mp3 一共四种格式。
注意：1、调用此 API，音量会重置。
2、下行权限不能启用此 API。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVAccResult)StartAccompany:(NSString*)filePath loopBack:(BOOL)loopBack loopCount:(int)loopCount
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    	|NSString    		|播放伴奏的路径											|
| loopBack  	|boolean         	|是否混音发送，一般都设置为 YES，即其他人也能听到伴奏	|
| loopCount	|int          		|循环次数，数值为 -1 表示无限循环							|
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] StartAccompany:path loopBack:isLoopBack loopCount:loopCount];
```

### 播放伴奏的回调
开始播放伴奏完成后，回调函数调用 OnEvent，事件消息为 ITMG_MAIN_EVENT_TYPE_ACCOMPANY_FINISH，在 OnEvent 函数中对事件消息进行判断。
> 示例代码  

```
-(void)OnEvent:(ITMG_MAIN_EVENT_TYPE)eventType data:(NSDictionary *)data{
    NSLog(@"OnEvent:%lu,data:%@",(unsigned long)eventType,data);
    switch (eventType) {
        case ITMG_MAIN_EVENT_TYPE_ACCOMPANY_FINISH：
        {
	    //完成播放伴奏
        }
            break;
    }
}
```

### 停止播放伴奏
调用此接口停止播放伴奏。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVAccResult)StopAccompany:(int)duckerTime
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| duckerTimeMs    |int             |淡出时间|

> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] StopAccompany:duckerTime];
```

### 伴奏是否播放完毕
如果播放完毕，返回值为 YES，如果没播放完，返回值为 NO。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(bool)IsAccompanyPlayEnd
```
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] IsAccompanyPlayEnd];
```

### 暂停播放伴奏
调用此接口暂停播放伴奏。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVAccResult)PauseAccompany
```
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] PauseAccompany];
```

### 重新播放伴奏
此接口用于重新播放伴奏。
> 函数原型  

```
GetAudioEffectCtrl -(QAVAccResult)ResumeAccompany
```
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] ResumeAccompany];
```

### 设置自己是否可以听到伴奏
此接口用于设置自己是否可以听到伴奏。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVAccResult)EnableAccompanyPlay:(BOOL)enable
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| enable    |BOOL             |是否能听到|
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] EnableAccompanyPlay :YES];
```

### 设置他人是否也可以听到伴奏
设置他人是否也可以听到伴奏。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVAccResult)EnableAccompanyLoopBack:(BOOL)enable
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| enable    |BOOL             |是否能听到|

> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] EnableAccompanyLoopBack:YES];
```

### 设置伴奏音量
设置 DB 音量，默认值为 100，数值大于 100 音量增益，数值小于 100 音量减益，值域为 0-200。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVAccResult)SetAccompanyVolume:(int)vol
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| vol    |int             |音量数值|

> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] SetAccompanyVolume:volume];
```

### 获取播放伴奏的音量
此接口用于获取 DB 音量。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(int)GetAccompanyVolume
```
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] GetAccompanyVolume];
```

### 获得伴奏播放进度
以下两个接口用于获得伴奏播放进度。需要注意：Current / Total = 当前循环次数，Current % Total = 当前循环播放位置。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(int)GetAccompanyFileTotalTimeByMs
ITMGContext GetAudioEffectCtrl -(int)GetAccompanyFileCurrentPlayedTimeByMs
```
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] GetAccompanyFileTotalTimeByMs];
[[[ITMGContext GetInstance] GetAudioEffectCtrl] GetAccompanyFileCurrentPlayedTimeByMs];
```

### 设置播放进度
此接口用于设置播放进度。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVAccResult)SetAccompanyFileCurrentPlayedTimeByMs:(uint) time
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| time    |uint                |播放进度，以毫秒为单位|

> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] SetAccompanyFileCurrentPlayedTimeByMs:time];
```

## 实时语音音效相关接口


### 播放音效
此接口用于播放音效。参数中音效 id 需要 App 侧进行管理，唯一标识一个独立文件。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVResult)PlayEffect:(int)soundId filePath:(NSString*)filePath loop:(BOOL)loop
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId  	|int           	|音效 id			|
| filePath    	|NSString    	|音效路径		|
| loop    		|boolean  	|是否重复播放	|
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] PlayEffect:soundId filePath:path loop:isLoop];
```

### 暂停播放音效
此接口用于暂停播放音效。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVResult)PauseEffect:(int)soundId
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId    |int                    |音效 id|

> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] PauseEffect:soundId];
```

### 暂停所有音效
调用此接口暂停所有音效。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVResult)PauseAllEffects
```
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] PauseAllEffects];
```

### 重新播放音效
此接口用于重新播放音效。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVResult)ResumeEffect:(int)soundId
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId    |int                    |音效 id|
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] ResumeEffect:soundId];
```

### 重新播放所有音效
调用此接口重新播放所有音效。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVResult)ResumeAllEffects
```
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] ResumeAllEffects];
```

### 停止播放音效
此接口用于停止播放音效。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVResult)StopEffect:(int)soundId
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId    |int                    |音效 id|
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] StopEffect:soundId];
```

### 停止播放所有音效
调用此接口停止播放所有音效。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVResult)StopAllEffects
```
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] StopAllEffects];
```

### 变声特效
调用此接口设置变声特效。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVResult)SetVoiceType:(ITMG_VOICE_TYPE) type
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
[[[ITMGContext GetInstance] GetAudioEffectCtrl] SetVoiceType:0];
```

### 获取播放音效的音量
获取播放音效的音量，为线性音量，默认值为 100，数值大于 100 为增益效果，数值小于 100 为减益效果。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(int)GetEffectsVolume
```
> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] GetEffectsVolume];
```

### 设置播放音效的音量
调用此接口设置播放音效的音量。
> 函数原型  

```
ITMGContext GetAudioEffectCtrl -(QAVResult)SetEffectsVolume:(int)volume
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| volume    |int                    |音量数值|

> 示例代码  

```
[[[ITMGContext GetInstance] GetAudioEffectCtrl] SetEffectsVolume:(int)Volume];
```

## 离线语音
### 离线语音技术接入初始化
初始化需要传入鉴权 access token 给 TLS 相关函数。鉴权的获取详细流程见[游戏多媒体引擎接入指引](https://github.com/TencentMediaLab/GME/blob/master/GME%20Introduction.md)
。
Error 参数用于传递错误信息，比如参数填错了：appid 填 0、key 为空、identifier 为空之类的情况都会返回错误。
> 函数原型  

```
+(NSString*)genSig:(NSString*)appId identifier:(NSString*)identifier privateKey:(NSString*)privateKey error:(NSError**)error
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| sdkAppId  	|int   		|来自腾讯云控制台的 SdkAppId 号码													|
| openID    	|NSString	|唯一标识一个用户，规则由 App 开发者自行制定											|
| key    		|NSString 	|来自腾讯云控制台的鉴权																|
| error    	|Error   		|指定一个传错误值的对象的地址。生成 sig 出错了，返回值为空，error 里面会带上错误信息。	|
```
-(QAVResult)ApplyAccessToken:(NSString*)accessToken
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| accessToken    |NSString                       |getTLSSig 函数返回的 accessToken|
> 示例代码  

```
NSString* privateKey = @"自己在官网的 key";
NSString* accessToken = [QAVSDKSigManager genSig:SDKAPPID3RD identifier:_openId privateKey:privateKey error:&error];
[[[ITMGContext GetInstance]GetPTT]ApplyAccessToken:accessToken];
```

### 限制最大语音信息时长
限制最大语音消息的长度，最大支持 60 秒。

> 函数原型  

```
ITMGContext GetPTT -(void)SetMaxMessageLength:(int)msTime
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| msTime    |int                    |语音时长|
> 示例代码  

```
[[[ITMGContext GetInstance]GetPTT]SetMaxMessageLength:(int)msTime];
```

### 启动录音
此接口用于启动录音。
> 函数原型  

```
ITMGContext GetPTT -(void)StartRecording:(NSString*)fileDir
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| fileDir    |NSString                     |存放的语音路径|
> 示例代码  

```
[[[ITMGContext GetInstance]GetPTT]StartRecording:path];
```

### 启动录音的回调
启动录音完成后的回调调用函数 OnEvent，事件消息为 ITMG_MAIN_EVNET_TYPE_PTT_RECORD_COMPLETE， 在 OnEvent 函数中对事件消息进行判断。

> 示例代码  

```
-(void)OnEvent:(ITMG_MAIN_EVENT_TYPE)eventType data:(NSDictionary *)data{
    NSLog(@"OnEvent:%lu,data:%@",(unsigned long)eventType,data);
    switch (eventType) {
        case ITMG_MAIN_EVNET_TYPE_PTT_RECORD_COMPLETE：
        {
	    //录音完成
        }
            break;
    }
}
```

### 停止录音
此接口用于停止录音。
> 函数原型  

```
ITMGContext GetPTT -(QAVResult)StopRecording
```
> 示例代码  

```
[[[ITMGContext GetInstance]GetPTT]StopRecording];
```

### 取消录音
调用此接口取消录音。
> 函数原型  

```
ITMGContext GetPTT -(QAVResult)CancelRecording
```
> 示例代码  

```
[[[ITMGContext GetInstance]GetPTT]CancelRecording];
```

### 上传语音文件
此接口用于上传语音文件。
> 函数原型  

```
ITMGContext GetPTT -(void)UploadRecordedFile:(NSString*)filePath
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    |NSString                      |上传的语音路径|
> 示例代码  

```
[[[ITMGContext GetInstance]GetPTT]UploadRecordedFile:path];
```

### 上传语音完成的回调
上传语音完成后，事件消息为 ITMG_MAIN_EVNET_TYPE_PTT_UPLOAD_COMPLETE， 在 OnEvent 函数中对事件消息进行判断。
```
-(void)OnEvent:(ITMG_MAIN_EVENT_TYPE)eventType data:(NSDictionary *)data{
    NSLog(@"OnEvent:%lu,data:%@",(unsigned long)eventType,data);
    switch (eventType) {
        case ITMG_MAIN_EVNET_TYPE_PTT_UPLOAD_COMPLETE：
        {
	    //上传语音完成
        }
            break;
    }
}
```

### 下载语音文件
此接口用于下载语音文件。
> 函数原型  

```
ITMGContext GetPTT -(void)DownloadRecordedFile:(NSString*)fileId downloadFilePath:(NSString*)downloadFilePath
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| fileID    			|NSString                      |文件的url路径		|
| downloadFilePath 	|NSString                      |文件的下载路径	|
> 示例代码  

```
[[[ITMGContext GetInstance]GetPTT]DownloadRecordedFile:fileIdpath downloadFilePath:path];
```

### 下载语音文件完成回调
下载语音完成后，事件消息为 ITMG_MAIN_EVNET_TYPE_PTT_DOWNLOAD_COMPLETE， 在 OnEvent 函数中对事件消息进行判断。
```
-(void)OnEvent:(ITMG_MAIN_EVENT_TYPE)eventType data:(NSDictionary *)data{
    NSLog(@"OnEvent:%lu,data:%@",(unsigned long)eventType,data);
    switch (eventType) {
        case ITMG_MAIN_EVNET_TYPE_PTT_DOWNLOAD_COMPLETE：
        {
	    //完成下载语音文件
        }
            break;
    }
}
```

### 播放语音
此接口用于播放语音。
> 函数原型  

```
ITMGContext GetPTT -(void)PlayRecordedFile:(NSString*)downloadFilePath
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| downloadFilePath    |NSString                      |文件的路径|
> 示例代码  

```
[[[ITMGContext GetInstance]GetPTT]PlayRecordedFile:path];
```

### 播放语音的回调
播放语音的回调，事件消息为 ITMG_MAIN_EVNET_TYPE_PTT_PLAY_COMPLETE， 在 OnEvent 函数中对事件消息进行判断。
```
-(void)OnEvent:(ITMG_MAIN_EVENT_TYPE)eventType data:(NSDictionary *)data{
    NSLog(@"OnEvent:%lu,data:%@",(unsigned long)eventType,data);
    switch (eventType) {
        case ITMG_MAIN_EVNET_TYPE_PTT_PLAY_COMPLETE：
        {
	    //播放完成
        }
            break;
    }
}
```

### 停止播放语音
此接口用于停止播放语音。
> 函数原型  

```
ITMGContext GetPTT -(int)StopPlayFile
```
> 示例代码  

```
[[[ITMGContext GetInstance]GetPTT]StopPlayFile];
```

### 获取语音文件的大小
通过此接口，获取语音文件的大小。
> 函数原型  

```
ITMGContext GetPTT -(int)GetFileSize:(NSString*)filePath
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    |NSString                     |语音文件的路径|
> 示例代码  

```
[[[ITMGContext GetInstance]GetPTT]GetFileSize:path];
```

### 获取语音文件的时长
此接口用于获取语音文件的时长。
> 函数原型  

```
ITMGContext GetPTT -(int)GetVoiceFileDuration:(NSString*)filePath
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    |NSString                     |语音文件的路径|
> 示例代码  

```
[[[ITMGContext GetInstance]GetPTT]GetVoiceFileDuration:path];
```

### 将指定的语音文件翻译成文字
此接口用于将指定的语音文件翻译成文字。
> 函数原型  

```
ITMGContext GetPTT -(int)SpeechToText:(NSString*)fileID
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| fileID    |NSString                     |语音文件 url|
> 示例代码  

```
[[[ITMGContext GetInstance]GetPTT]SpeechToText:fileID];
```

### 翻译回调
将指定的语音文件翻译成文字的回调，事件消息为 ITMG_MAIN_EVNET_TYPE_PTT_SPEECH2TEXT_COMPLETE， 在 OnEvent 函数中对事件消息进行判断。
```
-(void)OnEvent:(ITMG_MAIN_EVENT_TYPE)eventType data:(NSDictionary *)data{
    NSLog(@"OnEvent:%lu,data:%@",(unsigned long)eventType,data);
    switch (eventType) {
        case ITMG_MAIN_EVNET_TYPE_PTT_SPEECH2TEXT_COMPLETE：
        {
	    //翻译完成
        }
            break;   
    }
}
```
