## 简介

欢迎使用腾讯云游戏多媒体引擎 SDK 。为方便开发者接入腾讯云游戏多媒体引擎产品，这里向您介绍适用于腾讯云游戏多媒体引擎 SDK 的语音角色说明。
## 角色介绍
不同角色代表不同的通话质量，在接入 SDK 后，使用实时语音功能需要对角色进行配置。进入实时语音房间时候需要角色名作为参数进入房间，以获得相应的通话质量和通话效果，优化用户体验。
在进入房间后也可以调用函数对角色进行重新设置。

## 角色特性
角色分别代表的通话质量：

|角色名称     | 适用场景         |关键特性|
| ------------- |:-------------:|-------------
| esports    				|适用于MOBA、竞技、射击类游戏     								|普通音质、极低延时	|
| Rhost    	|适用于 MMORPG 类游戏的指挥模式，只有指挥主播可上麦     		|高流畅、低延时		|
| Raudience    		|适用于 MMORPG 类游戏的指挥模式，只有指挥主播可上麦     		|高流畅、低延时		|
| Werewolf    			|适用于狼人杀、休闲游戏等										|高音质、网络抗性强	|
| host    			|适用于 MMORPG 类游戏的主播模式，主播可与玩家进行语音视频互动	|高音质、网络抗性强	|
| audience    			|适用于 MMORPG 类游戏的主播模式，主播可与玩家进行语音视频互动	|高音质、网络抗性强	|

## 使用场景详细说明
![](https://github.com/TencentMediaLab/GME/blob/master/Image/r1.png?raw=true)
### 1.对战类 App，应该根据业务类型选择：
>esports:
>或者带指挥形式的 Rhost 和 Raudience ：

### 2.休闲类App，应该根据业务类型：
>Werewolf:
>或带直播形式的 host 和 audience ：

## 3.角色制定
### 1.打开网址[腾讯云控制台](https://console.qcloud.com/ilvb) 
登录账号，在应用列表中选择需要编辑的应用，在【SPEAR引擎配置中】点击【变更场景】，选择【游戏实时语音】。

![](https://github.com/TencentMediaLab/GME/blob/master/Image/r13.png?raw=true)

### 2.接下来选择需要配置的平台。  

![](https://github.com/TencentMediaLab/GME/blob/master/Image/r8.png?raw=true)

### 3.在界面中可以看到默认建立的一些角色。  

![](https://github.com/TencentMediaLab/GME/blob/master/Image/r9.png?raw=true)

### 4.点击【编辑】按钮可以对现有的角色进行编辑，如更改名字及更改效果。

![](https://github.com/TencentMediaLab/GME/blob/master/Image/r10.png?raw=true)

### 5.如果要新建自定义角色，在界面最下面可以找到【添加角色及配置】按钮。
![](https://github.com/TencentMediaLab/GME/blob/master/Image/r11.png?raw=true)

### 6.点击可添加自定义用户角色及配置。
![](https://github.com/TencentMediaLab/GME/blob/master/Image/r12.png?raw=true)

## 4.角色的使用
每个角色对应有一个名字，作为参数填入相应的函数中。
>Unity中示例代码：
```
IQAVContext.GetInstance().GetRoom().ChangeRole(“Player”, authBuffer);
```
>Android中示例代码：
```
ITMGContext.GetInstance(this).GetRoom().ChangeRole("Player",bytes);
```
>iOS中示例代码：
```
[[[ITMGContext GetInstance]GetRoom ]ChangeRole:@"Playre"authBuffer:authBuffer];
```