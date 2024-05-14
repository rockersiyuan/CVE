SQL injection vulnerability exists in Sourcecodester Simple Online Bidding System

official website:https://www.sourcecodester.com/php/14558/simple-online-bidding-system-using-phpmysqli-source-code.html

version:v1.0

route：/simple-online-bidding-system/index.php

injection parameter:$_Get['id']

#### 1.Vulnerability analysis

The parameter $DELETE_STR here is directly spliced into the sql statement after removing the null value. There is a sql injection point.

#### 2.Vulnerability verification

We can use Cartesian product blind injection for injection. The following payload can determine that the first character of the database name is t, because it was successfully delayed at 116. The ASCII code 116 also corresponds to the lowercase letter t. By analogy, the database name and any information about the database can be obtained through blind injection.

#### 3.Exploit

Based on the above verification analysis, the SQL injection vulnerability exists. We can continue to obtain the complete name of the entire database through blind injection.

The second digit of the database is intercepted through blind injection, and the second digit is judged to be the letter d through the delay time, and the ASCII code is 100.



In summary, the complete database name can be obtained based on this sql injection vulnerability: td_oa