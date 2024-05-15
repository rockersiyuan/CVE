SQL injection vulnerability exists in Sourcecodester Simple Online Bidding System

official website:https://www.sourcecodester.com/php/14558/simple-online-bidding-system-using-phpmysqli-source-code.html

version:v1.0

route：/simple-online-bidding-system/index.php?page=view_prod&id=1

injection parameter:$_Get['id']

#### 1.Vulnerability analysis

The parameter $_Get['id'] here is directly spliced into the sql statement after removing the null value. There is a sql injection point.

![image-20240515140442303](https://github.com/rockersiyuan/CVE/blob/main/SourceCodester%20Simple%20Online%20Bidding%20System%20Sql%20Inject-1.assets/image-20240515140442303.png)

#### 2.Vulnerability verification and exploit

We can exploit vulnerabilities using methods such as federated query injection. The database name and any information about the database can be obtained through this injection point.

First, you can test that the number of queries is 9, and further test the echo positions of the 9 query results, as shown in the figure below.

```
page=view_prod&id=-2+union+select+1,2,3,4,5,6,7,8,9%23
```

![image-20240515142817374](https://github.com/rockersiyuan/CVE/blob/main/SourceCodester%20Simple%20Online%20Bidding%20System%20Sql%20Inject-1.assets/image-20240515142817374.png)

Then you can select 3 and 4 as the echo positions, modify the payload to query the database name and current database user name.

```
page=view_prod&id=-2+union+select+1,2,database(),user(),5,6,7,8,9%23 
```

![image-20240515143141889](https://github.com/rockersiyuan/CVE/blob/main/SourceCodester%20Simple%20Online%20Bidding%20System%20Sql%20Inject-1.assets/image-20240515143141889.png)

We can also use sqlmap to conduct injection point testing. The test results are shown in the figure below. There are also SQL injection vulnerabilities that can be exploited.

![image-20240515140535912](https://github.com/rockersiyuan/CVE/blob/main/SourceCodester%20Simple%20Online%20Bidding%20System%20Sql%20Inject-1.assets/image-20240515140535912.png)
