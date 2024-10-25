# SQL-Murder-Mystery
SQL Murder Mystery is a fun SQL Challange that is aimed to test users ability, by exploring the SQL Database, finding clues and following leads.<br>[Link To Murder Mystery Challange](https://mystery.knightlab.com/)

## Challange Walkthrough
Below is outlined the steps that I have took to solve the SQL Murder Mystery Challange. Within the repository you will find the a file containing the entire SQL script.

### Step 1
The challange gives us some details abotut the crime: 
- It was on 15th January 2018
- it was a murder
- it took place in SQL City
````sql
select *
from crime_scene_report
where date = 20180115
	and city = 'SQL City'
	and type = 'murder'
````
Using this information the below script shows us how mamy witness' there are. 
| Witness | Details |
| ----- | ----- |
| 1 | Lives at the last house on "Northwestern Dr" |
| 2 | Name is Annabel, lives somewhere on "Franklin Ave" |

### Step 2.1 
Finding Witness 1
````sql
select id,
	name,
	license_id,
	MAX(address_number),
	address_street_name,
	ssn
from person
where address_street_name like '%Northwestern Dr%'
````
This returns us the details of witness 1
Name = Morty Schapiro, id = 14887, license_id = 118009, ssn = 111564949
