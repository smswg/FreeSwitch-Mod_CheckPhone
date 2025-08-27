# FreeSwitch Mod_CheckPhone 模块

**很多人都对FreeSWITCH外呼手机号状态振铃音检测比较感谢兴趣，这里提供Mod_CheckPhone模块直接检测手机号振铃音，大家可以基于此模块实现检测早期媒体音,实现手机号空号识别+各种前期手机号异常状态功能**

mod_check_phone.so是模块主程序，该模块需要授权，授权价格1万，模块一次性永久授权，包售后,可开发票。如有其他定制化模块需求如源码等，请加微信或进入https://www.callwg.com/ 了解更多详情。

![](https://www.callwg.com/templets/callgw/images/ma.png)

## `Mod_CheckPhone模块介绍`

此模块由合肥标通科技有限公司开发，基于FreeSwitch1.10.9版本开发,可支持最新版本的FreeSwitch，使用C++11原生编写。本模块基于神经网络声纹算法识别振铃音需求，经大量生产高并发测试非常稳定，特别适合需要检测早期媒体音判断是否是空号等场景,如果您是做外呼系统的相关科技公司可以直接采购本模块,节省大量的踩坑成本。本公司有成熟的callwg语音呼叫系统，欢迎各位老板前来采购。


## `Mod_CheckPhone模块使用教程`
1.首先下载mod_check_phone.so文件到FreeSwitch运行目录,正常目录是/usr/local/freeswitch/mod/。
2.复制check_phone.xml文件到/usr/local/freeswitch/conf/autoload_configs/目录下，并修改相关参数。

```
<configuration name="check_phone.conf" description="mod_check_phone configuration">
    <settings>
		<!-- 推送手机号识别结果到接口 -->
        <param name="pushUrl" value=""/>
		<!-- 匹配阈值 不建议动 -->
		<param name="match-threshold" value="150.0f"/>
		<!-- 检测窗口 建议3000 单位毫秒 -->
		<param name="detect-window" value="3000"/>
    </settings>
</configuration>
```

3.修改/usr/local/freeswitch/conf/autoload_configs/modules.conf.xml 增加一行<load module="mod_check_phone"/>,重启fs系统生效模块。

4.模块使用命令如下:

```
originate {origination_caller_id_name=955555,origination_caller_id_number=955555,absolute_codec_string=^^:PCMU:PCMA,leg_timeout=30,execute_on_media=start_check_phone}user/4000 park inline
```

命令解释：使用955555号码呼叫分机4000,此时955555号码听到分机4000的振铃音, 同时检测到媒体流时开启start_check_phone模块,模块会持续检测匹配算法。

## `mod_check_phone模块事件结果`

1.上面的命令执行成功以后，一旦匹配上模型会立刻生成结果，分别通过Http Post json到业务接口或Esl事件方式通知,可以二选一方式。

Esl事件如下：

​		模块识别结果事件

```
Event-Subclass: check_phone::detected
Event-Name: CUSTOM
Unique-ID: 58a08a69-7858-407a-be69-679150d34193
FreeSWITCH-Hostname: MiWiFi-R3D-srv
FreeSWITCH-Switchname: MiWiFi-R3D-srv
FreeSWITCH-IPv4: 192.168.31.164
FreeSWITCH-IPv6: ::1
Event-Date-Local: 2017-12-10 11:30:32
Event-Date-GMT: Sun, 10 Dec 2017 03:30:32 GMT
Event-Date-Timestamp: 1512876632835590
Event-Calling-File: mod_check_phone.cpp
Event-Calling-Function: OnResultDataRecved
Event-Calling-Line-Number: 55
Event-Sequence: 914
check_phone_Detected-Label: 模板标签
check_phone_Detected-Type: 状态
Channel: sofia/external/linphone@192.168.31.210
```

​	Http接口 post方式 json数据格式：

​	

```
{"call_info":{"call_id": "f16bc610-8ddc-443a-b7e9-64405107e048","caller": "4001","callee": "4002"},"detected-label": "zc","detected_type": "50"}
json数据只需要关注detected_type字段值即可,detected_type值请联系客服了解。
```

​	
