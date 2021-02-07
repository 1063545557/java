## 查询目标时间段里的每一天的日期

```mysql
DROP PROCEDURE IF EXISTS findPerDay;
delimiter $$
create PROCEDURE findPerDay(IN start_time VARCHAR(20), IN end_time VARCHAR(20))
BEGIN
DECLARE i int;
SET i=0;
DROP temporary TABLE IF EXISTS fpd;
	 create temporary table if not exists fpd(date date);
WHILE DATE_ADD( start_time, INTERVAL i DAY ) <= end_time DO
INSERT INTO fpd VALUE (DATE_FORMAT(DATE_ADD(start_time,INTERVAL i DAY),'%Y-%m-%d'));
SET i=i+1;
END WHILE;

SELECT date.time,IFNULL(dataCount.count,0) count  FROM (
SELECT DISTINCT date AS time FROM fpd
) DATE 
LEFT JOIN (SELECT DATE_FORMAT(create_time,'%Y-%m-%d') TIME ,COUNT(1) COUNT 
FROM warning_format GROUP BY DATE_FORMAT(create_time,'%y-%m-%d')) dataCount 
ON date.time = dataCount.time;
END $$
delimiter ;
#调用
CALL findPerDay(start_time,end_time);
#CALL findPerDay('2020-12-11','2020-12-18');
```

```mysql
DROP PROCEDURE IF EXISTS findPerDay;
delimiter $$
create PROCEDURE findPerDay(IN start_time VARCHAR(20), IN end_time VARCHAR(20))
BEGIN
DECLARE i int;
SET i=0;
DROP temporary TABLE IF EXISTS fpd;
	 create temporary table if not exists fpd(date date);
WHILE DATE_ADD( start_time, INTERVAL i DAY ) <= end_time DO
INSERT INTO fpd VALUE (DATE_FORMAT(DATE_ADD(start_time,INTERVAL i DAY),'%Y-%m-%d'));
SET i=i+1;
END WHILE;
END $$
delimiter ;
#调用
CALL findPerDay('2020-12-11','2020-12-18');
SELECT date.time,IFNULL(dataCount.count,0) count  FROM (
SELECT DISTINCT date AS time FROM fpd
) DATE 
LEFT JOIN (SELECT DATE_FORMAT(create_time,'%Y-%m-%d') TIME ,COUNT(1) COUNT 
FROM warning_violation GROUP BY DATE_FORMAT(create_time,'%y-%m-%d')) dataCount 
```

## 多表连接查询

```mysql
SELECT website_name as name,violation_count,format_count,secret_count FROM (
SELECT website_name,website_url,violation_count,format_count,IFNULL(secret_count,0)AS secret_count FROM (
SELECT website_name,website_url,violation_count,IFNULL(format_count,0)AS format_count FROM (
SELECT website_name,website_url,IFNULL(violation_count,0) AS violation_count FROM monitor_website  
LEFT JOIN (SELECT violation_host,count(*) AS violation_count FROM warning_violation GROUP BY violation_host) a ON website_url=violation_host) a1
LEFT JOIN (SELECT format_host,count(*) AS format_count FROM warning_format GROUP BY format_host) b ON website_url=format_host) b1
LEFT JOIN (SELECT secret_host,count(*) AS secret_count FROM warning_secret GROUP BY secret_host) c ON website_url=secret_host) c1
ORDER BY violation_count+format_count+secret_count DESC LIMIT 7
```

![1608538394039](../%E5%B7%A5%E5%85%B7/mysql%E5%AE%9E%E4%BE%8B_1.png)