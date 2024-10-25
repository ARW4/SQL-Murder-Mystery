-- Step 1
select *
from crime_scene_report
where date = 20180115
	and city = 'SQL City'
	and type = 'murder'

-- Shows that the murder in SQL City that was on the 15th January 2024 had two witnesses 
-- Witness 1: lives at last house on "Northwestern Dr"
-- Witness 2: name = Annabel, lives somewhere on "Franklin Ave"

-- Step 2.1 (Finding Witness 1)
select id,
	name,
	license_id,
	MAX(address_number),
	address_street_name,
	ssn
from person
where address_street_name like '%Northwestern Dr%'

-- Witness 1: Morty Schapiro, id = 14887, license_id = 118009, ssn = 111564949

-- Step 2.2 (Finding Witness 2)
select *
from person
where name like '%Annabel%'
	and address_street_name = 'Franklin Ave'
	
-- Witness 2: Annabel Miller id = 16371, license_id = 490173, ssn = 318771143

-- Step 3 (finding the witness' statement)
select *
from interview
where person_id = 16371
or person_id = 14887

-- Statements reveal the following details about the murder: "Get Fit Now Gym Bag", 48Z = Gold member, number plate included "H42W", was in gym on january 9th

-- Step 4 (finding the murderers member ID)
select *
from get_fit_now_check_in
where membership_id like '48Z%'
and check_in_date = 20180109

-- Reveals that the murders membership ID is either 48Z7A or 48Z55

-- Step 5 (Finding Murders Person ID)
select *
from get_fit_now_member
where id = '48Z55'
or id = '48Z7A'

-- Returns that the murderers person ID = 67318 or 28819

-- Step 6 (Finding the Murderers Name)
select name,
	transcript
from interview i
join person p
	on i.person_id = p.id
where i.person_id = 67318
or i.person_id = 28819

-- Found the killer as Jeremy Bowers. The transcript explains how he was paid by a woman.
-- Mystery Womans details: Lots of money, around 5'5" (65") or 5'7" (67"), Tesla model S, red hair, attended SQL Symphony Concert 3 times in December

-- Step 7 (Finding the Mystery Woman License ID)
select *
from drivers_license
where car_make = 'Tesla'
and car_model = 'Model S'
and gender = 'female'
and hair_color = 'red'
and height between 65 and 67

-- Mystery woman license_id either 202298, 291182, 918773

-- Step 8 (FInding Mystery Womans name)
select *
from facebook_event_checkin fb
inner join person p 
	on fb.person_id = p.id
	where license_id in (202298, 291182, 918773)
	
-- Mystery womans name is Miranda Priestly
