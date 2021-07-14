写了个存储过程

```mysql
DELIMITER //
CREATE DEFINER=`crmuser`@`172.16.%` PROCEDURE `countdata`(IN a VARCHAR(20), in num int)
BEGIN
	DECLARE c DECIMAL(18,4) DEFAULT 0;
	DECLARE count int DEFAULT 0;
	DECLARE tmp int;
	DECLARE finaldate DATE;
	set tmp = 0;
	while tmp<num do
	set finaldate = date_add(a,interval tmp day);
	SELECT sum(log.goldNetWeight) as goldNetWeight,count(log.barCode) as total into c , count

			from ······

	insert into `zzxtest` values(finaldate, c, count);

	set tmp = tmp + 1;
	end while;
END;
//
DELIMITER ;
```

