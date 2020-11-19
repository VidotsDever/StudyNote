#### Inner Join



![](D:\Github\StudyNote\assets\sql-inner-join.png)



```
SELECT column_name
FROM table1
INNER JOIN table2
ON table1.column_name = table2.column_name;
```

`Inner Join`与`Join`实现的查询结果相同，下面是`Join`语句：

```
SELECT Service_Id, Seller_Name, Purchaser_Name, Plot_No 
FROM Purchaser p, Seller s
WHERE p.Service_Id = s.Id
```



#### Left Outer Join

![](D:\Github\StudyNote\assets\sql-left-outer-join.png)



```
SELECT column_name
FROM table1
LEFT JOIN table2
ON table1.column_name = table2.column_name;
```



#### Right Outer Join

![](D:\Github\StudyNote\assets\sql-right-outer-join.png)



```
SELECT column_name
FROM table1
RIGHT JOIN table2
ON table1.column_name = table2.column_name;
```



#### Full Outer Join



![](D:\Github\StudyNote\assets\sql-full-outer-join.png)



```
SELECT column_name
FROM table1
FULL OUTER JOIN table2
ON table1.column_name = table2.column_name;
```



#### Cross Join

![](D:\Github\StudyNote\assets\cross-join-sql.png)



```
SELECT column_name
FROM table1
CROSS JOIN table2
ON table1.column_name = table2.column_name;
```

