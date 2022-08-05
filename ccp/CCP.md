# CCP

它是基于CAN的标定协议，标定就是Calibration的意思。标定那就有测量查看效果。测量就是我们标定工具能够获取ECU内部的参数。这就是测量。1999年发布了2.1版本。

* 协议定义	

  普通控制命令(Generic Control Command)

  +  Master与某个Slave建立链接
  + Master与某个Slave断开链接
  + 数据传输的控制
  + 握手报文（命令返回代码或者错误代码）

  数据采集命令(Data Acquisition Command)

  + 数据传输的初始化(Master)
  + 数据传送的执行（Slave）

  协议版本管理

  * 版本机制

       Major版本号（0--255）

       Minor协议版本号(0--9)

  * 版本兼容性

       主从设备Major协议版本一定相同

       注设备的Minor协议版本大于或等于从设备的版本

       主从设备必须支持所有Non-Optional命令

* Seed&Key

  * 产品ECU出厂后资源都是收到保护

    * master 12 获取Seed     master 13   算出来的 key

    * 三把锁，配三把钥匙，拥有不同权限。

      * PL_CAL  :the  key for CALIBRTION  ACCESS
      * PL_DAQ:the key for enabling the DATA AQCUISITION
      * PL_PGM : the key for FLASH ROGRAMMING

    * 设定

      * PL_CAL  PL_DAQ  PL_PGM (标定权限，测量权限，FLAHS编程的功能)这三个功能，申请的Seed的时候就告诉了下位机 申请的那个权限。

        

* CCP报文有两个(CAN的ID有两个)

  * CRO 
  * DTO 
  * 主从需要配对根据ID配对的

* DAQ模式 测量用的

  * POLLing  一个参数 
  * DAQ 模式 一次7个字节的数据

  假如每个参数都是1个字节，那么后者效率高一些。

ODT概念：DAQ 发送PID	数据	数据	数据 ......数据。它存在ODT里面的 一个ODT 对应一个DAQ报文，ODT PID   地址	地址	地址 .....地址    每一个字节数据存在一个地址上的。这是内部对应关系。DAQ是存在CAN上报文，而ODT是存在ECU内部的缓存区。

* DAQList：一个DAQlist里面可以包含多个ODT的。即多个ODT组成一个DAQList。

EXCHANGE_ID :  因为我们在做一些控制器的时候，我们控制器的一些信息，存储在控制器里面去，比如控制器的版本是多少，那家单位生产的，控制器的生产日期是什么，什么时间 ，什么批次。我们都可以存到ID里面去。放什么信息是整车厂自己定制的。

DTO ：第四个字节：从设备ID的长度是多少，以字节表示。从设备ID长度总共多少字节。设备ID 总长度是多少字节，比如 上面那些生产日期，时间，一共多少字节。

第五字节：什么样的格式的，供应商需求自己定义。

第6  7 是两个掩码，这两个掩码，资源就是上面是seed 和key 有关，6 个字节是资源可用是否有这个资源，7保护 有这个资源是否是受保护的的。

6 ：是否有这个资源（标定的功能，测量的功能，烧写的功能），1代表 是可以用的。

7：是否是保护的。1是保护的。

从设备再回复了交换ID之后，会 SetMTA0这个动作。指向从设备ID的第0个字节，，，主设备就会发送让ECU上传数据UPLOAD 。这个命令。

SET_MTA  : 地址扩展，是有些单片机 具有分页机制，可以放分页。

GET_DAQ_SIZE  获取大小。需要知道ECU某一个DAQ List当中有多少ODT。

​	CRO 请求某一个DAQ List  

​	DTO响应这个DAQList当中有多少ODT。

SET_DAQ_PTR 设置DAQList指针0x15  是为了把某一个地址写到某一个DAQ列表了里面的ODT里面去。把这个地址写到某一个DAQ列表的ODT里面去。它是写DAQ配合使用的。。。。

第几个DAQList 的第几个ODT的第几个元素。就是这样设置的。。。。

WRITE_DAQ_PTR  写DAQList入口 0X16   地址信息放在第四到第七个字节。



流程如下：

CRO  :SET_DAQ_PTR   

DTO ：应答一下

CRO : WRITE_DAQ  写个地址

DTO :  应答一下。  也就说15和16是配合使用的。

 

DAQList配置好后，就可以开始停止传输数据了STOP_START 了。一般都是由标定工具自己来完成的。我们不去管。

STOP_START  0x06 

也就是STOP_START之后  DAQ_DTO就开始主动上传了。



DISCONNET   断开链接OX07



SET_S_STATUS  设置会话状态，一般用不到，因为一般收到报文都会进入不同状态。一般不需要。

GET_S_STATUS 获取会话状态，

BUILD_CHESUM 计算校验和，上下位机都会校验，不一致的情况。说明参数不一致。这个时候就会询问这个参数

是上传还是下载。比如下位机参数传上来 覆盖掉上位机的参数。

CLEAR_MEMORY ：FLASH烧写用到的，清除某内存范围（0x10），擦除某一个空间。

流程

SET_MTA  设置一个地址。

CLEAR_MEMORY  删除范围大小。



PROGRAM  ---下载最多5个编程字节 （0x18）.FLASH烧写用到。

SET_MTA  设置地址

PROGRAM  下载

PROGRAM_6  ---下载6个编程字节 （0x22）.FLASH烧写用到。

SET_MTA  设置地址

PROGRAM _6 下载



MOVE 移动内存块（0x19） 它会用SET_MTA0  和SET_MTA1 

把一个内存放到另一个内存里面去。

流程

SET_MTA0

SET_MTA1

MOVE 指令



DIAG_SERVICE --诊断服务（0x20）  因为现在诊断服务都比较规范了，所以相对用不到了。

ACTION_SERVICE ---行为服务（0x21 ） 也很少用，如果特殊用到在查这个表



TEST   ---测试可用性（0x05）  最初建立链接的时候可以用TEST用来测试。



START_STOP_ALL  -- 开始/停止同步数据传输(0x08)  开始DAQ全部功能。

SELECT_CAL_PAGE  -- 选择标定数据页（0x11），这个用的也比较少，再做FLASH标定的时候，我们需要将FLASH参数拷贝到RAM区域，有一种特殊用法，参数既可以从FLASH获取也可以从RAM区获取。当你标定的时候参数就从RAM区获取的，那么这时候选择标定页，参数到底从FLASH区选择还是从RAM区选择。

GET_ACTIVE_CAL_PAGE ---获得当前激活得标定页（0x09）

当你不知道当前得参数在FLASH还是在RAM区，用这个获取下。用的也不是特比多。















































