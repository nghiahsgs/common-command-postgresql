# common-command-postgresql
common command postgresql

## login
login postgresql
```
psql -U postgres -h localhost
psql -U postgres -h localhost -p 5432
psql -U postgres -h localhost -p 5432 db_name
```
logout postgresql
```
\? help
\q
\x : expand mode => date show vertical
```

## database

after login, connect to specific db_name
```
\c db_name
```

list all db
```
\l
```
create new db (after every single command, need semicolon)
```
create database new_db;
```

drop a db
```
drop database db_name;
```

## table
create new table without constrant
```sql
CREATE TABLE person(
    id INT,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    gender VARCHAR(50),
    date_of_birth DATE
);
```

```sql
CREATE TABLE person(
    id BIGSERIAL NOT NULL PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    gender VARCHAR(50) NOT NULL,
    date_of_birth DATE NOT NULL,
    email VARCHAR(255)
);
```

show all table (d: describe)
```
\d
\dt only show table
\d person
```


counter numbers of tables:
```
select count(*)
from information_schema.tables
where table_schema = 'public';
```

drop table
```
DROP TABLE tb_name
```

## insert new record
insert new record
```
INSERT INTO person(first_name,last_name,gender,date_of_birth,email)
VALUES ('nghia','nguyen','male','1997-02-06','nghiahsgs@gmail.com');
```

insert demo data (mockaroo) and download file sql
```
mockaroo.com
```
execute list command in file sql
```
\i /root/person.sql
```

## SELECT
SELECT all
```
SELECT * FROM person;
```

order by
```
SELECT * FROM person ORDER BY date_of_birth ASC;
SELECT * FROM person ORDER BY date_of_birth DESC;
```

SELECT distinct
```
SELECT * FROM person;
SELECT country FROM person ORDER BY country ASC;
SELECT DISTINCT country FROM person ORDER BY country ASC;
```
```
=> q to exit
```

SELECT with condition
```
SELECT * FROM person WHERE gender='Female';
SELECT * FROM person WHERE gender='Female' AND (country='Vietnam' OR country='Taiwan');
```

comparision
```
SELECT 1=1;
SELECT 1<2;
SELECT 1>2;
SELECT 1<>2;
```

limit
```
SELECT * FROM person LIMIT 5;
```
limit and offset
```
SELECT * FROM person LIMIT 5 OFFSET 5;
SELECT * FROM person OFFSET 5 FETCH FIRST 5 ROW ONLY;
```

in
```
SELECT * FROM person WHERE country in ('Vietnam','China');
```

between
```
SELECT * FROM person WHERE id BETWEEN 1 and 10;
SELECT * FROM person WHERE date_of_birth BETWEEN DATE '1997-08-03' and DATE '1997-09-03';
```

like
```
SELECT * FROM person WHERE email LIKE '%.com';
<!-- start any 5 chars -->
SELECT * FROM person WHERE email LIKE '_____m@%';
```

ilike
```
SELECT * FROM person WHERE country LIKE 'p%';
SELECT * FROM person WHERE country ILIKE 'p%';

```

GROUP BY
```
SELECT country,COUNT(*) as count_peope_in_this_country FROM PERSON GROUP BY country ORDER BY country;

SELECT country,COUNT(*) as count_peope_in_this_country FROM PERSON GROUP BY country ORDER BY count_peope_in_this_country DESC;
```

GROUP BY HAVING + condition (HAVING right after GROUP BY)
```
SELECT country,COUNT(*) as count_peope_in_this_country FROM PERSON GROUP BY country ORDER BY count_peope_in_this_country ASC;

SELECT country,COUNT(*) as count_peope_in_this_country FROM PERSON GROUP BY country HAVING COUNT(*)>5 ORDER BY count_peope_in_this_country ASC;
```


## Static
```sql
create table car (
    id BIGSERIAL NOT NULL,
	company VARCHAR(50) NOT NULL,
	model VARCHAR(50) NOT NULL,
	price INT NOT NULL
);
```
prepare test data (vi car.sql)
```sql
insert into car (company, model, price) values ('Nissan', 'Murano', 32130);
insert into car (company, model, price) values ('Rolls-Royce', 'Phantom', 84545);
insert into car (company, model, price) values ('Jeep', 'Grand Cherokee', 43037);
insert into car (company, model, price) values ('Maserati', 'Spyder', 54144);
insert into car (company, model, price) values ('Mitsubishi', 'Outlander', 83393);
```

excute file sql
```
\i /root/car.sql
```


Max,Min, AVG
```
SELECT MAX(price) FROM car;
SELECT MIN(price) FROM car;
SELECT AVG(price) FROM car;
```

GROUP BY company,model and calc AVG(price)
```
SELECT company,model,count(*) as number_product,AVG(price) as avg_price FROM car GROUP BY company,model ORDER BY avg_price;
```

ROUND
```
SELECT ROUND(price) FROM car;
```

SUM
```
SELECT SUM(price) FROM car;
```


```
SELECT 1+1 as result;
SELECT 10-5 as result;
SELECT 10*5 as result;
SELECT 10/5 as result;
SELECT 10^2 as result;
SELECT 10! as result;
SELECT 10%3 as result;
```

Apply Calc all rows
```
SELECT *,ROUND(price*0.1,1) as tax FROM car;
```

COALESCE
```
SELECT COALESCE(1) as result;
SELECT COALESCE(null,2) as result;
SELECT COALESCE(null,null,3) as result;
SELECT COALESCE(null,10,3) as result;
SELECT COALESCE(email,'Email not provided') as new_email from person;
```

NULL IF (NULLIF(a,b) => return NULL if a==b else return a)
```
SELECT NULLIF(10,10);
SELECT NULLIF(9,10);
SELECT NULLIF(11,10);
```

Handle case when 10/0 => err => handle division by zero
```
SELECT 10/NULL; =>NULL
SELECT 10/NULLIF(0,0); =>NULL
```



## Date and time
```
SELECT now();
SELECT now()::date;
SELECT now()- INTERVAL'1 YEAR';
SELECT now()- INTERVAL'10 YEARS';
SELECT now()- INTERVAL'10 DAYS';
SELECT now()+ INTERVAL'10 MONTHS';
```

Extract
```
SELECT Extract(YEAR from NOW());
```

AGE
```
SELECT * FROM person;
SELECT date_of_birth,AGE(NOW(),date_of_birth) as age FROM person;
SELECT date_of_birth, EXTRACT(YEAR from AGE(NOW(),date_of_birth)) as age FROM person;

```

## constraint
DROP constraint
```
ALTER TABLE person DROP constraint person_pkey;
```
ADD primary key => primary key unique btw rows
```
ALTER TABLE person ADD PRIMARY KEY(id);
```

ADD constraint
```
ALTER TABLE person ADD constraint email_must_unique UNIQUE(email);
ALTER TABLE person ADD constraint email_must_unique UNIQUE(filed1,filed2);
```

ADD constraint check
```
ALTER TABLE person ADD constraint check_gender_before_insert CHECK(gender='Female' or gender='Male');
insert into person (first_name, last_name, email, gender, date_of_birth, country) values ('Shirleen', 'Gilogly', 'zzzz@mapquest.com', 'Bigender', '2011-03-03', 'New Zealand');
```

## UPDATE always use with where (if not => update entire table > toang)
```
UPDATE tb_name set email ='nghiahsgs@gmail',first_name ='nghiahsgs' WHERE id = 1
```

## DELETE always use with where (if not => update entire table > toang)
```
DELETE from person where id = 10;
```

## ON conflict
```
INSERT INTO person(id, first_name, last_name, email, gender, date_of_birth, country)  VALUES(1,'nghia','nguyen','nghiahsgs@gmail.com','Male',DATE'1997-02-06','Vietnam');

it will throw error =>  duplicate key value violates unique constraint "person_pkey"
```

It will not throw any error and do nothing
```
INSERT INTO person(id, first_name, last_name, email, gender, date_of_birth, country)
VALUES(1,'nghia','nguyen','nghiahsgs@gmail.com','Male',DATE'1997-02-06','Vietnam')
ON CONFLICT (id) DO NOTHING;
```

It will not throw any error and do nothing
```
INSERT INTO person(first_name, last_name, email, gender, date_of_birth, country)
VALUES('nghia','nguyen','nghiahsgs@gmail.com','Male',DATE'1997-02-06','Vietnam')
ON CONFLICT (email) DO NOTHING;
```

## Upsert (Update or insert). Insert if has nothing, update if has one
```
INSERT INTO person(id,first_name, last_name, email, gender, date_of_birth, country)
VALUES(1008,'nghia','nguyen','nghiahsgs20@gmail.com','Male',DATE'1997-02-06','Vietnam')
ON CONFLICT (id) DO UPDATE SET email = EXCLUDED.email;
```

## One - One relationship
```sql
CREATE TABLE person(
    id BIGSERIAL PRIMARY KEY not null,
    name VARCHAR(255) not null,
    age INT not null,
    car_id BIGINT not null REFERENCES car(id),
    UNIQUE(car_id)
);
CREATE TABLE car(
    id BIGSERIAL PRIMARY KEY not null,
    name VARCHAR(255) not null
);
```

insert car
```
INSERT INTO car(name) VALUES ('honda');
INSERT INTO car(name) VALUES ('suzuki');
INSERT INTO car(name) VALUES ('xe dap');
```

insert person
```
INSERT INTO person(name,age,car_id) VALUES ('nghiahsgs',20,1);
```
If remove car_id =1 in tablle car => ERROR because car_id = 1 still exitst in table person

## JOIN
### INNER JOIN, A+B=> C (bwt A and B is foreign key)
```
select * FROM person JOIN car ON person.car_id = car.id;
select person.id,person.name,car.id,car.name as car_name FROM person JOIN car ON person.car_id = car.id;
```
### LEFT JOIN, if left table has null foreign key => return all content of this. else => join right table
```
select * FROM person LEFT JOIN car ON person.car_id = car.id;
```
## Delete foreign key
Notice: if you want to remove car, u need remove person before hand or update set car_id = ''

## Export result of sql query to csv
```
\copy (select * from person) TO '/root/a.csv' DELIMITER ',' CSV HEADER;
```

## RESTART SEQUENCE
```
ALTER SEQUENCE car_id_seq RESTART WITH 10;
```
