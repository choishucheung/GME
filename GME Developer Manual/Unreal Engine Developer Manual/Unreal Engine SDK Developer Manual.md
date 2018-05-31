## 简介
欢迎使用腾讯云游戏多媒体引擎 SDK 。为方便 Unreal Engine 开发者调试和接入腾讯云游戏多媒体引擎产品 API，这里向您介绍适用于 Unreal Engine 开发的接入技术文档。

## SDK初始化  

### 准备工作
接入 GME 首先需要引入头文件 tmg_sdk.h，头文件类继承 ITMGDelegate 以进行消息的传递及回调。
> 示例代码  
```
#include "tmg_sdk.h"

class UEDEMO1_API AUEDemoLevelScriptActor : public ALevelScriptActor, public ITMGDelegate
{
public:
...
private:
...
｝
```

### 消息传递
GME 的消息通过 ITMGDelegate 传给应用，消息类型参考 ITMG_MAIN_EVENT_TYPE，data 在 Windows 平台下是 json 字符串格式， 具体 key-value 参见说明文档。

> 示例代码 
```
//函数实现：
//UEDemoLevelScriptActor.h:
class UEDEMO1_API AUEDemoLevelScriptActor : public ALevelScriptActor, public ITMGDelegate
{
public:
	void OnEvent(ITMG_MAIN_EVENT_TYPE eventType, const char* data);
...
｝

//UEDemoLevelScriptActor.cpp:
void AUEDemoLevelScriptActor::OnEvent(ITMG_MAIN_EVENT_TYPE eventType, const char* data){
	//在此对eventType进行判断及操作
}
```
>消息列表：

|消息     | 消息代表的意义   
| ------------- |:-------------:|
|ITMG_MAIN_EVENT_TYPE_ENTER_ROOM    				|进入音频房间消息
|ITMG_MAIN_EVENT_TYPE_EXIT_ROOM    				|退出音频房间消息
|ITMG_MAIN_EVENT_TYPE_ROOM_DISCONNECT			|房间因为网络等原因断开消息
|ITMG_MAIN_EVENT_TYPE_ENABLE_MIC    				|打开麦克风消息
|ITMG_MAIN_EVENT_TYPE_DISABLE_MIC    				|关闭麦克风消息
|ITMG_MAIN_EVENT_TYPE_MIC_NEW_DEVICE    			|新增麦克风设备消息
|ITMG_MAIN_EVENT_TYPE_MIC_LOST_DEVICE    			|丢失麦克风设备消息
|ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER				|打开扬声器消息
|ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER				|关闭扬声器消息
|ITMG_MAIN_EVENT_TYPE_SPEAKER_NEW_DEVICE			|新增扬声器设备消息
|ITMG_MAIN_EVENT_TYPE_SPEAKER_LOST_DEVICE		|丢失扬声器设备消息
|ITMG_MAIN_EVENT_TYPE_CHANGE_ROLE				|切换角色消息
|ITMG_MAIN_EVNET_TYPE_USER_UPDATE					|房间成员更新消息
|ITMG_MAIN_EVENT_TYPE_ACCOMPANY_FINISH			|伴奏结束消息
|ITMG_MAIN_EVNET_TYPE_PTT_RECORD_COMPLETE		|PTT 录音完成
|ITMG_MAIN_EVNET_TYPE_PTT_UPLOAD_COMPLETE		|上传 PTT 完成
|ITMG_MAIN_EVNET_TYPE_PTT_DOWNLOAD_COMPLETE	|下载 PTT 完成
|ITMG_MAIN_EVNET_TYPE_PTT_PLAY_COMPLETE			|播放 PTT 完成
|ITMG_MAIN_EVNET_TYPE_PTT_SPEECH2TEXT_COMPLETE	|语音转文字完成

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

### 设置单例
在 EnterRoom 函数调用之前要先获取 ITMGContext ，ITMGContext 以单例的形式提供，所有调用都从 ITMGContext 开始，由ITMGDelegate 回调回传给应用，必须首先设置。
>示例代码
```
 ITMGContextGetInstance()->TMGDelegate(this);
```

### 设置 App 信息
获取相关信息，由腾讯云控制台申请，详情见[游戏多媒体引擎接入指引](https://github.com/TencentMediaLab/GME/blob/master/GME%20Introduction.md)。

此接口需要来自腾讯云控制台的 SdkAppId 号码作为参数，再加上 openId，这个 openId 是唯一标识一个用户，规则由 App 开发者自行制定，App 内不重复即可（目前只支持 INT32）。
> 函数原型 
```
ITMGContext virtual void SetAppInfo(const char* sdkAppId, const char* openId)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| sdkAppId    	|char  	|来自腾讯云控制台的 SdkAppId 号码					|
| openID    		|char  	|OpenID 为 Int32 类型，必须大于 10000，用于标识用户 	|
> 示例代码  
```
std::string appid = TCHAR_TO_UTF8(CurrentWidget->editAppID->GetText().ToString().operator*());
std::string userId = TCHAR_TO_UTF8(CurrentWidget->editUserID->GetText().ToString().operator*());
ITMGContextGetInstance()->SetAppInfo(appid.c_str(), userId.c_str());
```

### 设置更新函数
Unreal Engine 的更新函数 Tick 需要配置。
>Poll 为回调触发函数，触发SDK回调，ITMGDelegate::OnEvent 事件(回调线程为Poll的调用线程)。

>函数原型
```
class ITMGContext {
protected:
    virtual ~ITMGContext() {}
    
public:
	//Destroy TMGSDK, make sure to release SDK after use.
    	virtual void Destroy() = 0;
	//Poll
    	virtual void Poll()= 0;
	//Pause
	virtual int Pause() = 0;
	//Resume
    	virtual int Resume() = 0;
}
```
>示例代码
```
//头文件中的声明
virtual void Tick(float DeltaSeconds);

//代码实现
void AUEDemoLevelScriptActor::Tick(float DeltaSeconds) 
{   
ITMGContextGetInstance()->Poll();
}
```


### 设置版本信息
设置版本信息，用于查 Log 信息及 Bug 时使用，方便后台统计, 策略调整等（不设置不影响功能）。
> 函数原型
```
ITMGContext virtual void SetAppVersion(const char* appVersion)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| appVersion    |char  |版本号|
> 示例代码  
```
ITMGContextGetInstance()->SetAppVersion("1.3");
```

### 获取版本号
获取 SDK 版本号，用于分析。
> 函数原型
```
ITMGContext virtual const char* GetSDKVersion()
```
> 示例代码  
```
ITMGContextGetInstance()->GetSDKVersion;
```

### 设置打印日志等级
用于设置打印日志等级。
> 函数原型
```
ITMGContext virtual void SetLogLevel(ITMG_LOG_LEVEL logLevel)
```
>ITMG_LOG_LEVEL 对照表
>
|ITMG_LOG_LEVEL|意义|
| -------------------------------	|----------------------	|
|TMG_LOG_LEVEL_NONE		|不打印日志			|
|TMG_LOG_LEVEL_ERROR		|打印错误日志		|
|TMG_LOG_LEVEL_INFO		|打印提示日志		|
|TMG_LOG_LEVEL_DEBUG	|打印开发调试日志	|
|TMG_LOG_LEVEL_VERBOSE	|打印高频日志		|
> 示例代码  
```
ITMGContextGetInstance()->SetLogLevel(TMG_LOG_LEVEL_NONE);
```

### 设置打印日志路径
用于设置打印日志路径。
> 函数原型
```
ITMGContext virtual void SetLogPath(const char* logDir) 
```

> 示例代码  
```
cosnt char* logDir = ""//自行设置路径
ITMGContext* context = ITMGContextGetInstance();
context->SetLogPath(logDir);
```

### 实时语音鉴权信息
生成 AuthBuffer，用于相关功能的加密和鉴权，相关参数获取及详情见[游戏多媒体引擎密钥文档](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/GME%20Key%20Manual.md)。  
>注意：在加入房间之前需要 AuthBuffer 作为参数。

> 函数原型
```
QAVSDK_API int QAVSDK_CALL QAVSDK_AuthBuffer_GenAuthBuffer(unsigned int appId, unsigned int authId, const char* account, const char* key, unsigned int expTime, unsigned int privilegeMap, unsigned char* retAuthBuff, unsigned int* buffLenght);
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| appId    		|int   		|来自腾讯云控制台的 SdkAppId 号码		|
| authId    		|int  		|要加入的房间名							|
| account  		|char    		|用户标识								|
| key    			|char	    	|来自腾讯云控制台的密钥					|
| expTime    		|int   		|authBuffer 超时时间						|
| privilegeMap   	|int    		|权限									|
| retAuthBuff   	|char    		|返回的 authbuff							|
| buffLenght   	|int    		|返回的authbuff的长度					|

>关于权限  

ITMG_AUTH_BITS_ALL 代表拥有全部权限，建议实时用户、主播使用，ITMG_AUTH_BITS_RECV 代表下行权限，建议纯听众、观众使用，不能使用startAccompany。

> 示例代码  
```
unsigned int bufferLen = 512;
unsigned char retAuthBuff[512] = {0};
unsigned int expTime =0;

QAVSDK_AuthBuffer_GenAuthBuffer(appId, roomId, "", account, expTime, ITMG_AUTH_BITS_DEFAULT, retAuthBuff, &bufferLen);
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
ITMGContext* context = ITMGContextGetInstance();
context->SetRecvMixStreamCount(nCount);
```


## 实时语音房间事件接口

### 加入房间
用生成的鉴权信息进房，会收到消息为 ITMG_MAIN_EVENT_TYPE_ENTER_ROOM 的回调。
>注意:
>1、加入房间默认不打开麦克风及扬声器。
>2、在 EnterRoom 函数调用之前要先调用 SetAppInfo 函数及 SetAppVersion 函数进行相关信息的设置。
>3、关于角色的设置，在[游戏多媒体引擎角色说明](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/GME%20Role%20Manual.md)中有介绍。
>4、如果普通语音进房，业务方面无涉及小队语音需求，则使用普通进房接口。详细信息请查阅[GME小队语音说明](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/GME%20TeamAudio%20Manual.md)。

> 函数原型

```
ITMGContext virtual void EnterRoom(int relationId, const char* role, const char* authBuff, int buffLen)//普通进房接口
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| relationId			|int   	|房间号 					|
| role    				|char    	|角色名称，按照需求设置，也可以询问接入技术人员获取	|
| authBuffer    		|char    	|鉴权码												|
| buffLen   			|int   	|鉴权码长度											|
> 示例代码  

```
ITMGContext* context = ITMGContextGetInstance();
context->EnterRoom(roomId, role, (char*)retAuthBuff,bufferLen);//普通语音进房示例代码
```


#### 小队语音房间
详细接入细节请查阅[小队语音接入文档](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/GME%20TeamAudio%20Manual.md)。
> 函数原型
```
ITMGContext virtual void EnterTeamRoom(int relationId, const char* role, const char* authBuff, int buffLen,int teamId, int audioMode)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| relationId			|int   	|房间号 					|
| role    				|char    	|角色名称，按照需求设置，也可以询问接入技术人员获取	|
| authBuffer    		|char    	|鉴权码												|
| buffLen   			|int   	|鉴权码长度											|
| teamId    		|int    	|加入的小队语音队伍标识码（不能为 0 ）	|
| audioMode    	|int    	|0 代表全局语音，1 代表小队语音			|

> 示例代码  
```
ITMGContext* context = ITMGContextGetInstance();
context->EnterRoom(roomId, role, (char*)retAuthBuff,bufferLen,1000,0);
```

### 加入房间事件的回调
加入房间完成后会发送信息 ITMG_MAIN_EVENT_TYPE_ENTER_ROOM，在 OnEvent 函数中进行判断。
> 代码说明
```
//在头文件中继承了 ITMGDelegate，并进行声明。
class TMGTestScene : public cocos2d::Scene,public ITMGDelegate
{
public:
    void OnEvent(ITMG_MAIN_EVENT_TYPE eventType,const char* data);
    ...	
}

//实现代码
void TMGTestScene::OnEvent(ITMG_MAIN_EVENT_TYPE eventType,const char* data){
	switch (eventType) {
            case ITMG_MAIN_EVENT_TYPE_ENTER_ROOM:
		{
		//进行处理
		break;
		}
	}
}
```

### 判断是否已经进入房间
通过调用此接口可以判断是否已经进入房间，返回值为 bool 类型。
> 函数原型  
```
ITMGContext virtual bool IsRoomEntered()
```
> 示例代码  
```
ITMGContext* context = ITMGContextGetInstance();
context->IsRoomEntered();
```

### 退出房间
通过调用此接口可以退出所在房间。
> 函数原型  

```
ITMGContext virtual void ExitRoom()
```
> 示例代码  

```
ITMGContext* context = ITMGContextGetInstance();
context->ExitRoom();
```

### 退出房间回调
退出房间完成回调，SDK 通过此回调通知 APP 成功退出了房间，事件为 ITMG_MAIN_EVENT_TYPE_EXIT_ROOM。
> 示例代码  

```
void TMGTestScene::OnEvent(ITMG_MAIN_EVENT_TYPE eventType,const char* data){
	switch (eventType) {
            case ITMG_MAIN_EVENT_TYPE_EXIT_ROOM:
		{
		//进行处理
		break;
		}
	}
}
```

### 角色设置
改变流控角色。该方法用于加入频道前设置用户角色，同时允许用户在加入频道后切换角色。
默认自动建6个角色，分别为：”esports””Rhost””Raudience””Werewolf””host””audience”。详细的角色说明请见[游戏多媒体引擎角色说明](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/GME%20Role%20Manual.md)。
> 函数原型  

```
ITMGRoom virtual void ChangeRole(const char* role, const unsigned char* authBuff, int authBuffLenght)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| role    				|char     	|设置角色			|
| authBuffer    		|char    	|鉴权需要重新设置	|
| authBuffLenght	|int    	|鉴权长度			|

>注意
流控角色意味着音视频编码参数的调整，所以需要再次调用音视频编码 API 重新设置鉴权（参考生成 AuthBuffer ）。
ChangeRole 只会改变自己的角色,  不可以改变其他人角色，主要会影响其他人听到自己的音质,   不会改变其他人上行的音质。
与房间无关,   房间没有角色概念,  房间可以理解为容器概念,  里面可以有各种音质的成员。

角色分别代表的通话质量：

|角色名称     | 适用场景         |关键特性|
| ------------- |:-------------:|-------------
| esports    		|适用于 MOBA、竞技、射击类游戏     								|普通音质、极低延时	|
| Rhost 			|适用于 MMORPG 类游戏的指挥模式，只有指挥主播可上麦  			|高流畅、低延时		|
| Raudience    	|适用于 MMORPG 类游戏的指挥模式，只有指挥主播可上麦   			|高流畅、低延时		|
| Werewolf    	|适用于狼人杀、休闲游戏等										|高音质、网络抗性强	|
| host    			|适用于 MMORPG 类游戏的主播模式，主播可与玩家进行语音视频互动	|高音质、网络抗性强	|
| audience    	|适用于 MMORPG 类游戏的主播模式，主播可与玩家进行语音视频互动	|高音质、网络抗性强	|

> 示例代码  
```
ITMGContextGetInstance()->GetRoom()->ChangeRole(role,authBuff,authBuffLenght);
```

### 角色设置完成回调
角色设置完成后，回调的事件消息为 ITMG_MAIN_EVENT_TYPE_CHANGE_ROLE，在 OnEvent 函数中对事件消息进行判断。
> 示例代码  
```
void TMGTestScene::OnEvent(ITMG_MAIN_EVENT_TYPE eventType,const char* data){
	switch (eventType) {
		case ITMG_MAIN_EVENT_TYPE_ENTER_ROOM:
		{
		//进行处理
		break;
	    	}
		...
            case ITMG_MAIN_EVENT_TYPE_CHANGE_ROLE:
		{
		//进行处理
		break;
		}
	}
}
```


### 成员状态变化
该事件在状态变化才通知，状态不变化的情况下不通知。如需实时获取成员状态，请在上层收到通知时缓存，事件消息为 ITMG_MAIN_EVNET_TYPE_USER_UPDATE，其中 data 包含两个信息，event_id 及 user_list，在 OnEvent 函数中需要对信息 event_id 进行判断。

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
void TMGTestScene::OnEvent(ITMG_MAIN_EVENT_TYPE eventType,const char* data){
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
ITMGRoom virtual const char* GetQualityTips()
```
> 示例代码  

```
ITMGContextGetInstance()->GetRoom()->GetQualityTips();
```

## 实时语音音频接口

### 获取麦克风设备数量
此接口用来获取麦克风设备数量。
>注意：需要在开启麦克风之前调用。

> 函数原型  
```
ITMGAudioCtrl virtual int GetMicListCount()
```
> 示例代码  
```
ITMGContextGetInstance()->GetAudioCtrl()->GetMicListCount();
```

### 枚举麦克风设备
此接口用来枚举麦克风设备。需要先获取麦克风设备数量。
>注意：需要在开启麦克风之前调用。

> 函数原型 
>  
```
ITMGAudioCtrl virtual int GetMicList(TMGAudioDeviceInfo* ppDeviceInfoList, int nCount)

class TMGAudioDeviceInfo
{
public:
	const char* pDeviceID;
	const char* pDeviceName;
};
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| ppDeviceInfoList    	|TMGAudioDeviceInfo   	|设备列表				|
| nCount    			|int     					|获取的麦克风设备数量	|
> 示例代码  

```
ITMGContextGetInstance()->GetAudioCtrl()->GetMicList(ppDeviceInfoList,nCount);
```

### 获取扬声器设备数量
此接口用来获取扬声器设备数量。
>注意：需要在开启扬声器之前调用。

> 函数原型  

```
ITMGAudioCtrl virtual int GetSpeakerListCount()
```
> 示例代码  

```
ITMGContextGetInstance()->GetAudioCtrl()->GetSpeakerListCount();
```

### 枚举扬声器设备
此接口用来枚举扬声器设备。需要先获取扬声器设备数量。
>注意：需要在开启扬声器之前调用。

> 函数原型  
```
ITMGAudioCtrl virtual int GetSpeakerList(TMGAudioDeviceInfo* ppDeviceInfoList, int nCount)

class TMGAudioDeviceInfo
{
public:
	const char* pDeviceID;
	const char* pDeviceName;
};
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| ppDeviceInfoList    	|TMGAudioDeviceInfo    	|设备列表				|
| nCount   			|int     					|获取的扬声器设备数量	|
> 示例代码  
```
ITMGContextGetInstance()->GetAudioCtrl()->GetSpeakerList(ppDeviceInfoList,nCount);
```

### 搜索麦克风设备
此接口用来搜索麦克风设备。
> 函数原型  
```
ITMGAudioCtrl virtual int SelectMic(const char* pMicID)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| pMicID    |char     |麦克风设备 ID|
> 示例代码  
```
const char* pMicID ="1";
ITMGContextGetInstance()->GetAudioCtrl()->SelectMic(pMicID);
```

### 麦克风开启关闭事件
此接口用来开启及关闭麦克风。
>注意:加入房间默认不打开麦克风及扬声器。

> 函数原型  
```
ITMGAudioCtrl virtual void EnableMic(bool bEnabled)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| bEnabled    |bool     |如果需要打开麦克风，则传入的参数为 true，如果关闭麦克风，则参数为 false		|
> 示例代码  
```
ITMGContextGetInstance()->GetAudioCtrl()->EnableMic(true);
ITMGContextGetInstance()->GetAudioCtrl()->EnableMic(false);
```

### 麦克风事件的回调
麦克风事件的回调调用函数 OnEvent，SDK 通过此回调通知成功调用了麦克风，事件消息为 ITMG_MAIN_EVENT_TYPE_ENABLE_MIC， ITMG_MAIN_EVENT_TYPE_DISABLE_MIC，在 OnEvent 函数中对事件消息进行判断。

> 示例代码  
```
void TMGTestScene::OnEvent(ITMG_MAIN_EVENT_TYPE eventType,const char* data){
	switch (eventType) {
		case ITMG_MAIN_EVENT_TYPE_ENTER_ROOM:
		{
		//进行处理
		break;
	        }
		...
            	case ITMG_MAIN_EVENT_TYPE_ENABLE_MIC:
		{
		//进行处理
		break;
		}
		case ITMG_MAIN_EVENT_TYPE_DISABLE_MIC:
		{
		//进行处理
		break;
		}
	}
}
```

### 麦克风状态获取
此接口获取麦克风状态。返回值 0 为关闭麦克风状态，返回值 1 为打开麦克风状态，返回值 2 为麦克风设备正在操作中，返回值 4 为设备没初始化好。
> 函数原型  
```
ITMGAudioCtrl virtual int GetMicState()
```
> 示例代码  
```
ITMGContextGetInstance()->GetAudioCtrl()->GetMicState();
```

### 获取麦克风实时音量
此接口用于获取麦克风实时音量，返回值为 int 类型。
> 函数原型  
```
ITMGAudioCtrl virtual int GetMicLevel()
```
> 示例代码  
```
ITMGContextGetInstance()->GetAudioCtrl()->GetMicLevel();
```

### 设置麦克风的软件音量
此接口用于设置麦克风的软件音量。参数 volume 用于设置麦克风的软件音量，当数值为 0 的时候表示静音，当数值为 100 的时候表示音量不增不减，默认数值为 100。
> 函数原型  
```
ITMGAudioCtrl virtual void SetMicVolume(int vol)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| vol    |int      |设置音量，范围 0 到 150|
> 示例代码  
```
int vol = 100;
ITMGContextGetInstance()->GetAudioCtrl()->SetMicVolume(vol);
```

### 获取麦克风的软件音量
此接口用于获取麦克风的软件音量。返回值为一个int类型数值。
> 函数原型  
```
ITMGAudioCtrl virtual int GetMicVolume()
```
> 示例代码  
```
ITMGContextGetInstance()->GetAudioCtrl()->GetMicVolume();
```
### 搜索扬声器设备
此接口用来搜索扬声器设备。
> 函数原型  
```
ITMGAudioCtrl virtual int SelectSpeaker(const char* pSpeakerID)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| pSpeakerID    |char     |扬声器设备 ID|
> 示例代码  
```
const char* pSpeakerID ="1";
ITMGContextGetInstance()->GetAudioCtrl()->SelectSpeaker(pSpeakerID);
```
### 扬声器开启关闭事件
此接口用于设置扬声器开启关闭。
> 函数原型  
```
ITMGAudioCtrl virtual void EnableSpeaker(bool enabled)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| enable   		|bool       	|如果需要关闭扬声器，则传入的参数为 false，如果打开扬声器，则参数为 true	|
> 示例代码  
```
ITMGContextGetInstance()->GetAudioCtrl()->EnableSpeaker(true);
ITMGContextGetInstance()->GetAudioCtrl()->EnableSpeaker(false);
```

### 扬声器事件的回调
扬声器事件回调，SDK 通过此回调通知成功调用了扬声器，事件消息为 ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER， ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER。
> 示例代码  
```
void TMGTestScene::OnEvent(ITMG_MAIN_EVENT_TYPE eventType,const char* data){
	switch (eventType) {
		case ITMG_MAIN_EVENT_TYPE_ENTER_ROOM:
		{
		//进行处理
		break;
	    	}
		...
        	case ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER:
		{
		//进行处理
		break;
		}
 		case ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER:
		{
		//进行处理
		break;
		}	
	}
}
```

### 扬声器状态获取
此接口用于扬声器状态获取。返回值为 int 类型数值。返回值 0 为关闭扬声器状态，返回值 1 为打开扬声器状态，返回值 2 为扬声器设备正在操作中，返回值 4 为设备没初始化好。
> 函数原型  
```
ITMGAudioCtrl virtual int GetSpeakerState()
```

> 示例代码  
```
ITMGContextGetInstance()->GetAudioCtrl()->GetSpeakerState();
```

### 获取扬声器实时音量
此接口用于获取扬声器实时音量。返回值为 int 类型数值，表示扬声器实时音量。
> 函数原型  
```
ITMGAudioCtrl virtual int GetSpeakerLevel()
```

> 示例代码  
```
ITMGContextGetInstance()->GetAudioCtrl()->GetSpeakerLevel();
```

### 设置扬声器的软件音量
此接口用于设置扬声器的软件音量。
>注意：参数 volume 用于设置扬声器的软件音量，当数值为 0 的时候表示静音，当数值为 100 的时候表示音量不增不减，默认数值为 100。

> 函数原型  
```
ITMGAudioCtrl virtual void SetSpeakerVolume(int vol)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| vol    |int        |设置音量，范围 0 到 150|
> 示例代码  
```
int vol = 100;
ITMGContextGetInstance()->GetAudioCtrl()->SetSpeakerVolume(vol);
```

### 获取扬声器的软件音量
此接口用于获取扬声器的软件音量。返回值为 int 类型数值，代表扬声器的软件音量。
>注意：Level 是实时音量，Volume 是扬声器的软件音量，最终声音音量相当于 Level*Volume%。举个例子：实时音量是数值是 100 的话，此时Volume的数值是 60，那么最终发出来的声音数值也是 60。

> 函数原型  
```
ITMGAudioCtrl virtual int GetSpeakerVolume()
```
> 示例代码  
```
ITMGContextGetInstance()->GetAudioCtrl()->GetSpeakerVolume();
```




## 实时语音伴奏相关接口

### 开始播放伴奏
调用此接口开始播放伴奏。支持 m4a、AAC、wav、mp3 一共四种格式。
注意：1、调用此 API，音量会重置。
2、下行权限不能启用此 API。
> 函数原型  
```
ITMGAudioEffectCtrl virtual void StartAccompany(const char* filePath, bool loopBack, int loopCount, int msTime) 
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    	|char	|播放伴奏的路径											|
| loopBack  	|bool	|是否混音发送，一般都设置为 true，即其他人也能听到伴奏	|
| loopCount	|int 		|循环次数，数值为 -1 表示无限循环							|
| msTime	|int   	|延迟时间												|
> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->StartAccompany(filePath,true,-1,0);
```

### 播放伴奏的回调
开始播放伴奏完成后，回调函数调用 OnEvent，事件消息为 ITMG_MAIN_EVENT_TYPE_ACCOMPANY_FINISH，在 OnEvent 函数中对事件消息进行判断。
> 示例代码  
```
void TMGTestScene::OnEvent(ITMG_MAIN_EVENT_TYPE eventType,const char* data){
	switch (eventType) {
		case ITMG_MAIN_EVENT_TYPE_ENTER_ROOM:
		{
		//进行处理
		break;
	   	}
		...
        case ITMG_MAIN_EVENT_TYPE_ACCOMPANY_FINISH:
		{
		//进行处理
		break;
		}
	}
}
```

### 停止播放伴奏
调用此接口停止播放伴奏。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int StopAccompany(int duckerTime)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| duckerTime	|int             |淡出时间|

> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->StopAccompany(0);
```

### 伴奏是否播放完毕
如果播放完毕，返回值为 true，如果没播放完，返回值为 false。
> 函数原型  
```
ITMGAudioEffectCtrl virtual bool IsAccompanyPlayEnd()
```
> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->IsAccompanyPlayEnd();
```

### 暂停播放伴奏
调用此接口暂停播放伴奏。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int PauseAccompany()
```
> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->PauseAccompany();
```

### 重新播放伴奏
此接口用于重新播放伴奏。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int ResumeAccompany()
```
> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->ResumeAccompany();
```

### 设置自己是否可以听到伴奏
此接口用于设置自己是否可以听到伴奏。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int EnableAccompanyPlay(bool enable)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| enable    |bool             |是否能听到|
> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->EnableAccompanyPlay(false);
ITMGContextGetInstance()->GetAudioEffectCtrl()->EnableAccompanyPlay(true);
```

### 设置他人是否也可以听到伴奏
设置他人是否也可以听到伴奏。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int EnableAccompanyLoopBack(bool enable)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| enable    |bool             |是否能听到|

> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->EnableAccompanyLoopBack(false);
ITMGContextGetInstance()->GetAudioEffectCtrl()->EnableAccompanyLoopBack(true);
```

### 设置伴奏音量
设置 DB 音量，默认值为 100，数值大于 100 音量增益，数值小于 100 音量减益，值域为 0-200。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int SetAccompanyVolume(int vol)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| vol    |int             |音量数值|

> 示例代码  
```
int vol=100;
ITMGContextGetInstance()->GetAudioEffectCtrl()->SetAccompanyVolume(vol);
```

### 获取播放伴奏的音量
此接口用于获取 DB 音量。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int GetAccompanyVolume()
```
> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->GetAccompanyVolume();
```

### 获得伴奏播放进度
以下两个接口用于获得伴奏播放进度。需要注意：Current / Total = 当前循环次数，Current % Total = 当前循环播放位置。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int GetAccompanyFileTotalTimeByMs()
ITMGAudioEffectCtrl virtual int GetAccompanyFileCurrentPlayedTimeByMs()
```
> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->GetAccompanyFileTotalTimeByMs();
ITMGContextGetInstance()->GetAudioEffectCtrl()->GetAccompanyFileCurrentPlayedTimeByMs();
```

### 设置播放进度
此接口用于设置播放进度。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int SetAccompanyFileCurrentPlayedTimeByMs(unsigned int time)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| time    |int                |播放进度，以毫秒为单位|

> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->SetAccompanyFileCurrentPlayedTimeByMs(time);
```


### 启动耳返
此接口用于启动耳返。
> 函数原型  
``` 
ITMGAudioCtrl virtual int EnableLoopBack(bool enable)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| enable    |bool         |设置是否启动|
> 示例代码  
```
ITMGContextGetInstance()->GetAudioCtrl()->EnableLoopBack(true);
ITMGContextGetInstance()->GetAudioCtrl()->EnableLoopBack(false);
```

## 实时语音音效相关接口


### 播放音效
此接口用于播放音效。参数中音效 id 需要 App 侧进行管理，唯一标识一个独立文件。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int PlayEffect(int soundId,  const char* filePath, bool loop, double pitch, double pan, double gain)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId  	|int        	|音效 id													|
| filePath    	|char    	|音效路径												|
| loop    		|bool  	|是否重复播放											|
| pitch    	|double	|播放频率，默认为 1.0，该值越小播放速度越慢、时间越长		|
| pan    		|double	|声道，取值范围为 -1.0 到 1.0 之间，-1.0 表示只开启左声道	|
| gain    		|double	|增益音量，取值范围为 0.0 到 1.0 之间，默认为 1.0			|
> 示例代码  
```
double pitch = 1.0;
double pan = 0.0;
double gain = 0.0;
ITMGContextGetInstance()->GetAudioEffectCtrl()->PlayEffect(soundId,filepath,true,pitch,pan,gain);
```

### 暂停播放音效
此接口用于暂停播放音效。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int PauseEffect(int soundId)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId    |int                    |音效 id|

> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->PauseEffect(soundId);
```

### 暂停所有音效
调用此接口暂停所有音效
> 函数原型  
```
ITMGAudioEffectCtrl virtual int PauseAllEffects()
```
> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->PauseAllEffects();
```

### 重新播放音效
此接口用于重新播放音效。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int ResumeEffect(int soundId)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId    |int                    |音效 id|
> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->ResumeEffect(soundId);
```

### 重新播放所有音效
调用此接口重新播放所有音效。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int ResumeAllEffects()
```
> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->ResumeAllEffects();
```

### 停止播放音效
此接口用于停止播放音效。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int StopEffect(int soundId)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| soundId    |int                    |音效 id|
> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->StopEffect(soundId);
```

### 停止播放所有音效
调用此接口停止播放所有音效。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int StopAllEffects()
```
> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->StopAllEffects();
```

### 获取播放音效的音量
获取播放音效的音量，为线性音量，默认值为 100，数值大于 100 为增益效果，数值小于 100 为减益效果。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int GetEffectsVolume()
```
> 示例代码  
```
ITMGContextGetInstance()->GetAudioEffectCtrl()->GetEffectsVolume();
```

### 设置播放音效的音量
调用此接口设置播放音效的音量。
> 函数原型  
```
ITMGAudioEffectCtrl virtual int SetEffectsVolume(int volume)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| volume    |int                    |音量数值|

> 示例代码  
```
int volume=1;
ITMGContextGetInstance()->GetAudioEffectCtrl()->SetEffectsVolume(volume);
```


### 变声特效
调用此函数设置变声特效。
> 函数原型  
```
TMGAudioEffectCtrl int setVoiceType(int type)
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
ITMGContextGetInstance()->GetAudioEffectCtrl()->setVoiceType(0);
```

## 离线语音接入
### 1.离线语音技术接入初始化
初始化需要传入鉴权 access token 给 TLS 相关函数。鉴权的获取详细流程见[游戏多媒体引擎密钥文档](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/GME%20Key%20Manual.md)。  
> 函数原型  
```
QAVSDK_API int QAVSDK_CALL QAVSDK_SIG_GenSig(unsigned int appId,const char* uin,const char* privateKey,char* retSigBuff,unsigned int buffLenght);
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| appId  		|int   		|来自腾讯云控制台的 SdkAppId 号码				|
| uin    		|char		|唯一标识一个用户，规则由 App 开发者自行制定		|
| privateKey	|char 		|来自腾讯云控制台的鉴权							|
| retSigBuff 	|char   		|返回的 sig										|
| buffLenght	|int   		|返回的 sig 长度									|
```
ITMGPTT virtual int ApplyAccessToken(const char* accessToken)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| accessToken    |char                       |QAVSDK_CALL QAVSDK_SIG_GenSig 函数返回的 accessToken|
> 示例代码  
```
char openID = 10000;
int buffLength = 1024;
char* sigBuff = new char[buffLength];
const char* privateKey  = @"自己在官网的 key";

int retCode = QAVSDK_SIG_GenSig(atoi(SDKAPPID3RD),openID,privateKey,sigBuff,buffLength);
ITMGContextGetInstance()->GetPTT()->ApplyAccessToken(sigBuff);
```

### 限制最大语音信息时长
限制最大语音消息的长度，最大支持 60 秒。
> 函数原型  
```
ITMGPTT virtual void SetMaxMessageLength(int msTime)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| msTime    |int                    |语音时长|
> 示例代码  
```
int msTime = 10;
ITMGContextGetInstance()->GetPTT()->SetMaxMessageLength(msTime);
```

### 启动录音
此接口用于启动录音。
> 函数原型  
```
ITMGPTT virtual void StartRecording(const char* fileDir)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| fileDir    |char                     |存放的语音路径|
> 示例代码  
```
ITMGContextGetInstance()->GetPTT()->SetMaxMessageLength(fileDir);
```

### 启动录音的回调
启动录音完成后的回调调用函数 OnEvent，事件消息为 ITMG_MAIN_EVNET_TYPE_PTT_RECORD_COMPLETE， 在 OnEvent 函数中对事件消息进行判断。

> 示例代码  
```
void TMGTestScene::OnEvent(ITMG_MAIN_EVENT_TYPE eventType,const char* data){
	switch (eventType) {
		case ITMG_MAIN_EVENT_TYPE_ENTER_ROOM:
		{
		//进行处理
		break;
	    }
		...
        case ITMG_MAIN_EVNET_TYPE_PTT_RECORD_COMPLETE:
		{
		//进行处理
		break;
		}
	}
}

```

### 停止录音
此接口用于停止录音。
> 函数原型  
```
ITMGPTT virtual int StopRecording()
```
> 示例代码  
```
ITMGContextGetInstance()->GetPTT()->StopRecording();
```

### 取消录音
调用此接口取消录音。
> 函数原型  
```
ITMGPTT virtual int CancelRecording()
```
> 示例代码  
```
ITMGContextGetInstance()->GetPTT()->CancelRecording();
```

### 上传语音文件
此接口用于上传语音文件。
> 函数原型  
```
ITMGPTT virtual void UploadRecordedFile(const char* filePath)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    |char                      |上传的语音路径|
> 示例代码  
```
ITMGContextGetInstance()->GetPTT()->UploadRecordedFile(filePath);
```

### 上传语音完成的回调
上传语音完成后，事件消息为 ITMG_MAIN_EVNET_TYPE_PTT_UPLOAD_COMPLETE， 在 OnEvent 函数中对事件消息进行判断。
```
void TMGTestScene::OnEvent(ITMG_MAIN_EVENT_TYPE eventType,const char* data){
	switch (eventType) {
		case ITMG_MAIN_EVENT_TYPE_ENTER_ROOM:
		{
		//进行处理
		break;
	    }
		...
        case ITMG_MAIN_EVNET_TYPE_PTT_UPLOAD_COMPLETE:
		{
		//进行处理
		break;
		}
	}
}
```

### 下载语音文件
此接口用于下载语音文件。
> 函数原型  
```
ITMGPTT virtual void DownloadRecordedFile(const char* fileId,const char* filePath)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| fileId  		|char  	|文件的 url 路径	|
| filePath 	|char 	|文件的下载路径	|
> 示例代码  
```
ITMGContextGetInstance()->GetPTT()->DownloadRecordedFile(fileID,filePath);
```

### 下载语音文件完成回调
下载语音完成后，事件消息为 ITMG_MAIN_EVNET_TYPE_PTT_DOWNLOAD_COMPLETE， 在 OnEvent 函数中对事件消息进行判断。
```
void TMGTestScene::OnEvent(ITMG_MAIN_EVENT_TYPE eventType,const char* data){
	switch (eventType) {
		case ITMG_MAIN_EVENT_TYPE_ENTER_ROOM:
		{
		//进行处理
		break;
	    }
		...
        case ITMG_MAIN_EVNET_TYPE_PTT_DOWNLOAD_COMPLETE:
		{
		//进行处理
		break;
		}
	}
}
```

### 播放语音
此接口用于播放语音。
> 函数原型  
```
ITMGPTT virtual void PlayRecordedFile(const char* filePath)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    |char                      |文件的路径|
> 示例代码  
```
ITMGContextGetInstance()->GetPTT()->PlayRecordedFile(filePath);
```

### 播放语音的回调
播放语音的回调，事件消息为 ITMG_MAIN_EVNET_TYPE_PTT_PLAY_COMPLETE， 在 OnEvent 函数中对事件消息进行判断。
```
void TMGTestScene::OnEvent(ITMG_MAIN_EVENT_TYPE eventType,const char* data){
	switch (eventType) {
		case ITMG_MAIN_EVENT_TYPE_ENTER_ROOM:
		{
		//进行处理
		break;
	    }
		...
        case ITMG_MAIN_EVNET_TYPE_PTT_PLAY_COMPLETE:
		{
		//进行处理
		break;
		}
	}
}
```

### 停止播放语音
此接口用于停止播放语音。
> 函数原型  
```
ITMGPTT virtual int StopPlayFile()
```
> 示例代码  
```
ITMGContextGetInstance()->GetPTT()->StopPlayFile();
```
### 获取语音文件的大小
通过此接口，获取语音文件的大小。
> 函数原型  
```
ITMGPTT virtual int GetFileSize(const char* filePath)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    |char                     |语音文件的路径|
> 示例代码  
```
ITMGContextGetInstance()->GetPTT()->GetFileSize(filePath);
```

### 获取语音文件的时长
此接口用于获取语音文件的时长。
> 函数原型  
```
ITMGPTT virtual int GetVoiceFileDuration(const char* filePath)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| filePath    |char                     |语音文件的路径|
> 示例代码  
```
ITMGContextGetInstance()->GetPTT()->GetVoiceFileDuration(filePath);
```



### 将指定的语音文件翻译成文字
此接口用于将指定的语音文件翻译成文字。
> 函数原型  
```
ITMGPTT virtual void SpeechToText(const char* fileID)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| fileID    |char                     |语音文件 url|
> 示例代码  
```
ITMGContextGetInstance()->GetPTT()->SpeechToText(fileID);
```

### 翻译回调
将指定的语音文件翻译成文字的回调，事件消息为 ITMG_MAIN_EVNET_TYPE_PTT_SPEECH2TEXT_COMPLETE， 在 OnEvent 函数中对事件消息进行判断。
```
void TMGTestScene::OnEvent(ITMG_MAIN_EVENT_TYPE eventType,const char* data){
	switch (eventType) {
		case ITMG_MAIN_EVENT_TYPE_ENTER_ROOM:
		{
		//进行处理
		break;
	    }
		...
        case ITMG_MAIN_EVNET_TYPE_PTT_SPEECH2TEXT_COMPLETE:
		{
		//进行处理
		break;
		}
	}
}
```

