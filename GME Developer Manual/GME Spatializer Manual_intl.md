## Introduction
Welcome to the Tencent Cloud Game Multimedia Engine (GME) SDK. This document describes access to technologies for development so that developers can easily debug and access to Spatializer APIs for Tencent Cloud GME.

## 3D Spatializer Access
### 1、Open 3D Spatializer 
This function is used to open 3D spatializer effects. This function must be called before using 3D spatializer effects.Users who only receive 3D sounds without 3D sounds will also need to call this function.

> Function prototype  
```
QAVAudioCtrl virtual int EnableSpatializer(bool enable)
```
|Parameter     | Type         |Description|
| ------------- |:-------------:|-------------
| enable    |bool         |Whether the setting is on|



### 2、Get current 3D spatializer status
This function gets the state of the current 3D spatializer effect and returns a value of type bool.

> Function prototype  
```
QAVAudioCtrl virtual bool IsEnableSpatializer()
```
|Callback Parameter	|Description	|
| ------- |---------|
| true    	|Open state      	|
| false    	|Close state   	|  

### 3、Update sound source location(including orientation)
This function is used to update the sound source azimuth information, and the 3D spatializer effect can be realized by calling each frame after the user's openID.

#### Distance and sound attenuation

In 3D spatializer effects, the volume of the sound source has a certain attenuation relationship with the sound source distance. After the unit distance exceeds 500, the volume decays to almost zero.

|Distance range (engine unit)|Attenuation formula|
| ------- |---------|
| 0< N <40  	|Attenuation coefficient: 1.0 (no attenuation)	|
| N≥40  		|Attenuation coefficient: 40/N 	 			|

![](https://github.com/TencentMediaLab/GME/blob/master/Image/t1.jpg)

> Function prototype    
```
QAVAudioCtrl virtual int UpdateSpatializer(string identifier,float azimuth,float elevation,float distance_cm)
```
|Parameter     | Type         |Description|
| ------------- |-------------|-------------
| identifier   		|string	|Pass in an identifier to identify the user (identifier was determined when entering the room)|
| azimuth    		|float	|Azimuth parameter (requires calculation)										|
| elevation    	|float 	|Angle parameter (requires calculation)											|
| distance_cm    	|float  	|Distance parameter (requires calculation)										|

> Function prototype    

![](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/Windows%20Developer%20Manual/Image/w0.png)

As shown in the figure, suppose that the recipient is located at point A and the sender at point B,<a href="https://www.codecogs.com/eqnedit.php?latex=\angle&space;CAB'" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\angle&space;CAB'" title="\angle CAB'" /></a> is for azimuth angle,<a href="https://www.codecogs.com/eqnedit.php?latex=\angle&space;B'AB" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\angle&space;B'AB" title="\angle B'AB" /></a> for elevation angle,and AB for the distance between A and B (distance_cm).
 If the coordinates<a href="https://www.codecogs.com/eqnedit.php?latex=A\left&space;(&space;x_{1},&space;y_{1},&space;z_{1}&space;\right&space;)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?A\left&space;(&space;x_{1},&space;y_{1},&space;z_{1}&space;\right&space;)" title="A\left ( x_{1}, y_{1}, z_{1} \right )" /></a> ，<a href="https://www.codecogs.com/eqnedit.php?latex=B\left&space;(&space;x_{2},&space;y_{2},&space;z_{2}&space;\right&space;)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?B\left&space;(&space;x_{2},&space;y_{2},&space;z_{2}&space;\right&space;)" title="B\left ( x_{2}, y_{2}, z_{2} \right )" /></a>,are converted to <a href="https://www.codecogs.com/eqnedit.php?latex=A\left&space;(&space;0,&space;0,0&space;\right&space;)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?A\left&space;(&space;0,&space;0,0&space;\right&space;)" title="A\left ( 0, 0,0 \right )" /></a>，<a href="https://www.codecogs.com/eqnedit.php?latex=B\left&space;(&space;x,&space;y,z\right&space;)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?B\left&space;(&space;x,&space;y,z\right&space;)" title="B\left ( x, y,z\right )" /></a>，and <a href="https://www.codecogs.com/eqnedit.php?latex=x=x_{2}-x_{1},y=y_{2}-y_{1},z=z_{2}-z_{1}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?x=x_{2}-x_{1},y=y_{2}-y_{1},z=z_{2}-z_{1}" title="x=x_{2}-x_{1},y=y_{2}-y_{1},z=z_{2}-z_{1}" /></a>,
the formula is as follows:

![](https://github.com/TencentMediaLab/GME/blob/master/GME%20Developer%20Manual/Windows%20Developer%20Manual/Image/w1.png)


> Sample Code
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



