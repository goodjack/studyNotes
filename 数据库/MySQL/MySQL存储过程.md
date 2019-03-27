# MySQL存储过程

#### InnoDB存储引擎

先创建数据表

```mysql
CREATE DATABASE ecommerce;
USE ecommerce;
CREATE TABLE empolyess(
	id INT NOT NULL,
    fname VARCHAR(30),
    lname VARCHAR(30),
    birth TIMESTAMP,
    hired DATE NOT NULL DEFAULT '1970-01-01',
    separated DATE NOT NULL DEFAULT '9999-12-31',
    job_code INT NOT NULL,
    store_id INT NOT NULL
)
partition BY range(store_id) (
	partition p0 values less than (10000),
	partition p1 values less than (50000),
	partition p2 values less than (100000),
    partition p3 values less than (150000),
    partition p4 values less than maxvalue
);
```

然后创建存储过程

```mysql
USE ecommerce;
DROP PROCEDURE BatchInse IF EXISTS;
delimiter //
CREATE PROCEDURE BatchInsert(IN init INT,IN loop_time INT)
BEGIN
	DECLARE Var Int;
	DECLARE ID INT;
	SET Var = 0;
	SET ID = init;
	WHILE Var < loop_time DO
		insert into empolyess(id,fname,lname,birth,hired,separated,job_code,store_id) values (ID,CONCAT('chen',ID),CONCAT('haixiang',ID),Now(),Now(),Now(),1,ID);
		SET ID = ID + 1;
		SET Var = Var + 1;
	END WHILE;
END;
//
delimiter ;
CALL BatchInsert(30036,200000);
```

#### MyISAM存储引擎

先创建数据表

```mysql
 use ecommerce;
 CREATE TABLE ecommerce.customer (
 id INT NOT NULL,
 email VARCHAR(64) NOT NULL,
 name VARCHAR(32) NOT NULL,
 password VARCHAR(32) NOT NULL,
 phone VARCHAR(13),
 birth DATE,
 sex INT(1),
 avatar BLOB,
 address VARCHAR(64),
 regtime DATETIME,
 lastip VARCHAR(15),
 modifytime TIMESTAMP NOT NULL,
 PRIMARY KEY (id)
 ) ENGINE = MyISAM ROW_FORMAT = DEFAULT
 partition BY RANGE (id) (
 partition p0 VALUES LESS THAN (100000),
 partition p1 VALUES LESS THAN (500000),
 partition p2 VALUES LESS THAN (1000000),
 partition p3 VALUES LESS THAN (1500000),
 partition p4 VALUES LESS THAN (2000000),
 Partition p5 VALUES LESS THAN MAXVALUE
 );
```

再创建存储过程：

```mysql
use ecommerce;
DROP PROCEDURE ecommerce.BatchInsertCustomer IF EXISTS;
delimiter //
CREATE PROCEDURE BatchInsertCustomer(IN start INT,IN loop_time INT)
  BEGIN
      DECLARE Var INT;
      DECLARE ID INT;
      SET Var = 0;
      SET ID= start;
      WHILE Var < loop_time DO
          insert into customer(ID, email, name, password, phone, birth, sex, avatar, address, regtime, lastip, modifytime) 
          values (ID, CONCAT(ID, '@sina.com'), CONCAT('name_', rand(ID)*10000 mod 200), 123456, 13800000000, adddate('1995-01-01', (rand(ID)*36520) mod 3652), Var%2, 'http:///it/u=2267714161, 58787848&fm=52&gp=0.jpg', '北京市海淀区', adddate('1995-01-01', (rand(ID)*36520) mod 3652), '8.8.8.8', adddate('1995-01-01', (rand(ID)*36520) mod 3652));
          SET Var = Var + 1;
          SET ID= ID + 1;
      END WHILE;
  END;
  //
delimiter ;
```

记：使用存储过程生成1百万测试数据，耗时 02:31:59:87
