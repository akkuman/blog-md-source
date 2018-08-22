title: 给斐讯K1刷机并拨号e信(湖北地区测试无问题)
date: 2016-09-22 13:38:21
update: ""
author: me
tags:
- life
- Hacker
- 生活
categories:
- 生活
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---



# ◆购买斐讯k1路由器

路由器在天猫京东斐讯旗舰店都有售卖，我买的价格是159，不过有一张铃铛卡，一个月之后返还160元，相当于0元购

# ◆路由器刷不死Breed

## 1.路由与电脑有线连接好，输入192.168.2.1，完成设置
![k1basicSetting](http://7xusrl.com1.z0.glb.clouddn.com/k1basicSetting.png)

## 2.在浏览器地址栏输入：http://192.168.2.1/goform/Diagnosis?pingAddr=192.168.2.100|echo""|telnetd
(如果你的电脑ip不是192.168.2.100,请改成你电脑的ip(内网ip))

<!--more-->

## 3.打开tftp，这里用tftp32演示，按图设置
![tftpK1Setting](http://7xusrl.com1.z0.glb.clouddn.com/tftpK1Setting.png)

## 4.打开CMD,务必使用管理员权限，telnet 192.168.2.1
![cmdK1Serting](http://7xusrl.com1.z0.glb.clouddn.com/cmdK1Serting.png)

## 5.输入用户名密码
![cmd2K1Serting](http://7xusrl.com1.z0.glb.clouddn.com/cmd2K1Serting.png)

## 6.输入命令
```cmd
 1) cd /tmp

 2) tftp –g –l /tmp/breed.bin –r breed.bin 192.168.2.100

 3) cat /dev/mtd1 >/tmp/mtd1.bin
   cat /dev/mtd0 >/tmp/mtd0.bin

 4) tftp –p –r mtd1.bin –l /tmp/mtd1.bin 192.168.2.100
   tftp –p –r mtd1.bin –l /tmp/mtd1.bin 192.168.2.100

 5) mtd_write write breed.bin Bootloader
```
等待出现#字
![cmd3K1Serting](http://7xusrl.com1.z0.glb.clouddn.com/cmd3K1Serting.png)

## 7.拔掉电源，然后按住reset键插上电源，地址栏输入192.168.1.1，就进入了breed界面
![K1breed](http://7xusrl.com1.z0.glb.clouddn.com/K1breed.png)

### 懒人一键式安装法：

输入：
wget http://breed.hackpascal.net/breed-mt7620-reset1.bin
然后输入：
mtd_write write breed-mt7620-reset1.bin Bootloader

等待出现#字（代表着已经完成）

## 刷breed后语

只要路由breed不被变动，路由刷错固件也不怕，同样方式进入breed刷回正确的即可。

推荐每次刷完固件后，去固件系统管理--恢复原厂默认值。

# ◆刷openWRT
## 1.刷新固件
我在下面的文件中打包了两个固件，一个是潘多拉的K1专版，一个是openWRT，我自己使用的是专版潘多拉，各位看官自己选择，刷新固件很简单，看图
![set1](http://7xusrl.com1.z0.glb.clouddn.com/K1breedset1.png)
![set2](http://7xusrl.com1.z0.glb.clouddn.com/K1breedset2.png)

点击更新，看路由灯全部亮起后，无线网络出现,OK

# ◆安装e信(NetKeeper)插件并进行拨号
## 1.你得准备一些东西(WINSCP一个，op系统相关netkeeper一只）找到对应地区更改文件名为`sxplugin.so`

## 2.通过WINSCP登录你的路由器
** 注意使用scp协议，密码admin（第一次登录op需要重设密码依然设为admin就可以了 **

![winscpK1set](http://7xusrl.com1.z0.glb.clouddn.com/winscpK1set.png)

## 3.放入拨号插件

登录之后打开路由器，在这儿选择/(root）然后选择/usr/lib/pppd/2.4.7文件夹将你编辑好的`sxplugin.so`文件放入即可（** 这里的sxplugin.so是自己更名的，湖北的就选择wuhan的来更名，文件在文末有打包 ** ）

## 4.设置帐号密码拨号

通过浏览器登录浏览器打开网络下的接口选择WAN口点击修改，协议选择PPPOE即可，然后下面有个按钮点一下会出来填帐号密码的，账户和密码也要写对，我是重庆动态密码可以正常用。（蓝字我是加了中文包的，你刷了过后是英文呢，凑合看吧，加中文包需要路由器联网。）

![op](http://7xusrl.com1.z0.glb.clouddn.com/openWRTK1.png)
![op2](http://7xusrl.com1.z0.glb.clouddn.com/openWRTK1wan.png)

最后点击保存应用退出

## 5.最后的配置
通过WINSCP登录路由器同样打开文件夹/etc/config/找到network修改
![K1network](http://7xusrl.com1.z0.glb.clouddn.com/K1network.png)

在图中的位置输入option 'pppd_options' 'plugin sxplugin.so'这个代码即可（注意粘贴后字体是否一致，主要是‘号的问题，可保存后再打开查看，必须搞定字体格式才行），到此netkeeper就安装完了。最后重启路由器，到系统里面选时区，同步浏览器时间，保存。再到wan点击连接就能联网了。（如果进不去wan这个界面就是设置错了）

![k1lastsetting](http://7xusrl.com1.z0.glb.clouddn.com/k1lastsetting.png)

> 最后要说的，这个可用的原因是湖北地区e信2.5的算法依旧可用，有的地区加了心跳，有的地区强制升级了，并不可用,教程到此处完结，后面的有能力可以看看，工具教程打包在文末

# ◆闪讯算法源码
```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <string.h>
#include <pppd/pppd.h>
#include <pppd/md5.h>

typedef unsigned char byte;

char pppd_version[] = VERSION;

static char saveuser[MAXNAMELEN] = {0};
static char savepwd[MAXSECRETLEN] = {0};

static void getPIN(byte *userName, byte *PIN)
{
    int i,j;//循环变量
    long timedivbyfive;//时间除以五
    time_t timenow;//当前时间，从time()获得
    byte RADIUS[16];//凑位字符
    byte timeByte[4];//时间 div 5
    byte beforeMD5[32];//时间 div 5+用户名+凑位
    MD5_CTX md5;//MD5结构体
    byte afterMD5[16];//MD5输出
    byte MD501H[2]; //MD5前两位
    byte MD501[3];
    byte timeHash[4]; //时间div5经过第一次转后后的值
    byte temp[32]; //第一次转换时所用的临时数组
    byte PIN27[6]; //PIN的2到7位，由系统时间转换

    //code
    info("sxplugin : using zjxinlisx01");
    strcpy(RADIUS, "zjxinlisx01");
    timenow = time(NULL);
    timedivbyfive = timenow / 5;

    for(i = 0; i < 4; i++) {
        timeByte[i] = (byte)(timedivbyfive >> (8 * (3 - i)) & 0xFF);
    }
    for(i = 0; i < 4; i++) {
        beforeMD5[i]= timeByte[i];
    }
    for(i = 4; i < 16 && userName[i-4]!='@' ; i++) {
        beforeMD5[i] = userName[i-4];
    }
    j=0;
    while(RADIUS[j]!='\0')
        beforeMD5[i++] = RADIUS[j++];

    MD5_Init(&md5);
    MD5_Update (&md5, beforeMD5, i);
    printf("%d %s\n",i,beforeMD5);
    MD5_Final (afterMD5, &md5);

    MD501H[0] = afterMD5[0] >> 4 & 0xF;
    MD501H[1] = afterMD5[0] & 0xF;

    sprintf(MD501,"%x%x",MD501H[0],MD501H[1]);

    for(i = 0; i < 32; i++) {
        temp[i] = timeByte[(31 - i) / 8] & 1;
        timeByte[(31 - i) / 8] = timeByte[(31 - i) / 8] >> 1;
    }

    for (i = 0; i < 4; i++) {
        timeHash[i] = temp[i] * 128 + temp[4 + i] * 64 + temp[8 + i]
            * 32 + temp[12 + i] * 16 + temp[16 + i] * 8 + temp[20 + i]
            * 4 + temp[24 + i] * 2 + temp[28 + i];
    }

    temp[1] = (timeHash[0] & 3) << 4;
    temp[0] = (timeHash[0] >> 2) & 0x3F;
    temp[2] = (timeHash[1] & 0xF) << 2;
    temp[1] = (timeHash[1] >> 4 & 0xF) + temp[1];
    temp[3] = timeHash[2] & 0x3F;
    temp[2] = ((timeHash[2] >> 6) & 0x3) + temp[2];
    temp[5] = (timeHash[3] & 3) << 4;
    temp[4] = (timeHash[3] >> 2) & 0x3F;

    for (i = 0; i < 6; i++) {
        PIN27[i] = temp[i] + 0x020;
        if(PIN27[i]>=0x40) {
            PIN27[i]++;
        }
    }

    PIN[0] = '\r';
    PIN[1] = '\n';

    memcpy(PIN+2, PIN27, 6);

    PIN[8] = MD501[0];
    PIN[9] = MD501[1];

    strcpy(PIN+10, userName);
}

static int pap_modifyusername(char *user, char* passwd)
{
    byte PIN[MAXSECRETLEN] = {0};
    getPIN(saveuser, PIN);
    strcpy(user, PIN);
    info("sxplugin : user  is %s ",user);
}

static int check(){
    return 1;
}

void plugin_init(void)
{
    info("sxplugin : init");
    strcpy(saveuser,user);
    strcpy(savepwd,passwd);
    pap_modifyusername(user, saveuser);
    info("sxplugin : passwd loaded");
    pap_check_hook=check;
    chap_check_hook=check;
}
```

# ◆下载地址
[  (访问码:4854)](http://cloud.189.cn/t/umuUBrQNNRfi )
