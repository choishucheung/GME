## 개요

텐센트의 클라우드 게임 멀티미디어 엔진 SDK를 사용해 주셔서 감사합니다. Unreal 개발자들의 디버그 및 텐센트의 클라우드 게임 멀티미디어 엔진 제품인 API의 접속에 편의를 제공하기 위해 Unreal 개발에 적용되는 빠른 접속 문서를 소개해 드립니다.

## 사용 계통도
![image](Image/i1.png)


### GME 사용 시 중요 사항

GME 快速入门文档只提供最主要的接入接口，更多详细接口请参考相关接口文档。


|重要接口     | 接口含义|
| ------------- |-------------|
|Init   				|初始化 GME 	|
|Poll    				|触发事件回调	|
|EnterRoom	 		|进房  			|
|EnableMic	 		|开麦克风 		|
|EnableSpeaker		|开扬声器 		|

**설명**

**GME의 인터페이스 호출 완료 후의 반환값은 QAVError.OK로 수치는 0임.**

**동일 스레드 하에서 GME의 인터페이스를 호출해야 한다.**

**GME의 입실은 인증(authentication)이 필요하며 문서의 인증 부분 내용 참조. **

## 빠른 접속 절차

### 1、1.입수 단일 예
음성 기능 사용 시 우선 ITMGContext 대상을 입수해야 합니다.

> 예시 코드  

```
ITMGContext* context = ITMGContextGetInstance();
context->SetTMGDelegate(this);
```



### 2、SDK 초기화
해당 인터페이스는 텐센트 클라우드 콘솔에서 발송한 SdkAppId 번호를 파라미터로 하고 openId를 추가하며, 이 openId는 유일한 1개 사용자를 표시하며, 규칙은 App 개발자가 스스로 정하고 App 내부에서 중복하지 않으면 됩니다(현재 INT64만 지원).
SDK 초기화 후에야 입실이 가능합니다.

> 함수 원형 

```
ITMGContext virtual void Init(const char* sdkAppId, const char* openId)
```
|파라미터     | 유형         |의미|
| ------------- |:-------------:|-------------|
| sdkAppId    	|char*   	|텐센트 클라우드 콘솔에서 수입한 SdkAppId 번호					|
| openID    	|char*   	|OpenID는 Int64 유형만 지원(string 인커밍으로 전환)하고 반드시 10000 이상으로 하며，사용자 식별에 사용.|

> 예시 코드

```
std::string appid = TCHAR_TO_UTF8(CurrentWidget->editAppID->GetText().ToString().operator*());
std::string userId = TCHAR_TO_UTF8(CurrentWidget->editUserID->GetText().ToString().operator*());
ITMGContextGetInstance()->Init(appid.c_str(), userId.c_str());
```

### 3、트리거 이벤트 콜백
Tick 내부 사이클의 Poll 호출을 통해 트리거 이벤트 콜백을 구현할 수 있습니다.

> 함수 원형

```
class ITMGContext {
protected:
    virtual ~ITMGContext() {}
    
public:    	
	virtual void Poll()= 0;
}

```
> 예시 코드
```
//헤더 파일 중의 성명
virtual void Tick(float DeltaSeconds);

//코드 구현
void AUEDemoLevelScriptActor::Tick(float DeltaSeconds) 
{   
ITMGContextGetInstance()->Poll();
}
```

### 4、입실
생성된 인증 정보로 입실하면 ITMG_MAIN_EVENT_TYPE_ENTER_ROOM 메시지의 콜백을 수신하게 됩니다. 
기본 입실은 마이크 및 스피커 OFF입니다.
EnterRoom인터페이스를 호출하기 전에 우선 Init 인터페이스를 호출해야 합니다.

> 함수 원형

```
ITMGContext virtual void EnterRoom(int relationId, ITMG_ROOM_TYPE roomType, const char* authBuff, int buffLen)//일반 입실 인터페이스
```
|파라미터     | 유형         |의미|
| ------------- |:-------------:|-------------|
| relationId			|int   		|룸 번호|
| roomType 			|ITMG_ROOM_TYPE	|룸 오디오 유형		|
| authBuffer    		|char*     	|인증코드			|
| buffLen   			|int   		|인증코드 길이		|

|오디오 유형     	|의미|파라미터|음량유형|콘솔 추천 샘플레이트 설정 |적용 신|
| ------------- |------------ | ---- |---- |---- |---- |
| ITMG_ROOM_TYPE_FLUENCY			|원활한 음질	|1|스피커: 통화음량, 이어폰: 미디어 음량|음질에 대한 특별한 요구가 없는 경우 16K 샘플레이트면 된다.					|원활성을 우선으로 하는 초저 딜레이 실시간 음성으로 게임 중 팀 대화 신(scene)에 사용되며, FPS, MOBA 등 유형의 게임에 적용됨.|							
| ITMG_ROOM_TYPE_STANDARD			|표준 음질	|2|스피커: 통화음량, 이어폰: 미디어 음량|음질에 대한 요구에 따라 16K/48K 샘플레이트  선택 가능 |음질이 좋고 딜레이가 적절하며 웨어울프, 보드게임 등 캐주얼 게임의 실시간 통화 신에 적용됨.  |												
| ITMG_ROOM_TYPE_HIGHQUALITY		|HD 음질	|3|스피커: 미디어 음량, 이어폰: 미디어음량|최고 효과를 얻기 위해 콘솔의 48k 샘플레이트의 고음질사양을 설정할 것을 제안.	|최고 음질, 딜레이가 긴 편이며, 음악/무도 유형 게임 및 음성 소셜 유형의 APP에 적용되며, 음악 재생, 온라인 노래방 등 고음질이 요구되는 경우에 적용됨.	|

- 음량 유형 또는 신에 특별한 요구가 있는 경우 프론트 고객센터에 연락하여 피드백하세요.
- 콘솔 샘플레이트의 설정은 게임의 음성 효과에 직접적인 영향을 미치므로 콘솔에서 샘플레이트 설정이 프로젝트 사용 신에 적합 여부를 재확인하세요.

> 예시 코드  

```
ITMGContext* context = ITMGContextGetInstance();
context->EnterRoom(roomId, ITMG_ROOM_TYPE_STANDARD, (char*)retAuthBuff,bufferLen);//일반 음성 입실 예시 코드
```

### 5、룸 가입 이벤트의 콜백
룸에 가입 완료하면 ITMG_MAIN_EVENT_TYPE_ENTER_ROOM 정보가 발송되며,  OnEvent 함수에서 판단을 합니다.
> 코드 설명
```

void TMGTestScene::OnEvent(ITMG_MAIN_EVENT_TYPE eventType,const char* data){
	switch (eventType) {
            case ITMG_MAIN_EVENT_TYPE_ENTER_ROOM:
		{
		//처리 진행
		break;
		}
	}
}
```

### 6、마이크 ON/OFF
해당 인터페이스는 마이크의 ON/OFF에 사용됩니다. 기본 룸 가입 시 마이크 및 스피커는 OFF입니다.

> 함수 원형  
```
ITMGAudioCtrl virtual void EnableMic(bool bEnabled)
```
|파라미터     | 유형         |의미|
| ------------- |:-------------:|-------------|
| bEnabled    |bool     |마이크 ON 필요 시 인커밍한 파라미터는 true이며, 마이크 OFF하면 파라미터는 false임.|
> 예시 코드  
```
ITMGContextGetInstance()->GetAudioCtrl()->EnableMic(true);
```


### 7、스피커 ON/OFF
해당 인터페이스는 스피커의 ON/OFF에 사용됩니다. 

> 함수 원형  
```
ITMGAudioCtrl virtual void EnableSpeaker(bool enabled)
```
|파라미터     | 유형         |의미|
| ------------- |:-------------:|-------------|
| enable   		|bool       	|스피커를 끄려면 인커밍한 파라미터는 false, 스피커를 켜면 파라미터는 true로 됨.|
> 예시 코드 
```
ITMGContextGetInstance()->GetAudioCtrl()->EnableSpeaker(true);
```


## 인증 관련
### 실시간 음성 인증 정보
AuthBuffer를 생성하여 관련 기능의 암호화 및 인증에 사용하며 관련 파라미터의 입수 및 세부 내용은[GME KEY 파일](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/GME%20Key%20Manual.md)참조. 

> 함수 원형
```
QAVSDK_API int QAVSDK_CALL QAVSDK_AuthBuffer_GenAuthBuffer(unsigned int appId, unsigned int authId, const char* strOpenID, const char* key, unsigned int expTime, unsigned int privilegeMap, unsigned char* retAuthBuff, unsigned int* buffLenght);
```
|파라미터     | 유형         |의미|
| ------------- |:-------------:|-------------|
| appId    		|int   		|텐센트 콘솔에서 수입한 SdkAppId 번호|
| authId    		|int  		|가입할 룸명|
| strOpenID  		|char*    	|사용자 표시					|
| key    		|char*	    	|텐센트 콘솔에서 수입한 KEY				|
| expTime    		|int   		|authBuffer 시간 초과		|
| privilegeMap   	|int    	|권한(ITMG_AUTH_BITS_DEFAULT는 모든 권한 보유 의미）|
| retAuthBuff   	|char*    	|리턴한 authbuff|
| buffLenght   		|int    	|리턴한 authbuff 길이|



> 예시 코드  
```
unsigned int bufferLen = 512;
unsigned char retAuthBuff[512] = {0};

QAVSDK_AuthBuffer_GenAuthBuffer(atoi(SDKAPPID3RD), roomId, "10001", AUTHKEY, expTime, ITMG_AUTH_BITS_DEFAULT, retAuthBuff, &bufferLen);
```