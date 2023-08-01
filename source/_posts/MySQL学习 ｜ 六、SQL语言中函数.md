---
title: MySQL学习 ｜ 六、SQL语言中函数
date: 2023-08-01 21:03:24
tags:
---
**函数：**是指一段可以直接被另一段程序调用的程序或代码。



#### **一、字符串函数**

| 函数                       |                           功能                            |
| :------------------------- | :-------------------------------------------------------: |
| CONCAT(S1, S2, ..., Sn)    |     字符串拼接，将字符串S1，S2...Sn拼接成一个新字符串     |
| LOWER(str)                 |                  将字符串str全部转为小写                  |
| UPPER(str)                 |                  将字符串str全部转为大写                  |
| LPAD(str, n, pad)          | 左填充，用字符串pad对str的左边进行填充，达到n个字段的长度 |
| RPAD(str, n, pad)          | 右填充，用字符串pad对str的右边进行填充，达到n个字段的长度 |
| TRIM(str)                  |                去掉字符串头部和尾部的空格                 |
| SUBSTRING(str, start, len) |       返回字符串str从start开始起的len个长度的字符串       |

1、CONCAT函数

```
SELECT CONCAT('hello', 'mysql');
输出：hello mysql
```

2、LOWER函数‍‍

```
SELECT LOWER('HELLO');
输出：hello
```

3、UPPER函数

```
SELECT UPPER('hello');
输出：HELLO
```

4、LPAD函数  

```
SELECT LPAD('01', 5, '-');
输出：---01
```

5、RPAD函数

```
SELECT RPAD('01', 5, '-');
输出：01---
```

6、TRIM函数（只能去除两端的空格，不能去除中间的空格）

```
SELECT TRIM(' Hello Trim ');
输出：Hello Trim
```

7、SUBSTRING

```
SELECT SUBSTRING('Hello MySQL', 1, 5);
输出：Hello
```

  </br>

#### **二、数值函数**‍‍

| 函数        | 功能                               |
| ----------- | ---------------------------------- |
| CEIL(x)     | 向上取整                           |
| FLOOR(x)    | 向下取整                           |
| MOD(x, y)   | 返回x/y的模                        |
| RAND()      | 返回0～1内的随机数                 |
| ROUND(x, y) | 求参数x的四舍五入的值，保留y位小数 |

1、CEIL函数

```
SELECT CEIL(1.6);    
输出：1

SELECT CEIL(1.1);    
输出：1
```

2、FLOOR函数

```
SELECT FLOOR(1.6);    
输出：2

SELECT FLOOR(1.1);   
输出：2
```

3、MOD函数(求余数)  

```
SELECT MOD(3, 4);   
输出：3

SELECT MOD(6, 4);    
输出：2
```

4、RAND  

```
SELECT RAND();       
输出：0～1之间的随机数
```

5、ROUND‍

```
SELECT ROUND(2.34, 2);     
输出：2.34

SELECT ROUND(1.236, 2);    
输出：1.24
```

  </br>

#### **三、日期函数**‍‍‍‍

| 函数                               | 功能                                            |
| ---------------------------------- | ----------------------------------------------- |
| CURDATE()                          | 返回当前日期                                    |
| CURTIME()                          | 返回当前时间                                    |
| NOW()                              | 返回当前日期和时间                              |
| YEAR(date)                         | 返回执行date的年份                              |
| MONTH(date)                        | 返回指定date的月份                              |
| DAY(date)                          | 返回指定date的日期                              |
| DATE_ADD(date, INTERVAL expr type) | 返回一个日期/时间加上一个时间间隔expr后的时间值 |
| DATEDIFF(date1, date2)             | 返回起始时间date1和结束时间date2之间的天数      |

1、CURDATE

```
SELECT CURDATE();    
输出：2023-07-13
```

2、CURTIME

```
SELECT CURTIME();    
输出：08:37:26
```

3、NOW

```
SELECT NOW();        
输出：2023-07-13 08:38:33
```

4、YEAR、MONTH、DAY

```
SELECT YEAR(NOW());      
输出：2023

SELECT YEAR(MONTH());    
输出：7

SELECT YEAR(DAY());      
输出：13
```

5、DATE_ADD

```
SELECT DATE_ADD(NOW(), INTERVAL 70 DAY);
输出：2023-09-21 08:40:13

SELECT DATE_ADD(NOW(), INTERVAL -10 MONTH);
输出：2022-09-13 08:40:13
```

6、DATEDIFF(第一个时间减第二个时间)

```
SELECT DATEDIFF('2023-07-10', '2023-07-01');
输出：9
```

  </br>

#### **四、流程控制函数**‍‍

| 函数                                                       | 功能                                                         |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
| IF(value, t, f)                                            | 如果value为true则返回t，否则返回f。                          |
| IFNULL(val1, val2)                                         | 如果val1不为空则返回val1，否则返回val2。                     |
| CASE WHEN [val1] THEN [res1] ... ELSE [default] END        | 如果val1为true则返回res1...否则返回默认值default。           |
| CASE [expr] WHEN [val1] THEN [res1] ... ELSE [default] END | 如果表达式expr的值为val1则返回res1...否则返回默认值default。 |

1、IF

```
SELECT IF(true, 'OK', 'Error');    
输出：OK
```

2、IFNULL‍

```
SELECT IFNULL('OK', 'Default');    
输出：OK

SELECT IFNULL('', 'Default');      
输出：

SELECT IFNULL(NULL, 'Default');    
输出：Default
```

3、CASE WHEN THEN ELSE END

```
查询员工姓名和城市，如果城市是上海和北京返回一线城市，其他返回二线城市

SELECT 
	name, 
	CASE work_address WHEN '上海' THEN '一线' WHEN '北京' THEN '一线' ELSE '二线' END 
FROM worker;
```