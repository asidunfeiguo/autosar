### 0. 所有Rte的API
0. API详述：见使用手册中关于Rte的部分
### 1. 文件夹结构
目录如下所示，需要关注的文件夹共2个，Application即应用层，Callout即基础软件的回调和Rte层的实现。
```html
├─Application
├─Arxml
├─BSW
├─Callout
├─Config
├─InternalBehavior
├─LinkFile
├─ServiceComponents
├─SwcComponents
├─VectorTableFile
```
打开Application文件夹，每个模块都会有4个文件，以AEB为例，分别是AEB.c，Rte_AEB.h，AEB_MemMap.h，Rte_AEB_Type.h，其中，需要关注的是前两个文件。
>在Rte_<模块名>.h中，可以找到本模块可以调用的所有Rte的API
>在<模块名>.c中，Runnable的注释中可以调用自己需要的API
>以AEB为例，如下图，在`User Code Start`和`User Code end`之间，可以插入本模块的逻辑。
>注1：若不在`User Code Start`和`User Code end`之间插入逻辑代码，会导致架构迭代后，代码丢失。
>注2：不能自己手动增加`User Code Start`和`User Code end`代码段，会识别不到，导致代码丢失。
![image.png|800](https://jiao-working-bucket1.oss-cn-beijing.aliyuncs.com/20240118105915.png)

### 2.<模块名>.c结构
>每个.c文件都有一个Init函数`Runnable_Init`函数。
>除DID外，每个.c文件都有一个周期触发的函数
>Dem、DID、TU（故障上传）模块，还有超时回调函数`Runnable_TimeOut`
>Dem、TU（故障上传）模块，还有收到信号的回调函数`Runnable_Receive`
### 3. 所有模块读写信号
0. 名词：
>信号：Signal
>信号组：SignalGroup
>组信号：GroupSignal，即信号组内的信号

2. 读写信号组或组信号
>读写信号组：直接调用API即可
>读写组信号：需调用读写信号组的API，获得信号组的结构体，再通过获取成员变量的方式获得组信号
3. 读写信号（注：非信号组 & 非组信号）
>直接调用API即可
### 4. SWDiag（滑窗模块）
>在周期触发的`Runnable_Send_DiagResult`中，需要从E2E读取诊断结果，目前生成代码并不支持这一功能，可以看到，调取的都是读取Com信号的API。因此，需要手动增加E2E的API，具体如下
>1. 在Config文件夹找到E2EPW.h文件，在SWDiag.c中，include此头文件。
>2. 在上述头文件中，找到需要的`E2EPW_Read_<SignalName>`接口，复制到上述Runnable即可。