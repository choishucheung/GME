## Overview

Thank you for using Tencent Cloud Game Multimedia Engine SDK. This document provides a detailed description that makes it easy for Unity developers to debug and integrate the APIs for Game Multimedia Engine (GME).

## How to Use
![](https://main.qcloudimg.com/raw/810d0404638c494d9d5514eb5037cd37.png)


### Key considerations for using GME

| Important API | Description |
| ------------- |:-------------:|
|Init    		|Initializes GME 	|
|Poll    		|Triggers event callback	|
|EnterRoom	 	|Enters a room  		|
|EnableMic	 	|Enables the microphone 	|
|EnableSpeaker		|Enables the speaker 	|

**Note:**
**When a GME API is called successfully, QAVError.OK is returned, and the value is 0.**

**GME APIs are called in the same thread.**

**The request for entering a room via GME API should be authenticated. For more information, please see authentication section in relevant documentation.**

**This document is applicable to GME sdk version：2.1.1.39800.**
## Initialization-related APIs
For an uninitialized SDK, you must initialize it via initialization authentication to enter a room.

| API | Description |
| ------------- |:-------------:|
|Init    	|Initializes GME 	| 
|Poll    	|Triggers event callback	|
|Pause   	|Pauses the system	|
|Resume 	|Resumes the system	|
|Uninit    	|Initializes GME 	|


### Initialize the SDK
For more information on how to obtain parameters, please see [GME Integration Guide](/GME%20Introduction_intl.md).
This API should contain SdkAppId and openId. The SdkAppId is obtained from Tencent Cloud console, and the openId is used to uniquely identify a user. The setting rule for openId can be customized by App developers, and this ID must be unique in an App (only INT64 is supported).
SDK must be initialized before a user can enter a room.

#### Function prototype
```
IQAVContext Init(string sdkAppID, string openID)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| sdkAppId    	|String  |The SdkAppId obtained from Tencent Cloud console |
| openID    	|String  |The OpenID supports Int64 type (which is passed after being converted to a string) only. It is used to identify users and must be greater than 10000. 	|
#### Sample code  
```
int ret = IQAVContext.GetInstance().Init(str_appId, str_userId);
	if (ret != QAVError.OK) {
		return;
	}
```

### Trigger event callback
This API is used to trigger the event callback via periodic Poll call in update.
#### Function prototype

```
ITMGContext public abstract int Poll();
```
### Pause the system
This API is used to notify the engine for Pause at the same time the system Pause occurs.
#### Function prototype

```
ITMGContext public abstract int Pause()
```

### Resume the system
This API is used to notify the engine for Resume at the same time the system Resume occurs.
#### Function prototype

```
ITMGContext  public abstract int Resume()
```






### Deinitialize the SDK
Deinitializes an SDK to make it uninitialized.
#### Function prototype

```
ITMGContext public abstract int Uninit()
```





## Voice Chat Room-Related APIs
You must initialize and call the SDK to enter a room before Voice Chat can start.

| API | Description |
| ------------- |:-------------:|
|GenAuthBuffer    	|Initialization authentication |
|EnterRoom   		|Joins a room |
|EnterTeamRoom   	|Enters a team voice chat room |
|IsRoomEntered   	|Indicates whether any member has entered a room |
|ExitRoom 		|Exits the room |
|ChangeRoomType 	|Modifies the audio type of the user's room |
|GetRoomType 		|Obtains the audio type of the user's room |



### Authentication
AuthBuffer is generated for encryption and authentication of appropriate features. For more information on how to obtain relevant parameters, please see [GME Key](../GME%20Key%20Manual_intl.md).    
A value of type Byte[] is returned by this API.
#### Function prototype
```
QAVAuthBuffer GenAuthBuffer(int appId, int roomId, string openId, string key)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| appId    		|int   		|The SdkAppId obtained from Tencent Cloud console |
| roomId    		|int   		|Room number. 32-bit is supported.In offline voice,room number must be 0|
| openId    	|String 	|User ID											|
| key    		|string 	|The key obtained from Tencent Cloud console								|

#### Sample code  
```
byte[] GetAuthBuffer(string appId, string userId, int roomId, uint authBits)
    {
	return QAVAuthBuffer.GenAuthBuffer(int.Parse(appId), roomId, userId, "a495dca2482589e9");
}
```

### Join a room
This API is used to enter a room with the generated authentication information. Microphone and speaker are not enabled by default after a user enters the room.
For more information on how to integrate team voice chat, please see relevant [integration document](../GME%20TeamAudio%20Manual_intl.md).


#### Function prototype
```
ITMGContext EnterRoom(int roomId, int roomType, byte[] authBuffer)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| roomId		|int   				| Room number. 32-bit is supported. |
| roomType 	|ITMGRoomType		|Audio type of the room		|
| authBuffer 	|Byte[] 	| Authentication key					|

| Audio Type | Meaning | Parameter | Volume Type | Recommended Sampling Rate on the Console | Application Scenarios |
| ------------- |------------ | ---- |---- |---- |---- |
| ITMG_ROOM_TYPE_FLUENCY			|Fluent	|1|Speaker: chat volume; headset: media volume 	| 16k sampling rate is recommended if there is no special requirement for sound quality					| With fluent sound and ultra-low latency, it allows voice chat and is suitable for team speak scenario in such games as FPS and MOBA.	|							
| ITMG_ROOM_TYPE_STANDARD			|Standard	|2|Speaker: chat volume; headset: media volume	| Choose 16k or 48k sampling rate depending on different requirements for sound quality				| With good sound quality and medium latency, it is suitable for real time chat scenarios in casual games such as Werewolf, chess and card games.	|												
| ITMG_ROOM_TYPE_HIGHQUALITY		|High-quality	|3|Speaker: media volume; headset: media volume	| To ensure optimum effect, it is recommended to enable HQ configuration with 48k sampling rate	| With ultra-high sound quality and high latency, it is suitable for scenarios demanding high sound quality, such as music playback and online karaoke.	|

- If you have special requirement for audio type or scenarios, contact the customer service.
- The sound effect in a game depends directly on the sampling rate set on the console. Please confirm whether the sampling rate you set on the [console](https://console.cloud.tencent.com/gamegme) is suitable for the project's application scenario.
#### Sample code  
```
IQAVContext.GetInstance().EnterRoom(roomId, ITMG_ROOM_TYPE_FLUENCY, authBuffer);
```

### Callback for entering a room
The delegate function is used for callback after a user enters a room. The passed parameter includes result and error_info.
#### Function prototype
```
Delegate function:
public delegate void QAVEnterRoomComplete(int result, string error_info);
Event function:
public abstract event QAVEnterRoomComplete OnEnterRoomCompleteEvent;
```

#### Sample code
```
Listen for an event:
IQAVContext.GetInstance().OnEnterRoomCompleteEvent += new QAVEnterRoomComplete(OnEnterRoomComplete);

Process the event after listening:
void OnEnterRoomComplete(int err, string errInfo)
    {
	if (err != 0) {
	    ShowWarnning (string.Format ("join room failed, err:{0}, errInfo:{1}", err, errInfo));
	    return;
	}else{
	    ShowWarnning (string.Format ("The current audio room id:{0}. Please enter this room from another device for an audio chat.",roomId ));
    }
}
```

### Identify whether any member has entered a room
This API is called to identify whether any member has entered a room. A BOOL value is returned.
#### Function prototype  
```
ITMGContext abstract bool IsRoomEntered()
```
#### Sample code  
```
IQAVContext.GetInstance().IsRoomEntered();
```

### Exit a room
This API is called to exit the current room.
#### Function prototype  
```
ITMGContext ExitRoom()
```
#### Sample code  
```
IQAVContext.GetInstance().ExitRoom();
```

### Callback for exiting a room
Callback is executed after a user exits the room, and the delegate function is used to pass the message.
#### Function prototype  
```
Delegate function:
public delegate void QAVExitRoomComplete();
Event function:
public abstract event QAVExitRoomComplete OnExitRoomCompleteEvent; 
```
#### Sample code  
```
Listen for an event:
IQAVContext.GetInstance().OnExitRoomCompleteEvent += new QAVExitRoomComplete(OnExitRoomComplete);
Process the event after listening:
void OnExitRoomComplete(){
    //Send a callback after a user exits the room
}
```

### Obtain the audio type of the user's room
This API is used to obtain the audio type of the user's room. The returned value is the audio type of the room. Value 0 means that an error occurred while obtaining the audio type of the user's room. See the API EnterRoom for the audio type of the user's room.

#### Function prototype  
```
ITMGContext ITMGRoom public  int GetRoomType()
```

#### Sample code  
```
IQAVContext.GetInstance().GetRoom().GetRoomType();
```

### Modify the audio type of the user's room
This API is used to modify the audio type of the user's room.
#### Function prototype  
```
ITMGContext ITMGRoom public void ChangeRoomType(ITMGRoomType roomtype)
```

| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| roomtype    |ITMGRoomType    |The room type to be switched to. See the API EnterRoom for the audio type of the user's room. |

#### Sample code  
```
IQAVContext.GetInstance().GetRoom().ChangeRoomType(ITMG_ROOM_TYPE_FLUENCY);
```



### Callback for modifying the audio type of the room
This API is used to set the room type, and the delegate function is used to pass the modified information.

| Returned Parameter | Description |
| ------------- |:-------------:|
| result    |Value 0 represents the function is successfully called |
| error_info    |In case of failure, an error message will be passed |

```
Delegate function:
public delegate void QAVOnChangeRoomtypeCallback(int result, string error_info);

Event function:
public abstract event QAVCallback OnChangeRoomtypeCallback; 
```
#### Sample code  
```
Listen for an event:
IQAVContext.GetInstance().OnChangeRoomtypeCallback += new QAVOnChangeRoomtypeCallback(OnChangeRoomtypeCallback);
Process the event after listening:
void OnChangeRoomtypeCallback(){
    //Send a callback after the room type has been set
}
```

### Notification of room type change
This API is used to allow you or other users in a room to modify the room type. This callback function is called whenever the room type changes, which is used to notify the business layer of such change. The returned value is room type. See the API EnterRoom.
```
Delegate function:
public delegate void QAVOnRoomTypeChangedEvent(int roomtype);

Event function:
public abstract event QAVOnRoomTypeChangedEvent OnRoomTypeChangedEvent;	
```
#### Sample code  
```
Listen for an event:
IQAVContext.GetInstance().OnRoomTypeChangedEvent += new QAVOnRoomTypeChangedEvent(OnRoomTypeChangedEvent);
Process the event after listening:
void OnRoomTypeChangedEvent(){
    //Send a callback after the room type has been changed
}
```


	
### Member status change
Notification about this event is sent only when the status changes. To obtain member status in real time, cache the notification when receiving it at a higher layer. The event message ITMG_MAIN_EVNET_TYPE_USER_UPDATE is returned. The parameter "intent" includes event_id and user_list. Identify the event message in the OnEvent function.
Audio events are subject to a threshold above which a notification is sent.

|event_id     | Description | What is maintained at the App side |
| ------------- |:-------------:|-------------|
|ITMG_EVENT_ID_USER_ENTER    				|A member enters the room			| Member list		|
|ITMG_EVENT_ID_USER_EXIT    				|A member exits the room			| Member list		|
|ITMG_EVENT_ID_USER_HAS_AUDIO    		|A member sends audio packages		| Chat member list	|
|ITMG_EVENT_ID_USER_NO_AUDIO    			|A member stops sending audio packages		| Chat member list	|

#### Sample code  
```
Delegate function:
public delegate void QAVEndpointsUpdateInfo(int eventID, int count, [MarshalAs(UnmanagedType.LPArray, SizeParamIndex = 1)]string[] identifierList);
Event function:
public abstract event QAVEndpointsUpdateInfo OnEndpointsUpdateInfoEvent;

Listen for an event:
IQAVContext.GetInstance().OnEndpointsUpdateInfoEvent += new QAVEndpointsUpdateInfo(OnEndpointsUpdateInfo);
Process the event after listening:
void OnEndpointsUpdateInfo(int eventID, int count, string[] identifierList)
{
    //Processing
		//The developer parses the parameter to obtain vent_id and user_list.
		    switch (eventID)
 		    {
 		    case ITMG_EVENT_ID_USER_ENTER:
  			    //A member enters the room
  			    break;
 		    case ITMG_EVENT_ID_USER_EXIT:
  			    //A member exits the room
			    break;
		    case ITMG_EVENT_ID_USER_HAS_AUDIO:
			    //A member sends audio packages
			    break;
		    case ITMG_EVENT_ID_USER_NO_AUDIO:
			    //A member stops sending audio packages
			    break;
		  
		    default:
			    break;
 		    }
		break;
}

```

### Message details

| Message | Description of message |   
| ------------- |:-------------:|
|ITMG_MAIN_EVENT_TYPE_ENTER_ROOM    				       |Enters the audio/video room |
|ITMG_MAIN_EVENT_TYPE_EXIT_ROOM    				         	|Exits the audio/video room |
|ITMG_MAIN_EVENT_TYPE_ROOM_DISCONNECT    		       |Room disconnection due to network or other reasons |
|ITMG_MAIN_EVENT_TYPE_CHANGE_ROOM_TYPE				|Room type change event |

### Details of Data corresponding to the message
| Message | Data         | Example |
| ------------- |:-------------:|------------- |
| ITMG_MAIN_EVENT_TYPE_ENTER_ROOM    				|result; error_info					|{"error_info":"","result":0}|
| ITMG_MAIN_EVENT_TYPE_EXIT_ROOM    				|result; error_info  					|{"error_info":"","result":0}|
| ITMG_MAIN_EVENT_TYPE_ROOM_DISCONNECT    		|result; error_info  					|{"error_info":"waiting timeout, please check your network","result":0}|
| ITMG_MAIN_EVENT_TYPE_CHANGE_ROOM_TYPE    		|result; error_info; new_room_type	|{"error_info":"","new_room_type":0,"result":0}|





## Audio APIs for Voice Chat
The audio APIs for Voice Chat can only be called after the SDK is initialized and there are members in the room.


| API | Description |
| ------------- |:-------------:|
|PauseAudio    				       	     	|Pauses audio engine |
|ResumeAudio    				      	|Resumes audio engine |
|EnableMic    						|Enables/disables the microphone |
|GetMicState    						|Obtains the microphone status |
|EnableAudioCaptureDevice    			|Enables/disables the capturing device	|
|IsAudioCaptureDeviceEnabled    	|Get the status of the capturing device	|
|EnableAudioSend    				|Enables/disables sending audio data	|
|IsAudioSendEnabled    				|Get the status of audio uplink |
|GetMicLevel    						|Obtains real-time microphone volume |
|SetMicVolume    					|Sets microphone volume |
|GetMicVolume    					|Obtains microphone volume |
|EnableSpeaker    						|Enables/disables the speaker |
|GetSpeakerState    					|Obtains the speaker status |
|EnableAudioPlayDevice    			|Enables/disables the playing device	|
|IsAudioPlayDeviceEnabled    		|Get the status of the playing device|
|EnableAudioRecv    				|Enables/disables receiving audio data	|
|IsAudioRecvEnabled    				|Get the status of audio downlink|
|GetSpeakerLevel    				|Obtains real-time speaker volume |
|SetSpeakerVolume    				|Sets speaker volume |
|GetSpeakerVolume    				|Obtains speaker volume |
|EnableLoopBack    					|Enables/disables in-ear monitoring |


### Pause the capture and playback features of the audio engine
This API is called to pause the capture and playback features of the audio engine, and only works when members have entered the room.
You can get the microphone permission after calling the EnterRoom API successfully, and other programs cannot capture audio data from the microphone during your use of microphone. Calling EnableMic(false) does not release the microphone.
If you really need to release the microphone, call PauseAudio, which can cause the engine to be paused entirely. To resume audio capturing, call ResumeAudio.
#### Function prototype  
```
ITMGAudioCtrl abstract int PauseAudio()
```
#### Sample code  
```
IQAVContext.GetInstance ().GetAudioCtrl ().PauseAudio();
```

### Resume the capture and playback features of the audio engine
This API is called to resume the capture and playback features of the audio engine, and only works when members have entered the room.
#### Function prototype  
```
ITMGAudioCtrl abstract int ResumeAudio()
```
#### Sample code  
```
IQAVContext.GetInstance ().GetAudioCtrl ().ResumeAudio();
```

### Enable/disable the microphone
This API is used to enable/disable the microphone. Microphone and speaker are not enabled by default after a user enters a room.

#### Function prototype  
```
ITMGAudioCtrl EnableMic(bool isEnabled)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| isEnabled    |boolean     |To enable the microphone, set this parameter to true, otherwise, set it to false. |
#### Sample code  
```
Enable microphone
IQAVContext.GetInstance().GetAudioCtrl().EnableMic(true);
```

### Obtain the microphone status
This API is used to obtain the microphone status. If "0" is returned, the microphone is off. If "1" is returned, the microphone is on. If "2" is returned, the microphone is being worked on. If "3" is returned, no microphone exists. If "4" is returned, the microphone is not initialized well.
#### Function prototype  
```
ITMGAudioCtrl GetMicState()
```
#### Sample code  
```
micToggle.isOn = IQAVContext.GetInstance().GetAudioCtrl().GetMicState();
```
### Enable/disable capturing device
This API is used to Enable/disable capturing device。The Deivce is not enabled by default after a user enters a room.。
- This API only be called after entering the room, and device is default disabled after exiting room.
- On the mobile(ios/android), opening the capturing device is usually accompanied by permission requests, volume type adjustments, etc.。

> Function prototype 

```
ITMGAudioCtrl int EnableAudioPlayDevice(bool isEnabled)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| isEnabled    |bool     |To enable the capturing device, set this parameter to true, otherwise, set it to false. |

> Sample code

```
IQAVContext.GetInstance().GetAudioCtrl().EnableAudioCaptureDevice(true);
```

### Obtain the capture device status
This API is used to obtain the capturing device status。
> Function prototype

```
ITMGAudioCtrl bool IsAudioCaptureDeviceEnabled()
```
> Sample code

```
bool IsAudioCaptureDevice = IQAVContext.GetInstance().GetAudioCtrl().IsAudioCaptureDeviceEnabled();
```

### Enable/disable audio upstream
This API is used to enable/disable audio upstream. If the capturing device is already turned on, the captured audio data is sent. If the capturing device is not turned on, it is still silent.
> Function prototype

```
ITMGAudioCtrl int EnableAudioSend(bool isEnabled)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| isEnabled    |bool     |To enable the audio upstream, set this parameter to true, otherwise, set it to false.|

> Sample code  

```
IQAVContext.GetInstance().GetAudioCtrl().EnableAudioSend(true);
```

### Obtain the capture audio upstream

> Function prototype
```
ITMGAudioCtrl bool IsAudioSendEnabled()
```
> Sample code  
```
bool IsAudioSend = IQAVContext.GetInstance().GetAudioCtrl().IsAudioSendEnabled();
```

### Obtain real-time microphone volume
This API is used to obtain real time microphone volume. An int value is returned.
#### Function prototype  
```
ITMGAudioCtrl -(int)GetMicLevel
```
#### Sample code  
```
IQAVContext.GetInstance().GetAudioCtrl().GetMicLevel();
```

### Set software volume for the microphone
This API is used to set software volume for the microphone. The corresponding parameter is "volume". The value "0" sets the volume to Mute, and "100" means the volume remains unchanged. Default is 100.
#### Function prototype  
```
ITMGAudioCtrl SetMicVolume(int volume)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| volume    |int      | Sets the volume, value range: 0 to 200 |
#### Sample code  
```
int micVol = (int)(value * 100);
IQAVContext.GetInstance().GetAudioCtrl().SetMicVolume (micVol);
```

### Obtain software volume for the microphone
This API is used to obtain the software volume for the microphone. An int value is returned.
#### Function prototype  
```
ITMGAudioCtrl GetMicVolume()
```
#### Sample code  
```
IQAVContext.GetInstance().GetAudioCtrl().GetMicVolume();
```

### Enable/disable the speaker
This API is used to enable/disable the speaker.
#### Function prototype  
```
ITMGAudioCtrl EnableSpeaker(bool isEnabled)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| isEnabled    |bool       	| To disable the speaker, set this parameter to false, otherwise, set it to true.	|
#### Sample code  
```
Enable the speaker.
IQAVContext.GetInstance().GetAudioCtrl().EnableSpeaker(true);
```


### Obtain the speaker status
This API is used to obtain the speaker status. If "0" is returned, the speaker is off. If "1" is returned, the speaker is on. If "2" is returned, the speaker is being worked on. If "3" is returned, no speaker exists. If "4" is returned, the speaker is not initialized well.
#### Function prototype  
```
ITMGAudioCtrl GetSpeakerState()
```

#### Sample code  
```
speakerToggle.isOn = IQAVContext.GetInstance().GetAudioCtrl().GetSpeakerState();
```
### Enable/disable the playing device

> Function prototype
```
ITMGAudioCtrl EnableAudioPlayDevice(bool isEnabled)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| isEnabled   		|bool       	| To enable the playing device, set this parameter to true, otherwise, set it to false.	|

```
IQAVContext.GetInstance().GetAudioCtrl().EnableAudioPlayDevice(true);
```

### Obtain the playing device status

> Function prototype

```
ITMGAudioCtrl bool IsAudioPlayDeviceEnabled()
```
> Sample code

```
bool IsAudioPlayDevice = IQAVContext.GetInstance().GetAudioCtrl().IsAudioPlayDeviceEnabled();
```

###  Enable/disable audio downstream
This API is used to enable/disable the audio downstream. If the playing device is already turned on, the audio data of other people in the room will be played. If the playing device is not turned on, it is still silent.

> Function prototype

```
ITMGAudioCtrl int EnableAudioRecv(bool isEnabled)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| isEnabled   		|bool       	| To enable audio downstream, set this parameter to true, otherwise, set it to false.	|

> Sample code

```
IQAVContext.GetInstance().GetAudioCtrl().EnableAudioRecv(true);
```

### Obtain audio downstream status
> Function prototype 
```
ITMGAudioCtrl bool IsAudioRecvEnabled()
```

> Sample code  
```
bool IsAudioRecv = IQAVContext.GetInstance().GetAudioCtrl().IsAudioRecvEnabled();
```

### Obtain real-time speaker volume
This API is used to obtain real time speaker volume. An int value is returned to indicate the real-time speaker volume.
#### Function prototype  
```
ITMGAudioCtrl GetSpeakerLevel()
```

#### Sample code  
```
IQAVContext.GetInstance().GetAudioCtrl().GetSpeakerLevel();
```

### Set software volume for the speaker
This API is used to set the software volume for the speaker.
The corresponding parameter is "volume". The value "0" sets the volume to Mute, and "100" means the volume remains unchanged. Default is 100.

#### Function prototype  
```
ITMGAudioCtrl SetSpeakerVolume(int volume)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| vol    |int        | Sets the volume, value range: 0 to 150 |
#### Sample code  
```
int speVol = (int)(value * 100);
IQAVContext.GetInstance().GetAudioCtrl().SetSpeakerVolume(speVol);
```

### Obtain software volume for the speaker
This API is used to obtain the software volume for the speaker. An int value is returned to indicate the software volume for the speaker.
"Level" indicates the real-time volume, and "Volume" the software volume for the speaker. The ultimate volume equals to Level*Volume%. For example, if the value for "Level" is 100 and the one for "Volume" is 60, the ultimate volume will be "60".

#### Function prototype  
```
ITMGAudioCtrl GetSpeakerVolume()
```
#### Sample code  
```
IQAVContext.GetInstance().GetAudioCtrl().GetSpeakerVolume();
```


### Enable in-ear monitoring
This API is used to enable in-ear monitoring.
#### Function prototype  

```
ITMGContext GetAudioCtrl EnableLoopBack(bool enable)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| enable    |bool         | Specifies whether to enable in-ear monitoring |
#### Sample code  

```
IQAVContext.GetInstance().GetAudioCtrl().EnableLoopBack(true);
```

### Message details

| Message | Description of message |   
| ------------- |:-------------:|
|ITMG_MAIN_EVENT_TYPE_ENABLE_MIC    				       |Enables the microphone |
|ITMG_MAIN_EVENT_TYPE_DISABLE_MIC    				       |Disables the microphone |
|ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER				       |Enables the speaker |
|ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER				       |Disables the speaker |

### Details of Data corresponding to the message
| Message | Data         | Example |
| ------------- |:-------------:|------------- |
| ITMG_MAIN_EVENT_TYPE_ENABLE_MIC    				|result; error_info  					|{"error_info":"","result":0}|
| ITMG_MAIN_EVENT_TYPE_DISABLE_MIC    				|result; error_info  					|{"error_info":"","result":0}|
| ITMG_MAIN_EVENT_TYPE_ENABLE_SPEAKER    			|result; error_info  					|{"error_info":"","result":0}|
| ITMG_MAIN_EVENT_TYPE_DISABLE_SPEAKER    			|result; error_info  					|{"error_info":"","result":0}|

## APIs related to the accompaniment in voice chat
| API | Description |
| ------------- |:-------------:|
|StartAccompany    				       |Starts playing back the accompaniment |
|StopAccompany    				   	|Stops playing back the accompaniment |
|IsAccompanyPlayEnd				|Indicates whether the accompaniment is over |
|PauseAccompany    					|Pauses playing back the accompaniment |
|ResumeAccompany					|Replays the accompaniment |
|SetAccompanyVolume 				|Sets the accompaniment volume |
|GetAccompanyVolume				|Obtains the volume of the accompaniment |
|SetAccompanyFileCurrentPlayedTimeByMs 				|Sets the playback progress |

### Start playing back the accompaniment
This API is called to play back the accompaniment. Supported formats include M4A, AAC, WAV, and MP3. Calling this API resets the volume.
#### Function prototype  
```
IQAVAudioEffectCtrl int StartAccompany(string filePath, bool loopBack, int loopCount, int duckerTimeMs)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| filePath    |string            | Accompaniment's playback path |
| loopBack    |bool          | Indicates whether to send a mix. This is generally set to true, indicating that other users can also hear the accompaniment. |
| loopCount    |int          | Number of loops. Value -1 means an infinite loop. |
#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().StartAccompany(filePath,true,loopCount,duckerTimeMs);
```

### Callback for accompaniment playback
Callback is executed after an accompaniment has been played, and the delegate function is used to pass the message.
#### Function prototype  
```
Delegate function:
public delegate void QAVAccompanyFileCompleteHandler(int code, string filepath);
Event function:
public abstract event QAVAccompanyFileCompleteHandler OnAccompanyFileCompleteHandler;
```
#### Sample code
```
Listen for an event:
IQAVContext.GetInstance().GetAudioEffectCtrl().OnAccompanyFileCompleteHandler += new QAVAccompanyFileCompleteHandler(OnAccomponyFileCompleteHandler);
Process the event after listening:
void OnAccomponyFileCompleteHandler(int code, string filepath){
    //Send a callback after an accompaniment has been played
}
```

### Stop playing back the accompaniment
This API is used to stop playing back the accompaniment.
#### Function prototype  
```
IQAVAudioEffectCtrl int StopAccompany(int duckerTimeMs)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| duckerTimeMs    |int             | Fading time |

#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().StopAccompany(duckerTimeMs);
```

### Indicate whether the accompaniment is over
If it is over, "true" is returned. If it is not, "false" is returned.
#### Function prototype  
```
IQAAudioEffectCtrl bool IsAccompanyPlayEnd();
```
#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().IsAccompanyPlayEnd();
```


### Pause playing back the accompaniment
This API is used to pause playing back the accompaniment.
#### Function prototype  
```
IQAAudioEffectCtrl int PauseAccompany()
```
#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().PauseAccompany();
```



### Replay the accompaniment
This API is used to replay the accompaniment.
#### Function prototype  
```
IQAAudioEffectCtrl int ResumeAccompany()
```
#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().ResumeAccompany();
```

### Specify whether you can hear the accompaniment
This API is used to specify whether you can hear the accompaniment.
#### Function prototype  
```
IQAAudioEffectCtrl int EnableAccompanyPlay(bool enable)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| enable    |bool | Indicates whether you can hear the accompaniment |
#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().EnableAccompanyPlay(true);
```

### Specify whether others can also hear the accompaniment
This API is used to specify whether others can also hear the accompaniment.
#### Function prototype  
```
IQAAudioEffectCtrl int EnableAccompanyLoopBack(bool enable)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| enable    |bool | Indicates whether you can hear the accompaniment |

#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().EnableAccompanyLoopBack(true);
```


### Set the accompaniment volume
This API is used to set the DB volume. Value range: 0-200. Default is 100. A value greater than 100 means volume up, otherwise volume down.
#### Function prototype  
```
IQAAudioEffectCtrl int SetAccompanyVolume(int vol)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| vol    |int | Indicates the volume value |

#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().SetAccompanyVolume(vol);
```

### Obtain the volume of the accompaniment
This API is used to get the DB volume.
#### Function prototype  
```
IQAAudioEffectCtrl abstract int GetAccompanyVolume()
```
#### Sample code  
```
string currentVol = "VOL: " + IQAVContext.GetInstance().GetAudioEffectCtrl().GetAccompanyVolume();
```

### Obtain the accompaniment playback progress
The following two APIs are used to obtain the accompaniment playback progress. Note: Current/Total = current loop times, Current % Total = current loop playback position.
#### Function prototype  
```
IQAAudioEffectCtrl abstract uint GetAccompanyFileTotalTimeByMs()
IQAAudioEffectCtrl abstract int GetAccompanyFileCurrentPlayedTimeByMs()
```
#### Sample code  
```
Sstring current = "Current: " + IQAVContext.GetInstance().GetAudioEffectCtrl().GetAccompanyFileCurrentPlayedTimeByMs() + " ms";
string total = "Total: " + IQAVContext.GetInstance().GetAudioEffectCtrl().GetAccompanyFileTotalTimeByMs() + " ms";
```


### Set the playback progress
This API is used to set the playback progress.
#### Function prototype  
```
IQAAudioEffectCtrl abstract uint SetAccompanyFileCurrentPlayedTimeByMs(uint time)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| time    |uint | Indicates the playback progress in milliseconds |

#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().SetAccompanyFileCurrentPlayedTimeByMs(time);
```


## APIs related to sound effect in voice chat
| API | Description |
| ------------- |:-------------:|
|PlayEffect    		|Plays the sound effect |
|PauseEffect    	|Pauses the sound effect |
|PauseAllEffects	|Pauses all sound effects |
|ResumeEffect    	|Replays the sound effect |
|ResumeAllEffects	|Replays all sound effects |
|StopEffect 		|Stops the sound effect |
|StopAllEffects		|Stops all sound effects |
|SetVoiceType 		|Voice changing effects |
|GetEffectsVolume	|Obtains the volume of sound effects |
|SetEffectsVolume 	|Sets the volume of sound effects |

### Play the sound effect
This API is used to play sound effects. The sound effect ID in the parameter needs to be managed by the App side, uniquely identifying a separate file.
#### Function prototype  
```
IQAAudioEffectCtrl int PlayEffect(int soundId, string filePath, bool loop = false, double pitch = 1.0f, double pan = 0.0f, double gain = 1.0f)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| soundId    	|int      	|Indicates the sound effect ID |
| filePath    	|string 	|Indicates the sound effect path |
| loop    		|bool   	|Indicates whether to repeat playback |
| pitch    	|double	|Reserved field |
| pan    		|double	|Reserved field |
| gain    		|double	|Reserved field |
#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().PlayEffect(soundId,filePath,true,1.0,0,1.0);
```

### Pause the sound effect
This API is used to pause playing back the sound effect.
#### Function prototype  
```
IQAAudioEffectCtrl int PauseEffect(int soundId)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| soundId    |int | Indicates the sound effect ID |

#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().PauseEffect(soundId);
```

### Pause all sound effects
This API is used to pause all sound effects.
#### Function prototype  
```
IQAAudioEffectCtrl int PauseAllEffects()
```
#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().PauseAllEffects();
```

### Replay the sound effect
This API is used to replay the sound effect.
#### Function prototype  
```
IQAAudioEffectCtrl int ResumeEffect(int soundId)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| soundId    |int | Indicates the sound effect ID |
#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().ResumeEffect(soundId);
```



### Replay all sound effects
This API is used to replay all sound effects.
#### Function prototype  
```
IQAAudioEffectCtrl int ResumeAllEffects()
```
#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().ResumeAllEffects();
```

### Stop the sound effect
This API is used to stop the sound effect.
#### Function prototype  
```
IQAAudioEffectCtrl int StopEffect(int soundId)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| soundId    |int | Indicates the sound effect ID |
#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().StopEffect(soundId);
```

### Stop all sound effects
This API is used to stop all sound effects.
#### Function prototype  
```
IQAAudioEffectCtrl int StopAllEffects()
```
#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().StopAllEffects(); 
```

### Voice changing effects
This API is used to set the voice changing effects.
#### Function prototype  
```
IQAAudioEffectCtrl int setVoiceType(int type)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| type    |int | Indicates the type of local voice changing effect |


| Type | Parameter | Description |
| ------------- |-------------|------------- |
| ITMG_VOICE_TYPE_ORIGINAL_SOUND  		|0	|Original sound 			|
| ITMG_VOICE_TYPE_LOLITA    				|1	|Lolita			|
| ITMG_VOICE_TYPE_UNCLE  				|2	|Uncle			|
| ITMG_VOICE_TYPE_INTANGIBLE    			|3	|Intangible			|
| ITMG_VOICE_TYPE_DEAD_FATBOY  			|4	|Dead fatboy			|
| ITMG_VOICE_TYPE_HEAVY_MENTA			|5	|Heavy menta		|
| ITMG_VOICE_TYPE_DIALECT 				|6	|Dialect		|
| ITMG_VOICE_TYPE_INFLUENZA 				|7	|Influenza			|
| ITMG_VOICE_TYPE_CAGED_ANIMAL 			|8	|Caged animal			|
| ITMG_VOICE_TYPE_HEAVY_MACHINE		|9	|Heavy machine			|
| ITMG_VOICE_TYPE_STRONG_CURRENT		|10	|Strong current			|
| ITMG_VOICE_TYPE_KINDER_GARTEN			|11	|Kinder garten			|
| ITMG_VOICE_TYPE_HUANG 					|12	|Huang		|

#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().setVoiceType(0); 
```

### Obtain the volume of sound effects
This API is used to obtain the volume (linear volume) of sound effects. A value greater than 100 means volume up, otherwise volume down.
#### Function prototype  
```
IQAAudioEffectCtrl  int GetEffectsVolume()
```
#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().GetEffectsVolume();
```


### Set the volume of sound effects
This API is used to set the volume of sound effects.
#### Function prototype  
```
IQAAudioEffectCtrl  int SetEffectsVolume(int volume)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| volume    |int | Indicates the volume value |

#### Sample code  
```
IQAVContext.GetInstance().GetAudioEffectCtrl().SetEffectsVolume(volume);
```







## Offline Voice
| API | Description |
| ------------- |:-------------:|
|ApplyPTTAuthbuffer    		|Indicates the offline voice authentication |
|SetMaxMessageLength    |Specifies the maximum length of a voice message |
|StartRecording		|Starts recording |
|StopRecording    	|Stops recording |
|CancelRecording	|Cancels recording |
|UploadRecordedFile 	|Uploads voice files |
|DownloadRecordedFile	|Downloads voice files |
|PlayRecordedFile 	|Plays voice files |
|StopPlayFile		|Stops playing voice files |
|GetFileSize 		|Indicates the size of a voice file |
|GetVoiceFileDuration	|Indicates the length of a voice file |
|SpeechToText 		|Converts the voice file into text with Speech Recognition |

### Initialization for integrating the offline voice technology
Passing the authentication access token to the ApplyPTTAuthbuffer function is required for initialization. For more information on how to obtain authentication, please see [GME Key](../GME%20Key%20Manual_intl.md) document.  

```
ITMGPTT int ApplyPTTAuthbuffer (byte[] authBuffer)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| authBuffer    |char* | Authentication key	 |

#### Sample code  
```
IQAVContext.GetInstance().GetPttCtrl().ApplyPTTAuthbuffer(authBuffer);
```


### Specify the maximum length of a voice message
This API is used to specify the maximum length of a voice message, which is limited to 60 seconds.
#### Function prototype  
```
IQAVPTT int SetMaxMessageLength(int msTime)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| msTime    |int | Indicates the length of a voice message |
#### Sample code  
```
IQAVContext.GetInstance().GetPttCtrl().SetMaxMessageLength(60000); 
```


### Start recording
This API is used to start recording.
#### Function prototype  
```
IQAVPTT int StartRecording(string fileDir)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| fileDir    |string                      | Path for storing the voice file |
#### Sample code  
```
string recordPath = Application.persistentDataPath + string.Format ("/{0}.silk", sUid++);
int ret = IQAVContext.GetInstance().GetPttCtrl().StartRecording(recordPath);
```

### Callback for starting recordings
Callback is executed after recording, and the delegate function is used to pass the message.
#### Function prototype  
```
Delegate function:
public delegate void QAVRecordFileCompleteCallback(int code, string filepath); 
Event function:
public abstract event QAVRecordFileCompleteCallback OnRecordFileComplete;
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| code    |string                      | When code is 0, recording is completed |
| filepath    |string                      | Path for storing the recorded file |

#### Sample code  
```
Listen for an event:
IQAVContext.GetInstance().GetPttCtrl().OnRecordFileComplete += mInnerHandler;
Process the event after listening:
void mInnerHandler(int code, string filepath){
    //Send a callback after the recording has started
}
```

### Stop recording
This API is used to stop recording.
#### Function prototype  
```
IQAVPTT int StopRecording()
```
#### Sample code  
```
IQAVContext.GetInstance().GetPttCtrl().StopRecording();
```

### Cancel recording
This API is used to cancel recording.
#### Function prototype  

```
IQAVPTT int CancelRecording()
```
#### Sample code  

```
IQAVContext.GetInstance().GetPttCtrl().CancelRecording();
```

### Upload voice files
This API is used to upload voice files.
#### Function prototype  
```
IQAVPTT int UploadRecordedFile (string filePath)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| filePath    |string                      | Path for uploading the voice file |
#### Sample code  
```
IQAVContext.GetInstance().GetPttCtrl().UploadRecordedFile(filePath); 
```


### Callback for uploading voice files
Callback is executed after a voice file has been uploaded, and the delegate function is used to pass the message.
#### Function prototype  
```
Delegate function:
public delegate void QAVUploadFileCompleteCallback(int code, string filepath, string fileid);
Event function:
public abstract event QAVUploadFileCompleteCallback OnUploadFileComplete; 
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| code    |int                       | When code is 0, recording is completed |
| filepath    |string                      | Path for storing the recorded file |
| fileid    |string                      | URL of the file |
#### Sample code  
```
Listen for an event:
IQAVContext.GetInstance().GetPttCtrl().OnUploadFileComplete += mInnerHandler;
Process the event after listening:
void mInnerHandler(int code, string filepath, string fileid){
    //Send a callback after a voice file has been uploaded
}
```


### Download voice files
This API is used to download voice files.
#### Function prototype  
```
IQAVPTT DownloadRecordedFile (string fileID, string downloadFilePath)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| fileID    |string                       | URL of a file |
| downloadFilePath    |string                       | Local path for saving the file |
#### Sample code  
```
IQAVContext.GetInstance().GetPttCtrl().DownloadRecordedFile(fileId, filePath);
```


### Callback for downloading voice files
Callback is executed after a voice file has been downloaded, and the delegate function is used to pass the message.
#### Function prototype  
```
Delegate function:
public delegate void QAVDownloadFileCompleteCallback(int code, string filepath, string fileid);
Event function:
public abstract event QAVDownloadFileCompleteCallback OnDownloadFileComplete;
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| code    |int                       | When code is 0, recording is completed |
| filepath    |string                      | Path for storing the recorded file |
| fileid    |string                      | URL of the file |
#### Sample code  
```
Listen for an event:
IQAVContext.GetInstance().GetPttCtrl().OnDownloadFileComplete += mInnerHandler;
Process the event after listening:
void mInnerHandler(int code, string filepath, string fileid){
    //Send a callback after a voice file has been downloaded
}
```



### Play voice files
This API is used to play voice files.
#### Function prototype  
```
IQAVPTT PlayRecordedFile (string downloadFilePath)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| downloadFilePath    |string                       | Path of the file |
#### Sample code  
```
IQAVContext.GetInstance().GetPttCtrl().PlayRecordedFile(filePath); 
```


### Callback for playing voice files
Callback is executed after a voice file has been played, and the delegate function is used to pass the message.
#### Function prototype  
```
Delegate function:
public delegate void QAVPlayFileCompleteCallback(int code, string filepath);
Event function:
public abstract event QAVPlayFileCompleteCallback OnPlayFileComplete;
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| code    |int                       | When code is 0, recording is completed |
| filepath    |string                      | Path for storing the recorded file |

#### Sample code  
```
Listen for an event:
IQAVContext.GetInstance().GetPttCtrl().OnPlayFileComplete += mInnerHandler;
Process the event after listening:
void mInnerHandler(int code, string filepath){
    //Callback for playing a voice file
}
```




### Stop playing voice files
This API is used to stop playing back voice files.
#### Function prototype  
```
IQAVPTT int StopPlayFile()
```

#### Sample code  
```
IQAVContext.GetInstance().GetPttCtrl().StopPlayFile();
```



### Obtain the size of a voice file
This API is used to get the size of a voice file.
#### Function prototype  
```
IQAVPTT GetFileSize(string filePath) 
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| filePath    |string                      | Path of the voice file |
#### Sample code  
```
int fileSize = IQAVContext.GetInstance().GetPttCtrl().GetFileSize(filepath);
```

### Obtain the length of a voice file
This API is used to get the length of voice file.
#### Function prototype  
```
IQAVPTT int GetVoiceFileDuration(string filePath)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| filePath    |string                      | Path of the voice file |
#### Sample code  
```
int fileDuration = IQAVContext.GetInstance().GetPttCtrl().GetVoiceFileDuration(filepath);
```


### Convert the specified voice file into text with Speech Recognition
This API is used to convert the specified voice file into text with Speech Recognition.
#### Function prototype  
```
IQAVPTT int SpeechToText(String fileID)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| fileID    |string                      | URL of the voice file |

#### Sample code  
```
IQAVContext.GetInstance().GetPttCtrl().SpeechToText(fileID);
```

### Callback for Speech Recognition
This API is used to convert the specified voice file into text with Speech Recognition, and the delegate function is used to pass the message.
#### Function prototype  
```
Delegate function:
public delegate void QAVSpeechToTextCallback(int code, string fileid, string result);
Event function:
public abstract event QAVSpeechToTextCallback OnSpeechToTextComplete;
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| code    |int                       | When code is 0, recording is completed |
| filepath    |string                      | Path for storing the recorded file |
| result    |string                      | Result of text conversion |
#### Sample code  
```
Listen for an event:
IQAVContext.GetInstance().GetPttCtrl().OnSpeechToTextComplete += mInnerHandler;
Process the event after listening:
void mInnerHandler(int code, string fileid, string result){
    //Callback for playing a voice file
}
```
## Advanced APIs
### Obtain the version number
This API is used to get the SDK version number for analysis.
#### Function prototype
```
ITMGContext  abstract string GetVersion()
```
#### Sample code  
```
IQAVContext.GetInstance().GetVersion();
```





### Set the print log level
This API is used to set the print log level.
#### Function prototype
```
ITMGContext  SetLogLevel(int logLevel, bool enableWrite, bool enablePrint)
```


| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| logLevel    		|int | Level of logs to be printed |
| enableWrite    	| bool | Indicates whether to write data to a file. Default is Yes |
| enablePrint    	|bool | Indicates whether to write data to the console. Default is Yes |



|ITMG_LOG_LEVEL|Description |
| -------------------------------|:-------------:|
|TMG_LOG_LEVEL_NONE=0		|Do not print logs |
|TMG_LOG_LEVEL_ERROR=1		|Prints error logs (default) |
|TMG_LOG_LEVEL_INFO=2			|Prints prompt logs |
|TMG_LOG_LEVEL_DEBUG=3		|Prints development and debugging logs |
|TMG_LOG_LEVEL_VERBOSE=4		| Prints high-frequency logs |
#### Sample code  
```
IQAVContext.GetInstance().SetLogLevel(TMG_LOG_LEVEL_NONE,true,true);
```

### Set the print log path
This API is used to set the print log path.
The default path is:

| Platform | Path |
| ------------- |-------------|
|Windows 	|%appdata%\Tencent\GME\ProcessName|
|iOS    		|Application/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Documents|
|Android	|/sdcard/Android/data/xxx.xxx.xxx/files|
|Mac    		|/Users/username/Library/Containers/xxx.xxx.xxx/Data/Documents|

#### Function prototype
```
ITMGContext  SetLogPath(string logDir)
```

| Parameter | Type | Description |
| ------------- |:-------------:|-------------
| logDir    		|NSString | Path |

#### Sample code  
```
IQAVContext.GetInstance().SetLogPath(path);
```
### Obtain diagnostic messages
This API is used to obtain information about the quality of real-time audio/video calls. This API is mainly used to check the quality of real-time calls and troubleshoot problems, and can be ignored at the business side.
#### Function prototype  
```
IQAVRoom GetQualityTips()
```
#### Sample code  
```
string tips = IQAVContext.GetInstance().GetRoom().GetQualityTips();
```

### Add an ID to the audio data blacklist
This API is used to add an ID to the audio data blacklist. A return value of 0 indicates that the call failed.
#### Function prototype  

```
ITMGContext ITMGAudioCtrl AddAudioBlackList(string identifier)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| identifier    |NSString      | ID that needs to be added to the blacklist |
#### Sample code  

```
IQAVContext.GetInstance().GetAudioCtrl ().AddAudioBlackList (id);
```

### Remove an ID from the audio data blacklist
This API is used to remove an ID from the audio data blacklist. A return value of 0 indicates that the call failed.
#### Function prototype  

```
ITMGContext ITMGAudioCtrl RemoveAudioBlackList(string identifier)
```
| Parameter | Type | Description |
| ------------- |:-------------:|-------------|
| identifier    | NSString   | ID that needs to be removed from the blacklist |
#### Sample code  

```
IQAVContext.GetInstance().GetAudioCtrl ().RemoveAudioBlackList (id);
```


