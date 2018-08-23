## 简介

欢迎使用腾讯云游戏多媒体引擎 SDK 。为方便开发者调试和接入腾讯云游戏多媒体引擎产品，这里向您介绍适用于所有平台的日志文件相关技术文档。

## 提供日志相关信息
提供日志的时候，请提供相关的信息：
- 发生错误的时间；
- 日志来源（例如：Unity开发的iOS平台）；
- 日志来自发送端或接收端


## 日志相关接口
### 设置打印日志等级
用于设置打印日志等级。
#### 函数原型
```
ITMGContext virtual void SetLogLevel(int logLevel, bool enableWrite, bool enablePrint)
```
|参数     | 类型         |意义|
| ------------- |:-------------:|-------------|
| logLevel    		|int   		|打印日志级别		|
| enableWrite    	|bool   				|是否写文件，默认为是	|
| enablePrint    	|bool   				|是否写控制台，默认为是	|


|ITMG_LOG_LEVEL|意义|
| -------------------------------|:-------------:|
|TMG_LOG_LEVEL_NONE=0		|不打印日志			|
|TMG_LOG_LEVEL_ERROR=1		|打印错误日志（默认）	|
|TMG_LOG_LEVEL_INFO=2			|打印提示日志		|
|TMG_LOG_LEVEL_DEBUG=3		|打印开发调试日志	|
|TMG_LOG_LEVEL_VERBOSE=4		|打印高频日志		|

#### 示例代码  
```
ITMGContext* context = ITMGContextGetInstance();
context->SetLogLevel(0,true,true);
```

### 设置打印日志路径
用于设置打印日志路径。
默认路径为：

|平台     |路径        |
| ------------- |:-------------:|
|Windows 	|%appdata%\Tencent\GME\ProcessName|
|iOS    		|Application/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Documents|
|Android	|/sdcard/Android/data/xxx.xxx.xxx/files|
|Mac    		|/Users/username/Library/Containers/xxx.xxx.xxx/Data/Documents|

#### 函数原型
```
ITMGContext virtual void SetLogPath(const char* logDir) 
```

|参数     | 类型         |意义|
| ------------- |:-------------:|-------------|
| logDir    		|char*    		|路径|
#### 示例代码  
```
cosnt char* logDir = ""//自行设置路径
ITMGContext* context = ITMGContextGetInstance();
context->SetLogPath(logDir);
```

