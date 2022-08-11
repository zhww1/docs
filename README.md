[![数学学习](other/novice-guide/figures/1.png)](#数学学习)



[![数学学习](other/novice-guide/figures/2.png)](#协议学习)

[![数学学习](other/novice-guide/figures/3.png)](#应用开发)

<p style="color: #AD5D0F;font-size: 22px; ;">
    初次查看得小伙伴，可以持续参考本篇得路线跟我一起进步吧。
</p>

# 简介

<!-- tabs:start -->

## 学习内容 ##









## 应用开发



**针对人群：有 C 语言、嵌入式基础，想学习嵌入式当中一些技巧得人群**



### 案例一

- 开发中需要经常逻辑判断一些变量得值域是否在一个范围或者是否是某几个值.

```c
int  g_nCount;/* 定义一个全局变量，是需要判断得。*/
/* 使用宏 */
#define   ABS_CAN_IS_VALID_RETARDERGEAR(val)     (val >= 1  && val <=  5) 
if(ABS_CAN_IS_VALID_RETARDERGEAR(g_nCount)){
        /* ...  */
}
/* 使用宏在嵌入式当中是很常见得，这里展示其实就是为了想让代码判断哪里少一些 ，而且这里是判断是否有效得。 */
```



### 案例二

- 在嵌入式当中有时候我们可能会用到结构体得二维数组 ，那么二维数组得初始化有那些方法。/* 一个维度里面有5个元素，每个元素是一个结构体 */

  ```
  structConfConstantFancFan[5][5] = { { { 370, 360 }, { 380, 370 }, { 390, 380 }, { 400, 390 }, { 400, 400 } }, \
        {{ 360, 350 }, { 370, 360 }, { 380, 370 }, { 390, 380 }, { 400, 390 } }, \
        {{ 360, 350 }, { 370, 360 }, { 380, 370 }, { 390, 380 }, { 400, 390 } }, \
        {{ 360, 350 }, { 370, 360 }, { 380, 370 }, { 390, 380 }, { 400, 390 } }, \
        {{ 360, 350 }, { 370, 360 }, { 380, 370 }, { 390, 380 }, { 400, 390 } } };
  
  /* 这种初始化方法就比较好 */
  ```

  

### 案例三

- 在嵌入式当中有一种组合开关（有3个以上开关），需要发送开关状态，组合开关拨动得很快，造成结果就是可能中间状态丢失。而要求拨动快的时候，也需要至少发一次中间得开关状态。在中间得时候就一直发固定值就可以。

```c
/* 定一个一个4态得变量 */
uint8_t  g_lastPDUPos ;/* 4态 */
/* 定义4种状态 */
enum {
    CAN_APP_PDU_INVALID = 0,
    CAN_APP_PDU_ON,
    CAN_APP_PDU_OFF,
    CAN_APP_PDU_ACC,
};

/* 开关在OFF */
if(IN_ACC == 0X00  && IN_ON == 0x00){
    /* 如果上次是ON  发1 */
    if( CAN_APP_PDU_ON == g_lastPDUPos ){
        ptr->PDUPosition = 0x01;
    }else{
        ptr->PDUPosition = 0x00;   /* 一直发0 ，但是上次是ON有效，就发一次1 */
    }
    
    /* 置OFF状态*/
    g_lastPDUPos = CAN_APP_PDU_OFF;
}
/* 开关在ACC*/
else if( IN_ACC == 0x00 && IN_ON = 0X00 ){
     ptr->PDUPosition = 0x01;
     /* 置ACC状态*/
    g_lastPDUPos = CAN_APP_PDU_ACC;
}
/* 开关在ON挡 */
else if(IN_ON == 0X01){
    /*上次是OFF状态 发送一次ACC ，再发ON状态*/
    if(g_lastPDUPos == CAN_APP_PDU_OFF){
        ptr->PDUPosition = 0x01;
    }else{
		ptr->PDUPosition = 0x02;
    }
    /*置ON状态*/
    g_lastPDUPos = CAN_APP_PDU_ON;
}

/* 这种方法就比较好 */
```



### 案例四

- 嵌入式中，输出保护后，有逻辑业务需要等5分钟开启后才能输出，但是有些需求要求，重新有一个开关有效后要 立刻输出，不需要再等5分钟了。



```c

/*
	那么应该逻辑层处理下，如果重新开关后，让中间层提供一个函数接口执行立刻输出，而执行输出得时候需要判断下，状态开着，但是需要判断下，比如继电器状态是开着，但是MOSFET已经关了。
*/
/* 中间层继电器提供接口函数 */
void  RelayRefreshSteady(void){

	if(RELAY_STATEON == g_relays[RELAY_TYPE_STEADY1].state && MOULE_STATE_OFF == MosfetsGet(MOSFETS_TYPE_STEADY1)){

		MosfetSet(MOSFETS_TYPES_TYPE_STEADY1, MODULE_STATE_ON,0);	

	}
}
```



### 案例五

### 案例六

### 案例七



