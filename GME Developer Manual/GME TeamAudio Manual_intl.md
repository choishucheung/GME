## Introduction
Welcome to the Tencent Cloud Game Multimedia Engine (GME) SDK. This document describes access to technologies for development so that developers can easily debug and access to Real-time team APIs for Tencent Cloud GME.

### 1.Enter a Room
The AuthBuffer parameter is required to allow a user to enter a room.

#### Real Time Room
> Function Prototype
```
ITMGContext EnterRoom(int relationId, ITMG_ROOM_TYPE roomType, byte[] authBuffer)
```
|Parameter     | Type         |Description|
| ------------- |:-------------:|-------------
| relationId		|int    		|Room number,32 bits supported only.|
| roomType 			|ITMG_ROOM_TYPE	|Audio room type.|
| authBuffer    	|Byte[]  		|Authentication code.|

|ITMG_ROOM_TYPE     	|Description|Parameter|Console recommended sample rate setting|Applicable scene|Applicable scene|
| ------------- |------------ | ---- |---- |---- |---- |
| ITMG_ROOM_TYPE_FLUENCY			|Low sound quality		|1|Speaker: call volume; headset: media volume	|If there is no special demand for sound quality, the 16K sampling rate can be used;									|Smooth priority, ultra-low latency real-time voice, applied in the game to open team play scenes, suitable for FPS, MOBA and other types of games;|
| ITMG_ROOM_TYPE_STANDARD			|Standard sound quality	|2|Speaker: call volume; headset: media volume	|According to the demand for sound quality, you can choose the sampling rate of 16k/48k							|The sound quality is good, the lag is moderate, suitable for real-time call scenes of casual games such as  table games, chess and so on;|
| ITMG_ROOM_TYPE_HIGHQUALITY		|High sound quality		|3|Speaker: media volumeheadset: media volume	|For best performance, it is recommended that the console set a high-quality configuration with a 48k sample rate.	|High sound quality, relatively laggy, suitable for music and dance games and voice social apps; suitable for playing music, online karaoke and other scenes with high sound quality requirements;|

- If you have special needs for volume type or scene, please contact the frontline customer service;
- The console sample rate setting directly affects the game's voice performance. Please check again on the [console](https://console.cloud.tencent.com/gamegme) that the sample rate setting is consistent with the project usage scenario.



#### Real-Time Team Room
Real-time team chatting introduction:
Team voice :Players team up before the game starts, team voice can only be heard by teammates.
Global voice:The player can set the global voice mode before the game starts and during the game. After setting, a certain range of people near the player can hear the player's voice.
The voice mode can be switched at any time during the game.

#### Assume that the A player state is global voice, corresponding to the mode of the B player's voice type in different situations:

|Whether in the same team	|Whether within range	|Voice Type	|Can A hear the sound of B？|Can B hear the sound of A？|
| -----------------	| ------------ | ------------ |--------------------------	|--------------------------	|
|Y		|Y		 	|Global voice	|Y	|Y	|
|Y		|Y		 	|Team voice		|Y	|Y	|
|Y		|N		 	|Global voice	|Y	|Y	|
|Y		|N		 	|Team voice		|Y	|Y	|
|N		|Y		 	|Global voice	|Y	|Y	|
|N		|Y			|Team voice		|N	|N	|
|N		|N		 	|Global voice	|N	|N	|
|N		|N			|Team voice		|N	|N	|

#### Suppose the A player status is the team voice, corresponding to the mode of the B player's voice in different situations:

|Whether in the same team	|Whether within range	|Voice Type	|Can A hear the sound of B？|Can B hear the sound of A？|
| -----------------	| ------------ | ------------ |--------------------------	|--------------------------	|
|Y		|Y		 	|Global voice	|Y		|Y		|
|Y		|Y		 	|Team voice		|Y		|Y		|
|Y		|N		 	|Global voice	|Y		|Y		|
|Y		|N		 	|Team voice		|Y		|Y		|
|N		|Y		 	|Global voice	|N		|N		|
|N		|Y			|Team voice		|N		|N		|
|N		|N		 	|Global voice	|N		|N		|
|N		|N			|Team voice		|N		|N		|

Supplement to the range of speech distance:
- Whether it is within the range of voice distance does not affect the team members to talk to each other.
- Set the range of received speech distance API：UpdateCoordinate.

> Function Prototype
```
ITMGContext  EnterTeamRoom(int relationId,ITMG_ROOM_TYPE roomType, byte[] authBuffer,int teamId, int audioMode)
```
|Parameter     | Type         |Description|
| ------------- |:-------------:|-------------
| relationId		|int    		|Room number, 32 bits supported only.									|
| roomType 			|ITMG_ROOM_TYPE	|Audio room type.							|
| authBuffer    	|Byte[] 		|Authentication code.								|
| teamId    		|int    		|Joined team voice team identification code (cannot be 0)|
| audioMode    		|int    		|0 for global voice and 1 for team voice|






### 2.Modify team voice chatting mode
Use this function to modify the team voice chatting mode.
> Function Prototype
```
ITMGRoom int ChangeTeamAudioMode(int audioMode)
```
|Parameter     | Type         |Description|
| ------------- |:-------------:|-------------
| audioMode    |int     |0 for global voice and 1 for team voice|

> Sample Code  
```
ITMGContext.GetInstance().GetRoom().ChangeTeamAudioMode(1);
```

### 3.Set the range of received voice distance
This function is used to set the range of speech received (the distance is based on the game engine).
- Calling this function requires a call per frame.
- Called on the premise of entering the room.
- Every user in the game needs to be called.

> Function Prototype  
```
ITMGRoom int UpdateCoordinate(int pos_x, int pos_y, int pos_z, int range)
```
|Parameter     | Type         |Description|
| ------------- |-------------|-------------
| pos_x    |int         |The x coordinate of the user					|
| pos_y    |int         |The y coordinate of the user					|
| pos_z    |int         |The z coordinate of the user				|
| range 	 |int 	  |The range of incoming listeners, in units of distances of the game engine.|

> Sample Code
```
ITMGContext.GetInstance().GetRoom().UpdateCoordinate(pos_x,pos_y,pos_z,10);
```