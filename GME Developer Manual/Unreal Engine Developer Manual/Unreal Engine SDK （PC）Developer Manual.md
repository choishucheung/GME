## 简介
欢迎使用腾讯云游戏多媒体引擎 SDK 。为方便 Unreal Engine 开发者调试和接入腾讯云游戏多媒体引擎产品 API，这里向您介绍适用于 Unreal Engine 开发的接口技术文档。

## 目录
初始化相关接口

实时语音房间事件接口

实时语音音频接口

高级接口

回调消息列表

## 使用流程图
![image](Image/i0.png)


### 使用GME 重要事项

|重要接口     | 接口含义|
| ------------- |-------------|
|Init		    		|初始化 GME 	|
|Poll    				|触发事件回调	|
|EnterRoom	 		|进房  			|
|EnableMic	 		|开麦克风 		|
|EnableSpeaker		|开扬声器 		|

**说明**

**GME 的接口调用成功后返回值为 QAVError.OK，数值为0。**

**GME 的接口调用要在同一个线程下。**

**GME 加入房间需要鉴权，请参考文档关于鉴权部分内容。**

## 初始化相关接口
未初始化前，SDK 处于未初始化阶段，需要初始化鉴权后，通过初始化 SDK，才可以进房。


|接口     | 接口含义   
| ------------- |:-------------:|
|Init    				       			|初始化 GME 
|Poll    				       			|触发事件回调
|Pause    				       		|系统暂停
|Resume 				       		|系统恢复
|Uninit    				       		|反初始化 GME 

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


### 设置单例
在 EnterRoom 函数调用之前要先获取 ITMGContext ，所有调用都从 ITMGContext 开始，由ITMGDelegate 回调回传给应用，必须首先设置。
>示例代码
```
ITMGContext* context = ITMGContextGetInstance();
context->SetTMGDelegate(this);
```
### 消息传递
接口类采用 Delegate 方法用于向应用程序发送回调通知，消息类型参考 ITMG_MAIN_EVENT_TYPE，data 在 Windows 平台下是 json 字符串格式， 具体 key-value 参见说明文档。

> 示例代码 
```
//函数实现：
//UEDemoLevelScriptActor.h:
class UEDEMO1_API AUEDemoLevelScriptActor : public ALevelScriptActor, public SetTMGDelegate
{
public:
	void OnEvent(ITMG_MAIN_EVENT_TYPE eventType, const char* data);
｝

//UEDemoLevelScriptActor.cpp:
void AUEDemoLevelScriptActor::OnEvent(ITMG_MAIN_EVENT_TYPE eventType, const char* data){
	//在此对eventType进行判断及操作
}
```

### 初始化 SDK

此接口需要来自腾讯云控制台的 SdkAppId 号码作为参数，再加上 openId，这个 openId 是唯一标识一个用户，规则由 App 开发者自行制定，App 内不重复即可（目前只支持 INT64）。
初始化 SDK 之后才可以进房。
> 函数原型 
```
ITMGContext virtual void Init(const char* sdkAppId, const char* openId)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| sdkAppId    	|char*   	|来自腾讯云控制台的 SdkAppId 号码					|
| openID    		|char*   	|OpenID 只支持 Int64 类型（转为string传入），必须大于 10000，用于标识用户 	|

> 示例代码  

```
std::string appid = TCHAR_TO_UTF8(CurrentWidget->editAppID->GetText().ToString().operator*());
std::string userId = TCHAR_TO_UTF8(CurrentWidget->editUserID->GetText().ToString().operator*());
ITMGContextGetInstance()->Init(appid.c_str(), userId.c_str());
```


### 触发事件回调

通过在 Tick 里面周期的调用 Poll 可以触发事件回调。
> 函数原型

```
class ITMGContext {
protected:
    virtual ~ITMGContext() {}
    
public:    	
	virtual void Poll()= 0;
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


### 系统暂停

当系统发生 Pause 事件时，需要同时通知引擎进行 Pause。
> 函数原型

```
ITMGContext int Pause()
```

### 系统恢复
当系统发生 Resume 事件时，需要同时通知引擎进行 Resume。
> 函数原型

```
ITMGContext  int Resume()
```
### 反初始化 SDK
反初始化 SDK，进入未初始化状态

> 函数原型 
```
ITMGContext int Uninit()

```
> 示例代码  
```
ITMGContext* context = ITMGContextGetInstance();
context->Uninit();
```


## 实时语音房间相关接口
初始化之后，SDK 调用进房后进去了房间，才可以进行实时语音通话。

|接口     | 接口含义   
| ------------- |:-------------:|
|GenAuthBuffer    	|初始化鉴权
|EnterRoom   		|加入房间
|IsRoomEntered   	|是否已经进入房间
|ExitRoom 			|退出房间
|ChangeRoomType 	|修改用户房间音频类型
|GetRoomType 		|获取用户房间音频类型


### 实时语音鉴权信息
生成 AuthBuffer，用于相关功能的加密和鉴权，相关参数获取及详情见[GME密钥文档](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/GME%20Key%20Manual.md)。  

> 函数原型
```
QAVSDK_API int QAVSDK_CALL QAVSDK_AuthBuffer_GenAuthBuffer(unsigned int appId, unsigned int authId, const char* strOpenID, const char* key, unsigned int expTime, unsigned int privilegeMap, unsigned char* retAuthBuff, unsigned int* buffLenght);
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| appId    		|int   		|来自腾讯云控制台的 SdkAppId 号码		|
| authId    		|int  		|要加入的房间名							|
| strOpenID  		|char*    		|用户标识								|
| key    			|char*	    	|来自腾讯云控制台的密钥					|
| expTime    		|int   		|authBuffer 超时时间						|
| privilegeMap   	|int    		|权限（ITMG_AUTH_BITS_DEFAULT 代表拥有全部权限）|
| retAuthBuff   	|char*    		|返回的 authbuff							|
| buffLenght   	|int    		|返回的authbuff的长度					|



> 示例代码  
```
unsigned int bufferLen = 512;
unsigned char retAuthBuff[512] = {0};
unsigned int expTime = cocos2d::utils::gettime()+60*60*24*3;

QAVSDK_AuthBuffer_GenAuthBuffer(atoi(SDKAPPID3RD), roomId, "10001", AUTHKEY, expTime, ITMG_AUTH_BITS_DEFAULT, retAuthBuff, &bufferLen);
```

### 加入房间
用生成的鉴权信息进房，会收到消息为 ITMG_MAIN_EVENT_TYPE_ENTER_ROOM 的回调。加入房间默认不打开麦克风及扬声器。
如果普通语音进房，业务方面无涉及小队语音需求，则使用普通进房接口。详细信息请查阅[GME小队语音文档](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/GME%20TeamAudio%20Manual.md)。

> 函数原型

```
ITMGContext virtual void EnterRoom(int relationId, ITMG_ROOM_TYPE roomType, const char* authBuff, int buffLen)//普通进房接口
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| relationId			|int   				|房间号 			|
| roomType 			|ITMG_ROOM_TYPE	|房间音频类型	|
| authBuffer    		|char*     				|鉴权码			|
| buffLen   			|int   				|鉴权码长度		|

|音频类型     	|含义|参数|适用场景|音量类型|控制台推荐采样率设置|
| ------------- |------------ | ---- |---- |---- |---- |
| ITMG_ROOM_TYPE_FLUENCY			|流畅音质	|1|流畅优先、超低延迟实时语音，应用在游戏内开黑场景，适用于FPS、MOBA等类型的游戏；								|扬声器：通话音量；耳机：媒体音量	|如对音质无特殊需求，16K采样率即可；					|
| ITMG_ROOM_TYPE_STANDARD			|标准音质	|2|音质较好，延时适中，适用于狼人杀、棋牌等休闲游戏的实时通话场景；													|扬声器：通话音量；耳机：媒体音量	|根据对音质的需求，可以选择16k/48k采样率				|
| ITMG_ROOM_TYPE_HIGHQUALITY		|高清音质	|3|超高音质，延时相对大一些，适用于音乐舞蹈类游戏以及语音社交类APP；适用于播放音乐、线上K歌等有高音质要求的场景；	|扬声器：媒体音量；耳机：媒体音量	|为了保证最佳效果，建议控制台设置48k采样率的高音质配置	|

- 如对音量类型或场景有特殊需求，请联系一线客服反馈；
- 控制台采样率设置会直接影响游戏语音效果，请在[控制台](https://console.cloud.tencent.com/gamegme)上再次确认采样率设置是否符合项目使用场景。


> 示例代码  

```
ITMGContext* context = ITMGContextGetInstance();
context->EnterRoom(roomId, ITMG_ROOM_TYPE_STANDARD, (char*)retAuthBuff,bufferLen);//普通语音进房示例代码
```


#### 小队语音房间
详细接入细节请查阅[GME小队语音文档](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/GME%20TeamAudio%20Manual.md)。

> 函数原型
```
ITMGContext virtual void EnterTeamRoom(int relationId, ITMG_ROOM_TYPE roomType, const char* authBuff, int buffLen, int teamId, int gameAudioMode)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| relationId			|int   	|房间号 					|
| roomType 		|ITMG_ROOM_TYPE	|房间音频类型							|
| authBuffer    		|char*    	|鉴权码												|
| buffLen   			|int   	|鉴权码长度											|
| teamId    		|int    	|加入的小队语音队伍标识码（不能为 0 ）	|
| audioMode    	|int    	|0 代表全局语音，1 代表小队语音			|


|音频类型     	|含义|参数|适用场景|音量类型|控制台推荐采样率设置|
| ------------- |------------ | ---- |---- |---- |---- |
| ITMG_ROOM_TYPE_FLUENCY			|流畅音质	|1|流畅优先、超低延迟实时语音，应用在游戏内开黑场景，适用于FPS、MOBA等类型的游戏；								|扬声器：通话音量；耳机：媒体音量	|如对音质无特殊需求，16K采样率即可；					|
| ITMG_ROOM_TYPE_STANDARD			|标准音质	|2|音质较好，延时适中，适用于狼人杀、棋牌等休闲游戏的实时通话场景；													|扬声器：通话音量；耳机：媒体音量	|根据对音质的需求，可以选择16k/48k采样率				|
| ITMG_ROOM_TYPE_HIGHQUALITY		|高清音质	|3|超高音质，延时相对大一些，适用于音乐舞蹈类游戏以及语音社交类APP；适用于播放音乐、线上K歌等有高音质要求的场景；	|扬声器：媒体音量；耳机：媒体音量	|为了保证最佳效果，建议控制台设置48k采样率的高音质配置	|


> 示例代码  
```
ITMGContext* context = ITMGContextGetInstance();
context->EnterRoom(roomId, ITMG_ROOM_TYPE_STANDARD, (char*)retAuthBuff,bufferLen,1000,0);
```


### 加入房间事件的回调
加入房间完成后会发送信息 ITMG_MAIN_EVENT_TYPE_ENTER_ROOM，在 OnEvent 函数中进行判断。
> 代码说明
```
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
加入房间完成后会有回调，消息为 ITMG_MAIN_EVENT_TYPE_EXIT_ROOM。
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

### 修改用户房间音频类型
此接口用于修改用户房间音频类型，结果参见回调事件，事件类型为 ITMG_MAIN_EVENT_TYPE_CHANGE_ROOM_TYPE。
> 函数原型  
```
IITMGContext TMGRoom public void ChangeRoomType((ITMG_ROOM_TYPE roomType)
```


|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| roomType    |ITMG_ROOM_TYPE    |希望房间切换成的类型，房间音频类型参考 EnterRoom 接口|

> 示例代码  
```
ITMGContext* context = ITMGContextGetInstance();
ITMGContextGetInstance()->GetRoom()->ChangeRoomType(ITMG_ROOM_TYPE_FLUENCY);
```


### 获取用户房间音频类型
此接口用于获取用户房间音频类型，返回值为房间音频类型，房间音频类型参考 EnterRoom 接口。

> 函数原型  
```
IITMGContext TMGRoom public  int GetRoomType()
```

> 示例代码  
```
ITMGContext* context = ITMGContextGetInstance();
ITMGContextGetInstance()->GetRoom()->GetRoomType();
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
void TMGTestScene::OnEvent(ITMG_MAIN_EVENT_TYPE eventType,const char* data) {
	if (ITMGContext.ITMG_MAIN_EVENT_TYPE.ITMG_MAIN_EVENT_TYPE_CHANGE_ROOM_TYPE == type)
        {
		//对房间类型事件进行处理
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
			    //有成员发送音频包
			    break;
		    case ITMG_EVENT_ID_USER_NO_AUDIO:
			    //有成员停止发送音频包
			    break;
 		    }
		break;
		}
	}
}
```

### 消息详情

|消息     | 消息代表的意义   
| ------------- |:-------------:|
|ITMG_MAIN_EVENT_TYPE_ENTER_ROOM    				       |进入音视频房间消息|
|ITMG_MAIN_EVENT_TYPE_EXIT_ROOM    				         	|退出音视频房间消息|
|ITMG_MAIN_EVENT_TYPE_ROOM_DISCONNECT    		       |房间因为网络等原因断开消息|
|ITMG_MAIN_EVENT_TYPE_CHANGE_ROOM_TYPE				|房间类型变化事件|

### 消息对应的Data详情
|消息     | Data         |例子|
| ------------- |:-------------:|------------- |
| ITMG_MAIN_EVENT_TYPE_ENTER_ROOM    				|result; error_info					|{"error_info":"","result":0}|
| ITMG_MAIN_EVENT_TYPE_EXIT_ROOM    				|result; error_info  					|{"error_info":"","result":0}|
| ITMG_MAIN_EVENT_TYPE_ROOM_DISCONNECT    		|result; error_info  					|{"error_info":"waiting timeout, please check your network","result":0}|
| ITMG_MAIN_EVENT_TYPE_CHANGE_ROOM_TYPE    		|result; error_info; new_room_type	|{"error_info":"","new_room_type":0,"result":0}|


## 实时语音音频接口
初始化 SDK 之后进房，在房间中，才可以调用实时音频语音相关接口。

|接口     | 接口含义   
| ------------- |:-------------:|
|PauseAudio    				       	|暂停音频引擎
|ResumeAudio    				      	|恢复音频引擎
|GetMicListCount    				       	|获取麦克风设备数量
|GetMicList    				      	|枚举麦克风设备
|GetSpeakerListCount    				      	|获取扬声器设备数量
|GetSpeakerList    				      	|枚举扬声器设备
|SelectMic    				      	|搜索麦克风设备
|SelectSpeaker    				|搜索扬声器设备
|EnableMic    						|开关麦克风
|GetMicState    						|获取麦克风状态
|GetMicLevel    						|获取实时麦克风音量
|SetMicVolume    					|设置麦克风音量
|GetMicVolume    					|获取麦克风音量
|EnableSpeaker    					|开关扬声器
|GetSpeakerState    					|获取扬声器状态
|GetSpeakerLevel    					|获取实时扬声器音量
|SetSpeakerVolume    				|设置扬声器音量
|GetSpeakerVolume    				|获取扬声器音量
|EnableLoopBack    					|开关耳返

### 暂停音频引擎的采集和播放
调用此接口暂停音频引擎的采集和播放，只在进房后有效。
在 EnterRoom 接口调用成功之后之后就会占用麦克风权限，期间其他程序无法进行麦克风采集。调用 EnableMic(false) 无法释放麦克风占用。
如果确实需要释放麦克风，请调用 PauseAudio 接口。调用 PauseAudio 接口后会整个暂停引擎，调用 ResumeAudio 接口可恢复音频采集。
> 函数原型  

```
ITMGContext ITMGAudioCtrl int PauseAudio()
```
> 示例代码  

```
ITMGContextGetInstance()->GetAudioCtrl()->PauseAudio();
```

### 恢复音频引擎的采集和播放
调用此接口恢复音频引擎的采集和播放，只在进房后有效。
> 函数原型  

```
ITMGContext ITMGAudioCtrl int ResumeAudio()
```
> 示例代码  

```
ITMGContextGetInstance()->GetAudioCtrl()->ResumeAudio();
```



### 获取麦克风设备数量
此接口用来获取麦克风设备数量。

> 函数原型  
```
ITMGAudioCtrl virtual int GetMicListCount()
```
> 示例代码  
```
ITMGContextGetInstance()->GetAudioCtrl()->GetMicListCount();
```

### 枚举麦克风设备
此接口用来枚举麦克风设备。配合 GetMicListCount 接口使用。

> 函数原型 
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



### 选中麦克风设备
此接口用来选中麦克风设备。如果不调用或者传空字符，则选中系统默认设备。
> 函数原型  
```
ITMGAudioCtrl virtual int SelectMic(const char* pMicID)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| pMicID    |char*      |麦克风设备 ID|
> 示例代码  
```
const char* pMicID ="1";
ITMGContextGetInstance()->GetAudioCtrl()->SelectMic(pMicID);
```

### 开启关闭麦克风
此接口用来开启关闭麦克风。加入房间默认不打开麦克风及扬声器。

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
此接口用于获取麦克风状态，返回值 0 为关闭麦克风状态，返回值 1 为打开麦克风状态，返回值 2 为麦克风设备正在操作中，返回值 3 为麦克风设备不存在，返回值 4 为设备没初始化好。
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

### 获取扬声器设备数量
此接口用来获取扬声器设备数量。

> 函数原型  

```
ITMGAudioCtrl virtual int GetSpeakerListCount()
```
> 示例代码  

```
ITMGContextGetInstance()->GetAudioCtrl()->GetSpeakerListCount();
```

### 枚举扬声器设备
此接口用来枚举扬声器设备。配合 GetSpeakerListCount 接口使用。

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

### 选中扬声器设备
此接口用来选中播放设备。如果不调用或者传入空字符串，则选中系统默认播放设备。
> 函数原型  
```
ITMGAudioCtrl virtual int SelectSpeaker(const char* pSpeakerID)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| pSpeakerID    |char*      |扬声器设备 ID|
> 示例代码  
```
const char* pSpeakerID ="1";
ITMGContextGetInstance()->GetAudioCtrl()->SelectSpeaker(pSpeakerID);
```

### 开启关闭扬声器
此接口用于开启关闭扬声器。

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
此接口用于扬声器状态获取。返回值为 int 类型数值。返回值 0 为关闭扬声器状态，返回值 1 为打开扬声器状态，返回值 3 为扬声器设备不存在，返回值 2 为扬声器设备正在操作中，返回值 4 为设备没初始化好。
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
### 消息详情

|消息     | 消息代表的意义   
| ------------- |:-------------:|
|ITMG_MAIN_EVENT_TYPE_ENABLE_MIC    				       |打开麦克风消息|
|ITMG_MAIN_EVENT_TYPE_DISABLE_MIC    				       |关闭麦克风消息|
|ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER				       |打开扬声器消息|
|ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER				       |关闭扬声器消息|

### 消息对应的Data详情
|消息     | Data         |例子|
| ------------- |:-------------:|------------- |
| ITMG_MAIN_EVENT_TYPE_ENABLE_MIC    				|result; error_info  					|{"error_info":"","result":0}|
| ITMG_MAIN_EVENT_TYPE_DISABLE_MIC    				|result; error_info  					|{"error_info":"","result":0}|
| ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER    			|result; error_info  					|{"error_info":"","result":0}|
| ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER    			|result; error_info  					|{"error_info":"","result":0}|




## 高级 API

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
ITMGContext virtual void SetLogLevel(int logLevel, bool enableWrite, bool enablePrint)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| logLevel    		|int   		|打印日志级别		|
| enableWrite    	|bool   				|是否写文件，默认为是	|
| enablePrint    	|bool   				|是否写控制台，默认为是	|
>ITMG_LOG_LEVEL 对照表

|ITMG_LOG_LEVEL|意义|
| -------------------------------	|----------------------	|
|TMG_LOG_LEVEL_NONE=0		|不打印日志			|
|TMG_LOG_LEVEL_ERROR=1		|打印错误日志（默认）	|
|TMG_LOG_LEVEL_INFO=2			|打印提示日志		|
|TMG_LOG_LEVEL_DEBUG=3		|打印开发调试日志	|
|TMG_LOG_LEVEL_VERBOSE=4		|打印高频日志		|

> 示例代码  
```
ITMGContext* context = ITMGContextGetInstance();
context->SetLogLevel(0,true,true);
```

### 设置打印日志路径
用于设置打印日志路径。
默认路径为：

|平台     |路径        |
| ------------- |-------------|
|Windows 	|%appdata%\Tencent\GME\ProcessName|
|iOS    		|Application/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Documents|
|Android	|/sdcard/Android/data/xxx.xxx.xxx/files|
|Mac    		|/Users/username/Library/Containers/xxx.xxx.xxx/Data/Documents|

> 函数原型
```
ITMGContext virtual void SetLogPath(const char* logDir) 
```

|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| logDir    		|char*    		|路径|
> 示例代码  
```
cosnt char* logDir = ""//自行设置路径
ITMGContext* context = ITMGContextGetInstance();
context->SetLogPath(logDir);
```

### 加入音频数据黑名单
将某个 id 加入音频数据黑名单。返回值为 0 表示调用失败。
> 函数原型  

```
ITMGContext ITMGAudioCtrl int AddAudioBlackList(const char* openId)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| openId    |char*       |需添加黑名单的id|
> 示例代码  

```
ITMGContextGetInstance()->GetAudioCtrl()->AddAudioBlackList(openId);
```

### 移除音频数据黑名单
将某个 id 移除音频数据黑名单。返回值为 0 表示调用失败。
> 函数原型  

```
ITMGContext ITMGAudioCtrl int RemoveAudioBlackList(const char* openId)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| openId    |char*       |需移除黑名单的id|
> 示例代码  

```
ITMGContextGetInstance()->GetAudioCtrl()->RemoveAudioBlackList(openId);
```


## 消息列表

>消息列表：

|消息     | 消息代表的意义   
| ------------- |:-------------:|
|ITMG_MAIN_EVENT_TYPE_ENTER_ROOM    				|进入音频房间消息
|ITMG_MAIN_EVENT_TYPE_EXIT_ROOM    				|退出音频房间消息
|ITMG_MAIN_EVENT_TYPE_ROOM_DISCONNECT			|房间因为网络等原因断开消息
|ITMG_MAIN_EVENT_TYPE_CHANGE_ROOM_TYPE			|房间类型变化事件
|ITMG_MAIN_EVENT_TYPE_ENABLE_MIC    				|打开麦克风消息
|ITMG_MAIN_EVENT_TYPE_DISABLE_MIC    				|关闭麦克风消息
|ITMG_MAIN_EVENT_TYPE_MIC_NEW_DEVICE    			|新增麦克风设备消息
|ITMG_MAIN_EVENT_TYPE_MIC_LOST_DEVICE    			|丢失麦克风设备消息
|ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER				|打开扬声器消息
|ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER				|关闭扬声器消息
|ITMG_MAIN_EVENT_TYPE_SPEAKER_NEW_DEVICE			|新增扬声器设备消息
|ITMG_MAIN_EVENT_TYPE_SPEAKER_LOST_DEVICE		|丢失扬声器设备消息
|ITMG_MAIN_EVENT_TYPE_ACCOMPANY_FINISH			|伴奏结束消息
|ITMG_MAIN_EVNET_TYPE_USER_UPDATE					|房间成员更新消息
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
| ITMG_MAIN_EVENT_TYPE_CHANGE_ROOM_TYPE    		|result; error_info; new_room_type	|{"error_info":"","new_room_type":0,"result":0}
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

