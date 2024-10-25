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
The script above gives us the information of two of the witnesses
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
| id | name | license_id | address_number | address_street_name | ssn |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 14887 | Morty Schapiro | 118009 | 4919 | Northwestern Dr | 111564949 |

### Step 2.2
Finding Witness 2
````sql
select *
from person
where name like '%Annabel%'
	and address_street_name = 'Franklin Ave'
````
This returns us the details of witness 2
| id | name | license_id | address_number | address_street_name | ssn |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 16371 | Annabel Miller | 490173 | 103 | Franklin Ave | 318771143 |

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
We now have two possible murderers
| membership_id | check_in_date | check_in_time | check_out_time | 
| ---- | ---- | ----- | ---- |
|48Z7A|20180109|1600|1730|
|48Z55|20180109|1530|1700|

### Step 5
Using the membership IDs found in the previous step to search the members table for member details
````sql
select *
from get_fit_now_member
where id = '48Z55'
or id = '48Z7A'
````

| id | person_id | name | membership_start_date | membership_status |
| ---- | ---- | ---- | ---- | ---- |
| 48Z55 | 67318 | Jeremy Bowers | 20160101 | gold |
| 48Z7A | 28819 | Joe Germuska | 20160305 | gold |

We now have further details of the potential murder

### Step 6 
Now that we have the person_id of the murderer we can now cross reference to the information we have about the murderers number plate

````sql
select p.name
from person p
inner join (select id as License_id
	,plate_number
from drivers_license) dl
on dl.license_id = p.license_id

where id = 67318 and plate_number like '%H42W%'
or id = 28819 and plate_number like '%H42W%'
````

| Murderer | ID |
| ---- | ---- | 
|Jeremy Bowers | 67318 |

We have found the murderer!

However if we look at Jeremy Bowers interview transcript
````sql
select *
from interview
where person_id = 67318
````
| Person ID | transcript |
| ---- | ---- |
| 67318 | I was hired by a woman with a lot of money. I don't know her name but I know she's around 5'5" (65") or 5'7" (67"). She has red hair and she drives a Tesla Model S. I know that she attended the SQL Symphony Concert 3 times in December 2017. |

We now know that Jeremy Bowers was paid by a mystery woman to commit the murder.

### Step 7
USing the information from the murderers interview to find the mystery woman
````sql
select *
from drivers_license
where car_make = 'Tesla'
and car_model = 'Model S'
and gender = 'female'
and hair_color = 'red'
and height between 65 and 67
````
| id | age | height | eye_color | hair_color | gender | plate_number | car_make | car_model |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 202298 | 68 | 66 | green | red | female | 500123 | Tesla | Model S |
| 291182 | 65 | 66 | blue | red | female | 08CM64 | Tesla | Model S |
| 918773 | 48 | 65 | black | red | female | 917UU3 | Tesla | Model S |

We have no narrowed down the mystery woman to three license IDs.

### Step 8
Now that we have the three possible License IDs we can search the facebook_event_checkin table
````sql
select *
from facebook_event_checkin fb
inner join person p 
	on fb.person_id = p.id
	where license_id in (202298, 291182, 918773)
````
| person_id | event_id | event_name | date_id | name | license_id | address_number | address_street_name | SSN |
|----|----|----|----|----|----|----|----|----|
| 99716 | 1143 | SQL Symphony Concert | 20171206 | Miranda Priestly | 2022981883 | Golden Ave | 987756388 | 99716 |
| 99716 | 1143 | SQL Symphony Concert | 20171212 | Miranda Priestly | 2022981883 | Golden Ave | 987756388 | 99716 |
| 99716 | 1143 | SQL Symphony Concert | 20171229 | Miranda Priestly | 2022981883 | Golden Ave | 987756388 | 99716 |
