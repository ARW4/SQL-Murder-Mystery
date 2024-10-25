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

### Step 2.2
Finding Witness 2
````sql
select *
from person
where name like '%Annabel%'
	and address_street_name = 'Franklin Ave'
````
This returns us the details of witness 2
Name = Annabel Miller, id = 16371, license_id = 490173, ssn = 318771143

### Step 3
Now that we have the person ids of the two witnesses we can see what they said in their witness interview
````sql
select *
from interview
where person_id = 16371
or person_id = 14887
````
| person_id | transcript |
| ---- | ---- |
|14887|I heard a gunshot and then saw a man run out. He had a "Get Fit Now Gym" bag. The membership number on the bag started with "48Z". Only gold members have those bags. The man got into a car with a plate that included "H42W".|
|16371|	I saw the murder happen, and I recognized the killer from my gym when I was working out last week on January the 9th.|
From the witness interview transcripts we find out the following details about the murder:
- They had a "Get Fit Now Gym Bag"
- The membership number on their bag started with 48Z, hence they are a gold member
- Their number plate includes "H42W"
- They were spotted in the gym on January 9th 2018

### Step 4
Using the information about the murderer we can get a list of people that entered the gym on the 9th January 2018 and who's membership IDs start with "48Z"
````sql
select *
from get_fit_now_check_in
where membership_id like '48Z%'
and check_in_date = 20180109
````
| membership_id | check_in_date | check_in_time | check_out_time | 
| ---- | ---- | ----- | ---- |
|48Z7A|20180109|1600|1730|
|48Z55|20180109|1530|1700|

this query returns only two records, We have narrowed the murderer to two membership ID's ()
