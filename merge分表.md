```
DROP TABLE IF EXISTS `alldata_20160328`;
CREATE TABLE `alldata_20160328`(
id INT(11) PRIMARY KEY AUTO_INCREMENT,
`name` VARCHAR(100),
address VARCHAR(100),
phone VARCHAR(20)
)CHARSET=utf8 ENGINE=MYISAM;

```


```
DROP TABLE IF EXISTS `alldata`;
CREATE TABLE `alldata`(
id INT(11) PRIMARY KEY AUTO_INCREMENT,
`name` VARCHAR(100),
address VARCHAR(100),
phone VARCHAR(20)
)CHARSET=utf8 ENGINE=MERGE UNION(alldata_20160328) INSERT_METHOD=LAST;
```


```

DELIMITER $$
CREATE PROCEDURE createTable_pro()
BEGIN
DECLARE oldnames TEXT;
DECLARE newname VARCHAR(100);
DECLARE createtable VARCHAR(200);
DECLARE altertable TEXT;
SELECT GROUP_CONCAT(TABLE_NAME) INTO oldnames FROM INFORMATION_SCHEMA.TABLES WHERE table_name LIKE 'alldata_%';
SET newname = CONCAT('alldata_',DATE_FORMAT(NOW(),'%Y%m%d%H%i'));
SET oldnames = CONCAT(oldnames,',',newname);
SET createtable = CONCAT('CREATE TABLE ',newname,' LIKE alldata_20160328;');
SET altertable = CONCAT('ALTER TABLE alldata UNION=(',oldnames,')');
SET @sql = createtable;
PREPARE stmt FROM @sql;
EXECUTE stmt;
SET @sql = altertable;
PREPARE stmt FROM @sql;
EXECUTE stmt;
END$$
DELIMITER;
```



```
SHOW VARIABLES LIKE '%event_sche%'; 
SET GLOBAL event_scheduler = 1;
```

```
DROP EVENT IF EXISTS test_event;
CREATE EVENT IF NOT EXISTS test_event 
ON SCHEDULE EVERY 1 DAY STARTS NOW()
ON COMPLETION PRESERVE 
DO CALL createTable_pro;


insert into alldata(name,address,phone) values('小明444','幸福街道','234234234111')

```
