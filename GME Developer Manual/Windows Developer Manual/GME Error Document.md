## Introduction
In order to help C + + developers to debug and use Tencent Cloud Real-Time Games Audio and Video Products API,we are now introducing this document to you.

## 
|Name          						| Value   |Meaning										|Solution
| ----------------------------------------	|:-------:	|-------------------------------------------------------	|-
|AV_ERR_CODE_FAIL					|1		|The reason need to be located by analyzing log	|Analyzing log
|AV_ERR_REPETITIVE_OPERATION		|1001	|Re-Operation									|Re-Operation
|AV_ERR_EXCLUSIVE_OPERATION		|1002	|Muter operation								|Performing same operations of other types will return this error. For example, exiting the room while entering the room will return this error. Performing the next operation after receiving the previous operation callback.
|AV_ERR_HAS_IN_THE_STATE			|1004	|Illegal argument								|Inputting illegal argument while calls the SDK interface will return this error. For example, inputting illegal APPID while entering the room will return this error.Read the API documents.
|AV_ERR_TIMEOUT					|1005	|Time out										|Check the net connection
|AV_ERR_NOT_IMPLEMENTED		|1006	|The function is not implemented.				|Some function is not implemented while calling the SDK interface will return this error.
|AV_ERR_NOT_ON_MAIN_THREAD	|1007	|Not called in main thread.						|All SDK interfaces are required to be called in main thread, if it is called in sub-thread, will return this error. Modifiy the sub-thread logic to make sure the SDK interface is called in main thread.
|AV_ERR_RESOURCE_IS_OCCUPIED	|1008	|Resourse is occupied.							|The resourse is occupied when it is needed.Figure out what resource is needed, and make sure no conflits will come out while using the SDK.
