## 개요
텐센트의 클라우드 게임 멀티미디어 엔진 SDK를 사용해 주셔서 감사합니다. 개발자들의 디버그 및 텐센트의 클라우드 게임 멀티미디어 엔진 제품인 API의 접속에 편의를 제공하기 위해 GME 3D 음향효과의 접속 기술 문서를 소개해 드립니다.

## 3D 효과음 접속
### 1、1.스위치 3D 효과
이 함수는 스위치 3D 효과음에 사용됩니다. 3D 효과음을 사용하기 전에 반드시 이 함수를 호출해야 합니다.
주의: 3D효과음만 수신하고 3D 효과음을 발신하지 않는 사용자도 이 함수를 호출해야 합니다.

> 함수 원형  
```
QAVAudioCtrl virtual int EnableSpatializer(bool enable)
```
|파라미터     | 유형         |의미|
| ------------- |:-------------:|-------------
| enable    |bool         |ON 여부 설정|



### 2、2.현재 3D 효과음 상태 입수
이 함수는 현재 3D 효과음 상태를 입수하며 반환값은 bool 유형 수치입니다.

> 함수 원형  
```
QAVAudioCtrl virtual bool IsEnableSpatializer()
```
|반환값	|의미	|
| ------- |---------|
| true    	|ON 상태      	|
| false    	|OFF 상태      	|  

### 3、3.음원 방위 업데이트(방향 포함)
이 함수는 음원 방위각 정보의 업데이트에 사용되며, 사용자 openID를 인커밍한 다음 프레임 당 호출로 3D 효과음 효과를 구현할 수 있습니다.

#### 거리와 음파 감쇠 사이의 관계

3D 효과음 중 음원 음량의 크기와 음원과의 거리는 일정한 감쇠 관계가 있습니다. 단위 거리가 500이상이면 음량은 거의 제로로 감쇠됩니다.

|거리범위(엔진 단위)|감쇠 공식	|
| ------- |---------|
| 0< N <40  	|감쇠 계수: 1.0(음량 감쇠 무)|
| N≥40  |감쇠 계수: 40/N |

![](https://github.com/TencentMediaLab/GME/blob/master/Image/t1.jpg)

> 함수 원형  
```
QAVAudioCtrl virtual int UpdateSpatializer(string identifier,float azimuth,float elevation,float distance_cm)
```
|파라미터     | 유형         |의미|
| ------------- |-------------|-------------
| identifier   		|string	|사용자를 식별하도록 1개 identifier를 입력한다(identifier는 입실 시 이미 확정)|
| azimuth    		|float	|방위 파라미터(계산 필요)											|
| elevation    	|float 	|각도 파라미터(계산 필요)											|
| distance_cm    	|float  	|거리 파라미터(계산 필요)										|

>함수 원리

![](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/Windows%20Developer%20Manual/Image/w0.png)

그림의 파라미터를 보면, 수신측 사용자를 A점 위치를 가정한다면 발송측 사용자는 B점 위치이며 ,<a href="https://www.codecogs.com/eqnedit.php?latex=\angle&space;CAB'" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\angle&space;CAB'" title="\angle CAB'" /></a>  는  azimuth 방위，<a href="https://www.codecogs.com/eqnedit.php?latex=\angle&space;B'AB" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\angle&space;B'AB" title="\angle B'AB" /></a> 는  elevation 각도，AB 는 즉  distance_cm 거리다.
좌표  <a href="https://www.codecogs.com/eqnedit.php?latex=A\left&space;(&space;x_{1},&space;y_{1},&space;z_{1}&space;\right&space;)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?A\left&space;(&space;x_{1},&space;y_{1},&space;z_{1}&space;\right&space;)" title="A\left ( x_{1}, y_{1}, z_{1} \right )" /></a> ，<a href="https://www.codecogs.com/eqnedit.php?latex=B\left&space;(&space;x_{2},&space;y_{2},&space;z_{2}&space;\right&space;)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?B\left&space;(&space;x_{2},&space;y_{2},&space;z_{2}&space;\right&space;)" title="B\left ( x_{2}, y_{2}, z_{2} \right )" /></a>，를 <a href="https://www.codecogs.com/eqnedit.php?latex=A\left&space;(&space;0,&space;0,0&space;\right&space;)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?A\left&space;(&space;0,&space;0,0&space;\right&space;)" title="A\left ( 0, 0,0 \right )" /></a>，<a href="https://www.codecogs.com/eqnedit.php?latex=B\left&space;(&space;x,&space;y,z\right&space;)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?B\left&space;(&space;x,&space;y,z\right&space;)" title="B\left ( x, y,z\right )" /></a>，로 전환한다고 가정한다면 그중 <a href="https://www.codecogs.com/eqnedit.php?latex=x=x_{2}-x_{1},y=y_{2}-y_{1},z=z_{2}-z_{1}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?x=x_{2}-x_{1},y=y_{2}-y_{1},z=z_{2}-z_{1}" title="x=x_{2}-x_{1},y=y_{2}-y_{1},z=z_{2}-z_{1}" /></a>
계산식은 다음과 같다.

![](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/Windows%20Developer%20Manual/Image/w1.png)


> 예시 코드
```
private void CalculatePosition()
{
	Transform selftrans = currentPlayer.gameObject.transform;
	Vector3 relativePos = new  Vector3 (playerPrefab.transform.position.x - selftrans.position.x, playerPrefab.transform.position.y - selftrans.position.y, playerPrefab.transform.position.z - selftrans.position.z);
	Vector3 rotation = Quaternion.Inverse(selftrans.rotation) * relativePos;  
	double distance = 0;
	double azimuth = 0;
	double elevation = 0;
	double x = rotation.z;
	double y = rotation.x;
	double z = rotation.y;
	double sqxy = Math.Sqrt(x*x + y*y);
	distance = Math.Sqrt(relativePos.x*relativePos.x + relativePos.y*relativePos.y + relativePos.z*relativePos.z)*10;
	if (y != 0)
	{
		if (x != 0)
		{
			if (x > 0)
			{
				azimuth =  Math.Atan(y / x);
			}
			else
			{
				azimuth = (Math.PI) + Math.Atan(y / x);
			}
		}
		else
		{
			if (y > 0)
			{
				azimuth = -Math.PI/2;
			}
			else
			{
				azimuth = Math.PI/2;
			}
		}
	}
	else
	{
		if (x > 0)
		{
			azimuth = 0;
		}
		else
		{
			azimuth = -Math.PI;
		}
	}

	if (sqxy != 0)
	{
		elevation = Math.Atan(z / sqxy);
	}
	else
	{
		if (z > 0)
		{
			elevation = Math.PI;
		}
		else
		{
			elevation = -Math.PI;
		}
	}
	Debug.LogFormat(string.Format ("3Daudio UpdateSpatializer, azimuth:{0}, elevation:{1}, distance:{2}", (float)(azimuth * 180)/Math.PI, (float)(elevation * 180)/Math.PI,distance));		
}
```


