## 简介
欢迎使用腾讯云游戏多媒体引擎 SDK 。为方便开发者调试和接入腾讯云游戏多媒体引擎产品 API，这里向您介绍 GME 3D 音效的接入技术文档。


## 3D音效接入
### 1.开关 3D 音效
此函数用于开关 3D 音效。在使用 3D 音效之前必须先调用此函数。
>注意：只接收 3D 音效而不发出 3D 音效的用户也需要调用此函数。

> 函数原型  
```
QAVAudioCtrl virtual int EnableSpatializer(bool enable)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------
| enable    |bool         |设置是否开启|



### 2.获取当前 3D 音效状态
此函数获取当前 3D 音效的状态，返回值为 bool 类型数值。

> 函数原型  
```
QAVAudioCtrl virtual bool IsEnableSpatializer()
```
|返回值	|意义	|
| ------- |---------|
| true    	|开启状态     
| false    	|关闭状态         

### 3.更新声源方位（包含朝向）
此函数用于更新声源方位角信息，传入用户 openID 后每帧调用便可实现 3D 音效效果。

>距离与声音衰减的关系

3D 音效中，音源音量的大小与音源距离有一定的衰减关系。单位距离超过500之后，音量衰减到几乎为零。

|距离范围（引擎单位）|衰减公式	|
| ------- |---------|
| 0~40   	|音量衰减：1.0
| 40～N  |音量大小：40/N          

![](https://github.com/TencentMediaLab/GME/blob/master/Image/t1.jpg)

> 函数原型  
```
QAVAudioCtrl virtual int UpdateSpatializer(string identifier,float azimuth,float elevation,float distance_cm)
```
|参数     | 类型         |意义|
| ------------- |-------------|-------------
| identifier   		|string	|传入一个 identifier，以识别用户（identifier 在进房时候已经确定）	|
| azimuth    		|float	|方位参数（需要计算）											|
| elevation    	|float 	|角度参数（需要计算）											|
| distance_cm    	|float  	|距离参数，以厘米为距离单位（需要计算）							|

>函数原理

![](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/Windows%20Developer%20Manual/Image/w0.png)

从图看参数，假设接收端用户为 A 点位置，发送端用户为 B点位置 ,<a href="https://www.codecogs.com/eqnedit.php?latex=\angle&space;CAB'" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\angle&space;CAB'" title="\angle CAB'" /></a> 为 azimuth 方位，<a href="https://www.codecogs.com/eqnedit.php?latex=\angle&space;B'AB" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\angle&space;B'AB" title="\angle B'AB" /></a> 为 elevation 角度，AB 即为 distance_cm 距离。
假设坐标 <a href="https://www.codecogs.com/eqnedit.php?latex=A\left&space;(&space;x_{1},&space;y_{1},&space;z_{1}&space;\right&space;)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?A\left&space;(&space;x_{1},&space;y_{1},&space;z_{1}&space;\right&space;)" title="A\left ( x_{1}, y_{1}, z_{1} \right )" /></a> ，<a href="https://www.codecogs.com/eqnedit.php?latex=B\left&space;(&space;x_{2},&space;y_{2},&space;z_{2}&space;\right&space;)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?B\left&space;(&space;x_{2},&space;y_{2},&space;z_{2}&space;\right&space;)" title="B\left ( x_{2}, y_{2}, z_{2} \right )" /></a>，转换为<a href="https://www.codecogs.com/eqnedit.php?latex=A\left&space;(&space;0,&space;0,0&space;\right&space;)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?A\left&space;(&space;0,&space;0,0&space;\right&space;)" title="A\left ( 0, 0,0 \right )" /></a>，<a href="https://www.codecogs.com/eqnedit.php?latex=B\left&space;(&space;x,&space;y,z\right&space;)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?B\left&space;(&space;x,&space;y,z\right&space;)" title="B\left ( x, y,z\right )" /></a>，其中 <a href="https://www.codecogs.com/eqnedit.php?latex=x=x_{2}-x_{1},y=y_{2}-y_{1},z=z_{2}-z_{1}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?x=x_{2}-x_{1},y=y_{2}-y_{1},z=z_{2}-z_{1}" title="x=x_{2}-x_{1},y=y_{2}-y_{1},z=z_{2}-z_{1}" /></a>
则计算公式为：

![](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/Windows%20Developer%20Manual/Image/w1.png)


>示例代码
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


## 音效距离效果接入
### 设置接收语音距离范围
此函数用于设置接收的语音范围（距离以游戏引擎为准）。
>注意：1、调用此函数需要每帧调用
>2、进房的前提下调用。
>3、游戏中每个用户都需要调用。

> 函数原型  
```
ITMGRoom int UpdateCoordinate(int pos_x, int pos_y, int pos_z, int range)
```
|参数     | 类型         |意义|
| ------------- |-------------|-------------
| pos_x    |int         |传入用户自身坐标的 x 坐标						|
| pos_y    |int         |传入用户自身坐标的 y 坐标						|
| pos_z    |int         |传入用户自身坐标的 z 坐标						|
| range 	 |int 	  |传入收听的范围，以游戏引擎的距离单位为单位		|


