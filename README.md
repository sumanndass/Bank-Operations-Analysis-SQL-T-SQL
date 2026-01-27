# Bank Operations SQL and T-SQL Analysis
## Table of Content
- [Overview](#overview)
- [Business Integrity](#business-integrity)
- [Tools Used](#tools-used)
- [Database Creation and Data Loading](#database-creation-and-data-loading)
- [﻿Data Cleaning or Preparation](﻿#data-cleaning-or-preparation)
- [Explanatory Data Analysis](#explanatory-data-analysis)
 
### Overview
End to end ERD modelling, database creation, data loading, data cleaning, data visualization and reporting project on banking operations.

### Business Integrity
1.	Bank offers 5 kinds of products. Like Fixed Deposit (FD), Loan Account (LA), Savings Bank (SB), Current Account (CA), Recurring Deposit (RD). Product table has Product ID, Product Name columns.
2.	Bank operates in North, East, South, West region. Region table has Region ID, Region Name columns.
3.	Bank staff designations are Branch Manager (BM), Assistant Manager (AM), Officer (OF), Head Cashier (HC), Clerk (C). Staff table has Staff ID, Staff Name, Designation columns.
4.	Bank has 4 branches (BR1, BR3, BR4, BR6) in North region, 3 branches in East (BR2, BR7, BR8), South (BR5, BR9, BR11) and West (BR10, BR12, BR13) region respectively. Branch table has Branch ID, Branch Name, Branch Address, Brach State, Branch Zip code, regarding Region ID columns. Branch names are according to their city name.
5.	Bank maintains customer table. Table has Account ID starts from 1001 and incremented by 1, Customer Name, Customer Address, Customer State, Customer Zip code, regarding Branch ID, regarding Product ID, Date of Opening of Account ID, regarding Clear Balance (balance in account), regarding Unclear Balance (balance to be clear after few hours of received or withdrawal of money, may contain 0), Status of Account (‘O’ for Operative, ‘I’ for Inoperative, ‘C’ for Closed) columns.
6.	Bank maintains transaction table. Table has Transaction ID, Date of Transactions, regarding Account ID, regarding Branch ID, regarding Transaction Type (‘CW’ for Cash Withdrawal, ‘CD’ for ‘Cash Deposit’, ‘CQD’ for Cheque Deposit), Cheque Number if cheque deposited, Cheque Date if cheque deposited, Transaction Amount, regarding Staff ID.
7.	The account, branch, product, region, and staff IDs all have unique values.
8.	The current date should be used as the Date of Transaction (DOT) and Date of Opening (DOO).
9.	A cheque that is over six months old is not acceptable.
10.	Accounts marked as "Inoperative" should not be used for transactions.
11.	The clerk's alteration of a transaction can results in a difference between the old and new amounts of the transaction, not exceeding 10%.
12.	The policy prohibits more than three cash withdrawal transactions in a single account on the same day.
13.	The transaction type 'Cheque Deposit' should not have 'NULL' in the 'Cheque Number' and 'Cheque Date' columns.
14.	A product should not be removed if it has attached accounts, and similar checks are required where appropriate.
15.	The transaction Amount should not be negative.
16.	The transaction type should be either 'CW', 'CD', or 'CQD'.
17.	The minimum balance for a Savings Bank should be Rs. 1,000/=.
18.	The 'Account Table' should be updated implicitly when an account transaction amount is inserted or updated.
19.	If the account does not have a minimum balance, withdrawals should be prohibited and an appropriate message should be displayed.
20.	The cash deposit transaction amount exceeding Rs. 50,000/= should be inserted into the 'High Value Transaction' table.
21.	The monthly transaction limit should be less than 5, and if it exceeds Rs. 50/= will be debited.
22.	The daily maximum cash withdrawal limit is Rs.50,000/-, and any excess will result in a 1% debit charge.

### Tools Used
- SQL Server - Data Analysis, Data Cleaning -- [Download Here](https://www.microsoft.com/en-in/sql-server/sql-server-downloads)
- T-SQL - Data Analysis, Reports -- [Download Here](https://www.microsoft.com/en-in/sql-server/sql-server-downloads)

### Database Creation and Data Loading
- ERD Modelling

  ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/fe9b1be1-c59a-4b10-9d8b-9b68a67c0b5e)
- create a database named ‘bank’
   ```sql
   create database bank
   ```
- use the 'bank' database
   ```sql
   use bank
   ```
- **first create those tables which have only primary key and no foreign keys.** 👍
- create a 'product_table'
   - col_name – data_type - remarks
   - prod_id – contains only 2 character – primary key
   - prod_name – maximum allowed 20 character – null not allowed
   ```sql
   create table product_table
   (
	   prod_id      char(2)       primary key,
	   prod_name    varchar(20)
   )
   ```
- Insert into 'product_table'
   ```sql
   insert into product_table values
   ('FD', 'Fixed Deposit'),
   ('LA', 'Loan Account'),
   ('SB', 'Savings Bank'),
   ('CA', 'Current Account'),
   ('RD', 'Recurring Deposit')
   ```
   ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/1cc3ea0c-00da-4e71-b6d6-0dbb0e5dc42b)
- create a 'region_table'
   - col_name – data_type - remarks
   - reg_id – integer – primary key
   - reg_name - contains only 6 character - null not allowed
   ```sql
   create table region_table
   (
	   reg_id      int      primary key,
	   reg_name    char(6)	 not null     check(reg_name in ('North', 'East', 'South', 'West'))
   )
   ```
- Insert into 'region_table'
   ```sql
   insert into region_table values(1, 'North'), (2, 'East'), (3, 'South'), (4, 'West')
   ```
   ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/a89c0a50-0789-4138-9718-56184e86d61b)
- create a 'staff_table'
   - col_name – data_type - remarks
   - staff_id – interger – primary key
   - staff_name – maximum allowed 40 character – null not allowed
   - designation – maximum allowed 2 character – null not allowed
   ```sql
   create table staff_table
   (
	   staff_id      int           primary key,
	   staff_name    varchar(40)	not null,
       designation   varchar(2)	not null      check(designation in ('BM', 'AM', 'OF', 'HC', 'C'))
   )
   ```
- Insert into 'staff_table'
   ```sql
   insert into staff_table values
  (1,'Brittany Nath', 'BM'),
  (2,'Karly Sudha', 'AM'),
  (3,'Slade Nath', 'AM'),
  (4,'Anika Srini', 'OF'),
  (5,'Cairo Muthu', 'OF'),
  (6,'Brennan Dhawan', 'OF'),
  (7,'Robert Kumar', 'HC'),
  (8,'Amal Joshi', 'HC'),
  (9,'Barrett Sahni', 'C'),
  (10,'Shafira Sahni', 'C'),
  (11,'Angela Engineer', 'C'),
  (12,'Ivor Saxena', 'C'),
  (13,'Yeo Malik', 'C'),
  (14,'Karleigh Ranga', 'C'),
  (15,'Damian Raji', 'C')
   ```
   ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/167e3c1f-6a58-4727-9a4c-d4b8b23f8bd9)
- create a 'branch_table'
   - col_name – data_type - remarks
   - br_id – maximum allowed 4 character – primary key
   - br_name – maximum allowed 20 characters, same as city name – null not allowed
   - br_add – maximum allowed 70 character – null not allowed
   - br_state – maximum allowed 20 character – null not allowed
   - br_zipcode – only 6 characters allowed – null not allowed
   - reg_id – integer – foreign key of region_table(reg_id), null not allowed
   ```sql
   create table branch_table
   (
	br_id        varchar(4)      primary key,
	br_name      varchar(20)     not null,
	br_add       varchar(70)     not null,
	br_state     varchar(20)     not null,
	br_zipcode   char(6)         not null,
	reg_id       int             not null   foreign key references region_table(reg_id)	on delete cascade on update cascade
   )
   ```
- Insert into 'branch_table'
   ```sql
   insert into branch_table values
  ('BR1', 'Delhi', '248, Sant Nagar, East Of Kailash', 'New Delhi', '110065', 1),
  ('BR2', 'Kolkata', 'Y8, Block - EP, Sector V Salt Lake', 'West Bengal', '700091', 2),
  ('BR3', 'Haryana', 'B 448 1st Floor, Nehru Ground', 'Haryana', '122001', 1),
  ('BR4', 'Aligarh', 'Mishrilal Colony Gambhir, Pura', 'Uttar Pradesh', '202001', 1),
  ('BR5', 'Bangalore', 'No.18,chitrakootapts,18thcrs,mlswrm, Malleswaram', 'Karnataka', '560055', 3),
  ('BR6', 'Bhopal', 'Plot 16, Ground Floor, Amar Stambh, Zone 1, MP Nagar', 'Madhya Pradesh', '462011', 1),
  ('BR7', 'Guwahati', '250 Jatia, Kahilipara', 'Assam', '781019', 2),
  ('BR8', 'Patna', 'Nehru Nagar, Patliputra Colony', 'Bihar', '800013', 2),
  ('BR9', 'Cochin', '343, Town Railway Rd, Hill View', 'Kerala', '682020', 3),
  ('BR10', 'Vadodara', 'Maruti Appt, B/h Wadi Shak Market, Pratapnagar', 'Gujarat', '390017', 4),
  ('BR11', 'Chennai', '61, R K Mutt Road Mandevli', 'Tamil Nadu', '600028', 3),
  ('BR12', 'Mumbai', '11, Prakash, Banganga X Road, Malabar Hill', 'Maharashtra', '400006', 4),
  ('BR13', 'Jalandhar', 'WQ 265, Nr Adda Basti Sheikh', 'Punjab', '144002', 4)
   ```
   ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/4a9aee54-ce55-43cf-a118-95844ee08871)
-  create a 'account_table'
   - col_name – data_type - remarks
   - acc_id – integer – primary key, starts from 1001 and increment by 1
   - cust_name – maximum allowed 30 characters – null not allowed
   - cust_add – maximum allowed 70 character – null not allowed
   - cust_state – maximum allowed 20 character – null not allowed
   - cust_zipcode – only 6 characters allowed – null not allowed
   - br_id – maximum allowed 4 character – foreign key of branch_table(br_id), null not allowed
   - prod_id – contains only 2 character – foreign key of product_table(prod_id), null not allowed
   - doo – datetime – null not allowed
   - clr_bal – money – null not allowed
   - unclr_bal – money – null allowed
   - status – 1 character allowed – null not allowed, default will be Operative i.e., ‘O’
   ```sql
   create table account_table
	(
	acc_id		int		primary key identity(1001,1),
	cust_name	varchar(30)	not null,
	cust_add	varchar(70)	not null,
	cust_state	varchar(30)	not null,
	cust_zipcode	varchar(6)	not null,
	br_id		varchar(4)	not null foreign key references branch_table(br_id) on delete cascade on update cascade,
	prod_id	char(2)		not null foreign key references product_table(prod_id) on delete cascade on update cascade,
	doo		datetime	not null,
	clr_bal	money		not null,
	unclr_bal	money,
	status		char(1)		not null check(status in ('O', 'I', 'C')) default('O')
	)
   ```
- Insert into 'account_table'
   ```sql
   insert into account_table values
  ('Sasha Vish','Ap #813-7938 Turpis St.','Madhya Pradesh','758821','BR12','LA','2020-10-12 02:28:38','855,318.84','50,063.02','I'),
  ('Hadley Narayan','P.O. Box 339, 854 Egestas Road','Manipur','729727','BR8','CA','2020-10-05 06:01:12','204,362.08','92,334.78','I'),
  ('Nathan Goel','P.O. Box 732, 7325 In Avenue','Daman and Diu','122215','BR7','LA','2022-08-03 04:03:57','773,147.58','5,293.94','I'),
  ('Raven Narang','3866 In St.','Tamil Nadu','458143','BR4','LA','2020-05-14 17:04:41','906,330.08','15,625.52','O'),
  ('Farrah Tyagi','109-6278 At St.','Jharkhand','187014','BR8','RD','2021-10-24 20:26:20','185,518.39','20,780.34','C'),
  ('Jenette Saini','5851 Fusce Rd.','Lakshadweep','903828','BR4','LA','2021-11-27 06:31:02','495,613.90','94,873.56','C'),
  ('Jennifer Gandhi','Ap #176-7431 Mi St.','Sikkim','657232','BR4','SB','2021-01-13 15:56:06','162,521.12','97,106.91','I'),
  ('Honorato Kumar','Ap #262-1292 Dolor St.','Maharastra','278343','BR5','SB','2022-02-03 18:49:50','177,900.45','20,052.04','C'),
  ('Teagan Veena','959-1226 Pede. St.','Goa','836555','BR6','SB','2022-01-09 16:47:51','25,017.26','80,312.24','O'),
  ('Mark Roy','552-2497 Malesuada Avenue','Pondicherry','701245','BR6','SB','2022-08-27 23:50:48','415,855.97','91,923.49','O'),
  ('Richard Suri','9662 Luctus Rd.','Dadra and Nagar Haveli','243337','BR8','RD','2019-01-27 11:05:03','104,825.07','40,705.19','C'),
  ('Moses Rai','457-666 Eu Rd.','Jammu and Kashmir','689748','BR11','RD','2018-01-03 22:49:24','201,088.63','10,964.00','I'),
  ('Drake Joshi','795-7840 Etiam Av.','Rajasthan','862304','BR12','FD','2019-02-25 22:57:41','512,412.18','87,398.28','O'),
  ('Boris Rajagopal','8206 Consectetuer Street','Meghalaya','733174','BR4','CA','2019-11-03 12:30:37','173,585.75','72,286.65','I'),
  ('Chandler Pal','P.O. Box 171, 8386 Dolor. Avenue','Karnataka','214225','BR2','LA','2019-03-29 07:36:26','653,530.18','88,021.08','I'),
  ('Unity Swami','Ap #197-5777 Sed Street','Sikkim','670011','BR2','LA','2022-04-15 09:14:24','921,020.25','35,247.03','O'),
  ('Moses Gandhi','P.O. Box 606, 6669 Est, Av.','Delhi','868786','BR11','RD','2019-09-12 22:54:16','504,994.84','25,448.53','O'),
  ('Mohammad Kapoor','Ap #694-9296 Sed Road','Himachal Pradesh','527414','BR1','CA','2018-12-13 05:13:34','253,176.16','88,465.67','O'),
  ('Kameko Chandrasekar','8783 Vel Av.','Andaman and Nicobar Islands','575230','BR8','LA','2018-08-12 20:39:29','856,813.65','11,387.76','C'),
  ('Hedda Sehgal','Ap #329-984 Posuere, Ave','Nagaland','514309','BR4','SB','2018-02-25 03:22:42','43,506.20','19,266.08','C'),
  ('Charity Jana','135-2527 Quam Av.','Bihar','558597','BR3','CA','2021-10-08 00:57:01','882,022.08','99,400.70','I'),
  ('Rama Malhotra','P.O. Box 976, 1400 At, Rd.','Nagaland','273562','BR3','FD','2021-02-12 18:39:26','183,283.23','59,910.38','I'),
  ('Phelan Charan','P.O. Box 492, 6357 Sem Ave','Punjab','544253','BR4','SB','2022-03-11 07:22:45','996,307.77','29,343.20','O'),
  ('Clare Mehta','772-2827 Nascetur Avenue','Odisha','266385','BR2','FD','2022-11-04 08:35:28','414,566.01','14,181.96','O'),
  ('Gay Aggarwal','P.O. Box 579, 1084 Adipiscing Street','Andhra Pradesh','565491','BR5','RD','2018-12-29 21:46:49','84,738.59','13,457.66','C'),
  ('Blythe Chauhan','958-4278 Sed Road','Lakshadweep','834831','BR5','CA','2020-03-15 20:11:54','63,039.53','38,170.01','C'),
  ('Ryan Subramanian','Ap #872-5396 Cras Av.','Jharkhand','642631','BR12','LA','2022-02-20 01:06:37','685,753.28','57,466.95','I'),
  ('Peter Prasad','2040 Ac Av.','Karnataka','383784','BR6','FD','2022-09-22 18:09:36','666,676.94','44,581.26','I'),
  ('Ulysses Verma','1241 Diam. Rd.','Tamil Nadu','304316','BR11','RD','2019-11-07 00:34:07','138,757.33','794.61','C'),
  ('Cullen Roy','Ap #330-8826 Diam Street','Rajasthan','760961','BR11','SB','2018-04-29 00:15:58','3,196.04','57,738.63','O'),
  ('Randall Vish','Ap #798-8682 Auctor Street','Nagaland','653148','BR11','LA','2019-10-02 12:42:17','297,373.87','7,679.79','C'),
  ('Cody Chander','Ap #347-9036 Integer Street','Manipur','328415','BR10','SB','2020-02-04 22:10:46','903,430.54','17,909.81','C'),
  ('Nola Prasad','989-6270 Ut, Av.','Jharkhand','936875','BR12','SB','2022-07-23 03:43:11','718,289.02','41,117.71','I'),
  ('Candice Nigam','857-8407 Nulla St.','Goa','137812','BR10','SB','2021-04-17 15:54:55','225,503.84','23,353.24','C'),
  ('Brenna Persaud','Ap #288-4468 Velit. St.','Andhra Pradesh','828589','BR13','SB','2019-01-28 11:53:12','873,281.71','45,369.00','O'),
  ('Zia Mittal','6447 Aliquam Avenue','Lakshadweep','495738','BR10','SB','2019-10-06 20:01:54','454,774.16','42,875.69','C'),
  ('Oliver Rastogi','9475 Quisque Street','Punjab','638515','BR12','LA','2018-09-25 05:04:19','881,615.64','45,318.60','I'),
  ('Danielle Malik','988-3560 Nullam Rd.','Tamil Nadu','233321','BR4','SB','2018-07-23 04:41:59','334,696.69','58,669.91','I'),
  ('Quinlan Kumar','Ap #742-472 Nunc, Rd.','Nagaland','792708','BR1','CA','2019-01-15 15:49:15','883,893.86','76,201.54','C'),
  ('Maia Neelam','918-3725 Sed Road','Nagaland','225265','BR13','FD','2021-07-07 15:09:52','102,698.07','12,863.23','I'),
  ('Frances Kapoor','9233 Quis Ave','Arunachal Pradesh','914660','BR5','CA','2021-10-05 23:36:53','620,360.91','56,741.87','O'),
  ('Mercedes Roy','P.O. Box 760, 6192 Id Ave','Punjab','317449','BR6','LA','2021-03-06 22:29:37','931,219.51','13,827.90','O'),
  ('Mara Soni','Ap #743-9695 Nullam Avenue','Uttar Pradesh','710968','BR13','RD','2019-02-03 03:12:28','62,227.52','68,216.06','O'),
  ('Mona Veena','206-8224 Phasellus Avenue','Andhra Pradesh','538767','BR4','FD','2021-11-19 15:59:56','980,360.06','40,269.31','C'),
  ('Barbara Chander','751-3178 Nisi Avenue','Andhra Pradesh','447473','BR7','LA','2020-07-24 02:05:37','437,729.99','91,561.01','C'),
  ('Sophia Pal','Ap #446-2189 Eu Av.','Mizoram','513459','BR4','RD','2018-12-06 14:31:33','185,254.94','91,809.53','O'),
  ('Yvette Swami','Ap #350-8922 Sed Ave','Kerala','554467','BR3','CA','2019-10-16 01:11:28','566,413.30','71,561.94','O'),
  ('Dale Meena','846-9506 Lobortis St.','Goa','346996','BR5','FD','2020-05-16 04:08:22','297,711.79','62,175.97','O'),
  ('Graiden Patel','Ap #382-4194 Nec Rd.','Bihar','448272','BR11','SB','2018-11-24 23:20:19','398,971.98','97,585.86','O'),
  ('Aladdin Anand','Ap #760-660 Sociis Ave','Tripura','903536','BR4','FD','2020-09-18 20:12:08','809,363.26','66,533.41','C'),
  ('Kimberly Sethi','8755 Eget Street','Rajasthan','844162','BR5','LA','2019-08-23 01:20:53','978,647.34','99,665.03','C'),
  ('Hamish Lata','P.O. Box 594, 5250 Non, Road','Tamil Nadu','273331','BR7','LA','2018-02-23 12:09:57','517,917.82','39,750.06','C'),
  ('Jennifer Neelam','Ap #286-7112 Risus. Ave','Chandigarh','879255','BR8','CA','2021-05-05 03:43:35','207,934.48','69,155.31','C'),
  ('Barry Vijaya','P.O. Box 381, 663 Odio Ave','Odisha','975122','BR12','RD','2022-04-25 14:22:03','341,725.39','24,727.72','I'),
  ('Samantha Jindal','P.O. Box 702, 7769 Justo St.','Daman and Diu','576762','BR13','SB','2019-06-10 08:14:42','672,937.41','70,673.28','C'),
  ('Ursula Neela','505-935 Mauris St.','Uttar Pradesh','804394','BR6','LA','2018-06-03 08:37:16','429,157.71','71,476.23','C'),
  ('Ivan Jana','5848 Ligula. St.','West Bengal','214426','BR12','FD','2018-07-21 17:35:58','550,724.14','82,497.49','C'),
  ('Keegan Aggarwal','798-8764 Amet Road','Himachal Pradesh','681679','BR2','SB','2018-10-14 03:40:22','821,397.10','48,198.15','O'),
  ('Tanisha Kumar','Ap #525-3970 Imperdiet Ave','Rajasthan','321051','BR10','SB','2020-10-20 11:14:14','42,555.61','15,604.91','O'),
  ('Abel Veer','761-109 Et Ave','Uttarakhand','785842','BR10','FD','2021-04-20 17:15:39','192,602.73','15,872.23','I'),
  ('Bernard Chande','407-2284 Mauris. Av.','Mizoram','770122','BR8','SB','2018-03-20 11:12:29','206,160.43','49,207.76','C'),
  ('Willa Jai','7910 Auctor, St.','Chandigarh','813351','BR10','LA','2018-06-17 19:21:20','956,622.36','48,694.12','I'),
  ('Philip Sehgal','P.O. Box 880, 9608 Ac Avenue','Andhra Pradesh','485869','BR2','SB','2021-05-02 05:05:53','619,781.23','71,765.61','C'),
  ('Roary Rajagopal','Ap #236-4553 Porttitor Ave','Chandigarh','416692','BR1','SB','2021-02-10 09:45:19','292,680.59','269.42','O'),
  ('Nita Tyagi','5270 Vel St.','Sikkim','388813','BR3','SB','2019-03-04 00:16:33','570,601.20','44,054.97','I'),
  ('Shoshana Narayan','Ap #136-7118 Enim. Rd.','Odisha','548364','BR7','LA','2021-12-25 21:53:07','6,389.53','47,959.44','I'),
  ('Caesar Neel','720-1054 Eros. Avenue','Meghalaya','794044','BR1','CA','2020-12-05 08:52:20','7,644.93','16,462.69','C'),
  ('Eliana Rana','Ap #405-8227 Eu Av.','Chhattisgarh','278818','BR11','CA','2022-06-14 06:46:49','316,248.29','77,403.35','I'),
  ('Honorato Rai','954-7610 In Av.','Karnataka','243587','BR12','LA','2018-03-07 12:19:27','743,947.77','23,814.22','O'),
  ('Kellie Raji','2095 Amet St.','Chandigarh','733355','BR13','FD','2020-02-27 04:31:56','335,997.04','71,684.31','C'),
  ('Odessa Meena','426-745 Hendrerit Av.','Maharastra','734433','BR8','FD','2018-12-21 18:02:23','451,951.69','39,983.03','O'),
  ('Haley Chande','P.O. Box 780, 2314 Quis, St.','Manipur','897673','BR5','SB','2019-11-30 14:34:24','279,009.88','30,961.73','C'),
  ('Buffy Veena','972-8450 Adipiscing, Road','Gujarat','281355','BR8','CA','2021-09-21 10:59:04','118,553.00','1,850.59','O'),
  ('Blythe Sudha','P.O. Box 272, 5062 Laoreet Road','Jharkhand','992263','BR2','LA','2019-07-04 10:55:51','552,995.10','5,153.76','O'),
  ('Hillary Subramanian','217-9543 Sem St.','Madhya Pradesh','227451','BR6','FD','2020-10-28 03:18:51','287,076.50','8,245.02','O'),
  ('Vanna Neelam','Ap #722-5816 Gravida Street','Jammu and Kashmir','736394','BR8','CA','2021-12-23 20:33:53','683,059.73','41,343.04','O'),
  ('Macey Nagpal','6957 Vitae Av.','Uttar Pradesh','734492','BR7','CA','2019-11-28 13:17:39','494,624.66','90,502.60','I'),
  ('Chancellor Sai','Ap #724-4292 Enim St.','Jharkhand','847712','BR3','RD','2019-03-22 16:43:57','621,048.22','56,764.52','O'),
  ('Ann Narang','976-7521 Et Road','Uttarakhand','578335','BR1','FD','2021-09-19 07:45:43','416,754.00','498.68','C'),
  ('Nolan Malhotra','Ap #650-2553 Adipiscing Rd.','Dadra and Nagar Haveli','817258','BR1','FD','2020-03-14 13:04:19','181,733.52','64,100.00','O'),
  ('Julie Mati','P.O. Box 861, 6290 Aenean Ave','Uttarakhand','453337','BR3','CA','2020-06-26 18:33:10','133,067.64','54,089.52','C'),
  ('Guinevere Samuel','485-2067 Neque. Street','Goa','372215','BR4','SB','2020-02-24 07:14:50','186,148.81','11,789.21','O'),
  ('Jacob Gandhi','334-7314 Odio. Av.','Kerala','661241','BR12','SB','2021-05-28 16:32:20','924,955.73','4,138.93','O'),
  ('Prescott Dhawan','P.O. Box 993, 4467 In, Avenue','Bihar','433696','BR7','FD','2022-04-02 13:25:10','893,431.65','15,395.39','I'),
  ('Igor Mati','721-9037 Montes, Av.','Andhra Pradesh','647175','BR6','FD','2020-06-26 23:58:19','963,392.81','1,112.52','C'),
  ('Lucian Vish','950-1555 Consectetuer St.','Lakshadweep','624238','BR3','RD','2020-11-25 18:38:23','169,362.63','9,326.14','C'),
  ('Dillon Meena','7487 Erat. Street','Uttarakhand','548391','BR4','LA','2022-11-29 23:24:32','484,349.04','89,330.17','O'),
  ('Omar Chander','539-5798 Ligula. Street','Meghalaya','229848','BR13','FD','2022-03-29 17:31:42','717,543.95','67,006.98','I'),
  ('Remedios Kishore','Ap #374-3436 Volutpat Rd.','West Bengal','616286','BR5','CA','2020-01-22 02:52:05','323,121.08','73,064.87','O'),
  ('Destiny Manju','928-3644 Nascetur Avenue','Jharkhand','457720','BR11','CA','2021-04-28 12:53:56','964,349.45','41,801.30','I'),
  ('Indira Ganesh','Ap #160-377 Libero Street','Punjab','324285','BR10','RD','2020-11-14 13:29:34','43,225.36','61,782.60','I'),
  ('Hayley Ranga','Ap #344-9184 Nibh. Ave','Mizoram','728620','BR4','CA','2020-07-01 17:50:32','207,818.82','96,262.53','I'),
  ('Jeremy Pal','Ap #743-9006 Eu, Ave','Arunachal Pradesh','217712','BR10','CA','2022-04-02 12:47:17','576,548.46','9,843.81','O'),
  ('Imelda Manohar','284-5637 Mi Street','Delhi','272804','BR12','RD','2018-12-08 17:55:10','546,736.25','53,592.40','C'),
  ('Jasper Neela','P.O. Box 721, 2688 Proin Rd.','Tamil Nadu','251295','BR7','RD','2018-11-17 21:24:45','675,443.65','3,815.51','I'),
  ('India Mehta','532-1271 Sed Road','Haryana','364711','BR4','FD','2018-05-22 03:56:03','751,973.16','1,454.57','I'),
  ('Daniel Sai','504-1992 Ac Ave','Manipur','308879','BR2','CA','2019-07-14 06:09:58','642,266.10','84,879.41','C'),
  ('Acton Lal','Ap #909-8116 Non, Road','Manipur','777936','BR8','LA','2020-06-10 12:00:09','749,051.93','95,286.13','O'),
  ('Tate Ganesh','381-3927 Vivamus St.','Manipur','836042','BR9','LA','2021-12-18 10:05:08','169,397.23','28,592.83','O'),
  ('Charity Verma','Ap #470-1753 Velit. St.','Jammu and Kashmir','741523','BR2','RD','2019-12-27 04:27:35','339,853.34','94,498.43','I')
   ```
   ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/1c8fab98-34fa-42be-aac1-0e41a1d29bb2)
- create a 'transaction_table'
   - col_name – data_type - remarks
   - tran_id – integer – primary key, starts from 1 and increment by 1
   - dot – datetime – null not allowed
   - acc_id – integer – null not allowed, foreign key of account_table(acc_id)
   - br_id – maximum allowed 4 character – foreign key of branch_table(br_id), null not allowed
   - txn_type – maximum 3 character allowed – null not allowed
   - chq_no – varchar(6) – null allowed, only 6 digits allowed
   - chq_date – datetime – null allowed
   - txn_amt – money – null not allowed
   - staff_id – integer – null not allowed, foreign key of staff_table(staff_id) 
   ```sql
   create table transaction_table
	(
	tran_id	int		primary key identity(1,1),
	dot		datetime	not null,
	acc_id		int		not null foreign key references account_table(acc_id),
	br_id		varchar(4)	not null foreign key references branch_table(br_id) on delete cascade on update cascade,
	txn_type	varchar(3)	not null check( txn_type in ('CW', 'CD', 'CQD')),
	chq_no		varchar(6)	check(chq_no between 00000 and 999999), check((txn_type = 'CQD' and chq_no is not null) or (txn_type <> 'CQD' and chq_no is null)),
	chq_date	datetime,
	txn_amt	money		not null,
	staff_id	int		not null foreign key references staff_table(staff_id) on delete cascade on update cascade
	)
	```
   ```sql
   alter table transaction_table
   add constraint chq_date_insert check((chq_no is not null and chq_date is not null) or (chq_no is null and chq_date is null))
   ```  	
- Insert into 'transaction_table'
   ```sql
  insert into transaction_table values
	('2022-08-24 11:25:13', 1011, 'BR1', 'CW', NULL, NULL, 537600.3, 12), 
	('2022-04-19 13:29:45', 1055, 'BR9', 'CW', NULL, NULL, 429637.09, 7), 
	('2021-09-26 15:15:35', 1070, 'BR11', 'CW', NULL, NULL, 665705.37, 10), 
	('2021-01-11 08:52:37', 1029, 'BR7', 'CQD', 271955, '2020-09-03 17:05:16', 718412.99, 14), 
	('2021-09-27 05:21:18', 1031, 'BR10', 'CQD', 462834, '2021-04-08 11:49:13', 673414.95, 12), 
	('2022-09-09 00:01:41', 1070, 'BR7', 'CW', NULL, NULL, 73012.38, 10), 
	('2021-12-01 09:13:27', 1074, 'BR3', 'CQD', 445664, '2021-07-04 04:49:32', 806896.37, 10), 
	('2023-12-10 10:26:22', 1005, 'BR3', 'CQD', 778431, '2023-08-17 14:08:29', 959655.44, 9), 
	('2023-03-01 11:06:31', 1001, 'BR13', 'CQD', 289551, '2022-11-01 23:40:30', 415740.77, 7), 
	('2024-01-27 18:17:15', 1003, 'BR3', 'CQD', 41568, '2023-09-23 10:46:24', 525159.88, 11), 
	('2022-02-12 16:14:13', 1055, 'BR12', 'CQD', 620732, '2021-10-20 10:20:34', 569709.62, 13), 
	('2021-05-25 07:01:20', 1053, 'BR7', 'CW', NULL, NULL, 990222.46, 7), 
	('2023-04-15 15:03:36', 1008, 'BR13', 'CQD', 537331, '2022-12-27 10:32:33', 64723.65, 13), 
	('2022-11-29 15:49:41', 1033, 'BR8', 'CW', NULL, NULL, 308156.67, 11), 
	('2021-12-16 15:58:18', 1046, 'BR1', 'CQD', 302109, '2021-07-16 18:09:23', 189485.4, 15), 
	('2021-09-20 09:24:32', 1079, 'BR5', 'CQD', 856643, '2021-04-03 20:08:24', 575313.93, 11), 
	('2022-05-21 21:06:32', 1059, 'BR8', 'CW', NULL, NULL, 861592.97, 10), 
	('2023-08-31 01:25:26', 1052, 'BR13', 'CQD', 361201, '2023-03-19 07:13:34', 35905.81, 9), 
	('2024-01-26 01:09:26', 1029, 'BR2', 'CW', NULL, NULL, 67125.86, 15), 
	('2023-03-14 00:36:12', 1020, 'BR4', 'CD', NULL, NULL, 13842.37, 13), 
	('2022-04-30 01:45:20', 1023, 'BR4', 'CD', NULL, NULL, 482239.44, 9), 
	('2023-12-13 14:55:29', 1054, 'BR4', 'CD', NULL, NULL, 645212.55, 14), 
	('2021-02-18 16:47:22', 1018, 'BR7', 'CW', NULL, NULL, 91047.83, 8), 
	('2020-02-28 02:01:44', 1012, 'BR3', 'CQD', 191486, '2019-10-12 09:48:53', 933782.18, 7), 
	('2023-03-01 02:58:56', 1011, 'BR7', 'CQD', 118168, '2022-10-05 23:33:48', 83918.86, 10), 
	('2024-02-17 16:03:51', 1034, 'BR3', 'CD', NULL, NULL, 697163.13, 15), 
	('2022-07-27 17:34:13', 1076, 'BR9', 'CW', NULL, NULL, 103542.24, 12), 
	('2022-08-10 21:10:11', 1002, 'BR13', 'CD', NULL, NULL, 303972.06, 13), 
	('2021-07-06 20:27:23', 1080, 'BR12', 'CW', NULL, NULL, 931112.91, 7), 
	('2023-12-02 07:43:41', 1073, 'BR10', 'CW', NULL, NULL, 502656.26, 13), 
	('2023-10-09 21:45:46', 1075, 'BR4', 'CW', NULL, NULL, 455458.6, 9), 
	('2022-02-05 23:46:27', 1063, 'BR11', 'CQD', 48755, '2021-08-04 20:18:59', 446724.22, 9), 
	('2023-03-27 01:19:54', 1092, 'BR8', 'CD', NULL, NULL, 392482.13, 9), 
	('2021-09-06 08:51:18', 1045, 'BR9', 'CW', NULL, NULL, 687282.64, 11), 
	('2019-11-29 08:33:23', 1031, 'BR7', 'CQD', 174404, '2019-06-27 04:38:56', 773266.78, 7), 
	('2020-12-04 11:21:46', 1049, 'BR9', 'CD', NULL, NULL, 960655.57, 11), 
	('2020-02-04 08:43:13', 1017, 'BR7', 'CW', NULL, NULL, 11651.08, 12), 
	('2023-03-07 09:35:00', 1092, 'BR11', 'CD', NULL, NULL, 406644.7, 7), 
	('2024-02-13 12:05:03', 1066, 'BR13', 'CW', NULL, NULL, 881225.93, 8), 
	('2023-02-10 23:37:51', 1075, 'BR6', 'CQD', 931116, '2022-09-25 05:34:09', 51993.72, 10), 
	('2023-07-16 11:29:25', 1028, 'BR11', 'CD', NULL, NULL, 387312.57, 10), 
	('2021-05-04 15:52:24', 1077, 'BR8', 'CD', NULL, NULL, 123810.98, 9), 
	('2021-08-29 05:41:24', 1074, 'BR8', 'CQD', 104910, '2021-03-28 03:32:56', 426738.47, 7), 
	('2021-06-12 12:18:47', 1086, 'BR8', 'CW', NULL, NULL, 974100.47, 13), 
	('2023-04-09 18:40:20', 1004, 'BR7', 'CD', NULL, NULL, 725898.48, 10), 
	('2024-01-05 03:12:30', 1023, 'BR8', 'CD', NULL, NULL, 563343.58, 15), 
	('2022-01-05 20:39:37', 1086, 'BR9', 'CQD', 947199, '2021-08-15 21:17:07', 217138.39, 12), 
	('2023-02-14 06:29:45', 1063, 'BR3', 'CW', NULL, NULL, 721689.12, 8), 
	('2024-02-08 12:44:36', 1034, 'BR2', 'CW', NULL, NULL, 40844.17, 10), 
	('2022-09-26 10:44:29', 1036, 'BR11', 'CD', NULL, NULL, 708874.46, 12), 
	('2020-07-04 04:14:28', 1058, 'BR4', 'CW', NULL, NULL, 9917.9, 7), 
	('2021-04-08 06:25:04', 1095, 'BR4', 'CW', NULL, NULL, 635533.15, 10), 
	('2022-10-26 02:51:11', 1064, 'BR2', 'CD', NULL, NULL, 552959.21, 13), 
	('2021-10-16 23:30:44', 1060, 'BR5', 'CW', NULL, NULL, 120925.52, 7), 
	('2023-03-03 09:41:09', 1095, 'BR2', 'CW', NULL, NULL, 821415.88, 7), 
	('2021-03-13 16:39:46', 1067, 'BR9', 'CW', NULL, NULL, 60121.83, 15), 
	('2022-03-06 17:35:37', 1067, 'BR11', 'CD', NULL, NULL, 373552.48, 13), 
	('2019-04-30 02:16:24', 1065, 'BR12', 'CQD', 107767, '2019-04-25 15:42:01', 350926.27, 7), 
	('2023-01-07 07:32:13', 1016, 'BR4', 'CW', NULL, NULL, 149170.58, 8), 
	('2022-12-05 02:33:48', 1075, 'BR8', 'CQD', 794721, '2022-06-09 11:16:27', 9232.64, 9), 
	('2021-06-25 05:41:00', 1081, 'BR10', 'CD', NULL, NULL, 387721.61, 9), 
	('2024-01-22 08:43:06', 1082, 'BR9', 'CW', NULL, NULL, 134978.99, 13), 
	('2024-01-13 06:48:35', 1095, 'BR12', 'CW', NULL, NULL, 239309.31, 10), 
	('2021-12-04 18:46:20', 1059, 'BR8', 'CW', NULL, NULL, 57315.92, 15), 
	('2024-01-18 19:11:42', 1093, 'BR4', 'CD', NULL, NULL, 487275.85, 14), 
	('2019-02-17 00:28:58', 1038, 'BR1', 'CQD', 464849, '2018-10-11 10:11:34', 291803.92, 8), 
	('2022-07-17 21:25:26', 1079, 'BR9', 'CW', NULL, NULL, 354525.15, 12), 
	('2021-08-22 02:30:57', 1051, 'BR10', 'CW', NULL, NULL, 33984.62, 13), 
	('2021-07-04 01:20:58', 1083, 'BR8', 'CD', NULL, NULL, 20263.42, 13), 
	('2022-08-18 14:40:32', 1017, 'BR7', 'CD', NULL, NULL, 52393.75, 14), 
	('2021-07-11 09:14:53', 1091, 'BR1', 'CW', NULL, NULL, 463920.29, 13), 
	('2024-01-30 03:33:42', 1012, 'BR8', 'CQD', 59160, '2023-09-02 10:23:57', 589764.73, 9), 
	('2022-05-11 08:57:14', 1037, 'BR7', 'CW', NULL, NULL, 830983.69, 7), 
	('2023-08-12 23:53:58', 1068, 'BR7', 'CQD', 483955, '2023-04-28 08:11:26', 552896.75, 13), 
	('2022-09-21 06:09:04', 1052, 'BR7', 'CW', NULL, NULL, 765460.61, 9), 
	('2024-01-24 03:10:26', 1054, 'BR8', 'CD', NULL, NULL, 66928.31, 9), 
	('2021-03-30 02:22:58', 1025, 'BR1', 'CD', NULL, NULL, 94233.87, 7), 
	('2023-06-07 10:33:08', 1100, 'BR11', 'CW', NULL, NULL, 73485.35, 13), 
	('2018-11-27 18:15:00', 1037, 'BR3', 'CD', NULL, NULL, 883114.83, 9), 
	('2019-10-31 01:44:58', 1019, 'BR12', 'CQD', 917383, '2019-10-17 01:34:46', 455514.58, 10), 
	('2023-12-21 17:09:02', 1058, 'BR10', 'CQD', 655017, '2023-06-29 18:38:59', 842757.54, 8), 
	('2022-08-29 18:38:08', 1027, 'BR4', 'CD', NULL, NULL, 909164.79, 8), 
	('2024-02-13 22:45:54', 1014, 'BR6', 'CD', NULL, NULL, 2877.51, 8), 
	('2022-04-03 23:05:14', 1086, 'BR8', 'CQD', 355777, '2021-11-23 08:37:23', 444416.77, 11), 
	('2023-11-12 01:47:47', 1093, 'BR4', 'CQD', 640365, '2023-07-20 15:13:44', 652674.81, 9), 
	('2023-12-27 22:00:43', 1087, 'BR4', 'CW', NULL, NULL, 118626.08, 12), 
	('2021-05-03 22:44:54', 1048, 'BR10', 'CW', NULL, NULL, 304189.17, 13), 
	('2022-11-28 16:50:20', 1079, 'BR3', 'CD', NULL, NULL, 476616.03, 11), 
	('2023-07-08 22:58:47', 1020, 'BR11', 'CQD', 970127, '2023-03-08 20:00:01', 345750.59, 8), 
	('2023-08-06 05:43:13', 1076, 'BR3', 'CD', NULL, NULL, 565714.52, 14), 
	('2020-10-27 23:03:57', 1089, 'BR2', 'CW', NULL, NULL, 952124.86, 9), 
	('2021-08-01 02:29:26', 1071, 'BR11', 'CD', NULL, NULL, 693338.62, 9), 
	('2020-03-14 06:53:39', 1019, 'BR8', 'CW', NULL, NULL, 756490.38, 12), 
	('2023-09-07 00:27:55', 1087, 'BR9', 'CW', NULL, NULL, 135883.73, 15), 
	('2021-10-29 16:11:59', 1021, 'BR7', 'CW', NULL, NULL, 718756.91, 14), 
	('2021-09-29 19:22:18', 1079, 'BR2', 'CD', NULL, NULL, 281255.62, 13), 
	('2023-05-28 02:11:02', 1016, 'BR3', 'CW', NULL, NULL, 5557.35, 7), 
	('2019-11-03 18:33:34', 1030, 'BR2', 'CQD', 325403, '2019-10-27 18:18:40', 48492.5, 7), 
	('2024-01-04 10:34:27', 1083, 'BR9', 'CW', NULL, NULL, 284499.98, 8), 
	('2021-11-05 11:36:38', 1032, 'BR4', 'CD', NULL, NULL, 58364.65, 11), 
	('2023-07-31 21:26:00', 1045, 'BR2', 'CQD', 560141, '2023-03-09 09:51:10', 7143.62, 14), 
	('2021-03-05 21:18:48', 1065, 'BR2', 'CQD', 143678, '2020-09-24 03:11:11', 86442.69, 7), 
	('2022-06-28 09:43:10', 1098, 'BR7', 'CD', NULL, NULL, 752783.21, 11), 
	('2019-09-04 02:51:02', 1043, 'BR13', 'CD', NULL, NULL, 879617.7, 13), 
	('2021-02-22 18:25:36', 1065, 'BR5', 'CW', NULL, NULL, 77620.32, 14), 
	('2020-09-17 10:08:59', 1048, 'BR1', 'CD', NULL, NULL, 112590.97, 14), 
	('2023-12-01 22:04:38', 1066, 'BR4', 'CQD', 259468, '2023-07-14 12:37:31', 704564.39, 15), 
	('2020-06-06 12:44:52', 1037, 'BR8', 'CQD', 453001, '2020-02-07 22:44:19', 82198.06, 10), 
	('2023-12-03 01:03:28', 1025, 'BR7', 'CW', NULL, NULL, 289387.49, 11), 
	('2023-12-07 20:37:50', 1021, 'BR12', 'CQD', 891997, '2023-06-07 13:21:19', 710668.05, 15), 
	('2023-01-25 16:30:55', 1029, 'BR1', 'CD', NULL, NULL, 870250.54, 14), 
	('2022-05-12 12:52:16', 1061, 'BR3', 'CD', NULL, NULL, 862477.36, 14), 
	('2018-07-24 02:26:02', 1052, 'BR4', 'CQD', 497225, '2018-07-19 16:10:19', 431840.33, 11), 
	('2019-01-10 05:58:05', 1037, 'BR10', 'CW', NULL, NULL, 18103.21, 12), 
	('2023-01-13 23:55:00', 1042, 'BR4', 'CW', NULL, NULL, 524765.19, 14), 
	('2023-12-04 01:23:04', 1009, 'BR10', 'CQD', 973172, '2023-08-11 02:06:50', 579984.3, 9), 
	('2023-11-29 22:29:48', 1013, 'BR11', 'CD', NULL, NULL, 413726.74, 8), 
	('2022-05-29 05:17:04', 1044, 'BR3', 'CD', NULL, NULL, 285668.61, 15), 
	('2021-12-23 10:45:04', 1064, 'BR12', 'CD', NULL, NULL, 515288.68, 7), 
	('2022-05-17 23:55:12', 1002, 'BR8', 'CD', NULL, NULL, 475848.44, 10), 
	('2020-12-01 07:32:34', 1074, 'BR13', 'CW', NULL, NULL, 648982.51, 8), 
	('2023-02-02 09:49:26', 1009, 'BR5', 'CD', NULL, NULL, 784503.06, 7), 
	('2023-10-24 03:33:50', 1053, 'BR11', 'CD', NULL, NULL, 558809.23, 13), 
	('2023-03-16 00:23:22', 1052, 'BR2', 'CQD', 209759, '2022-10-20 04:11:43', 20549.23, 15), 
	('2022-03-05 10:34:17', 1066, 'BR4', 'CW', NULL, NULL, 426198.73, 7), 
	('2021-12-30 20:22:20', 1079, 'BR9', 'CD', NULL, NULL, 98862.25, 9), 
	('2023-08-25 12:07:42', 1048, 'BR1', 'CQD', 457906, '2023-04-14 23:15:56', 827501.27, 9), 
	('2022-12-17 12:12:50', 1041, 'BR13', 'CD', NULL, NULL, 215960.11, 11), 
	('2021-10-15 01:20:43', 1051, 'BR9', 'CW', NULL, NULL, 898788.96, 10), 
	('2022-12-31 11:10:24', 1066, 'BR12', 'CD', NULL, NULL, 289609.02, 11), 
	('2024-01-03 15:24:56', 1048, 'BR6', 'CW', NULL, NULL, 898996.52, 7), 
	('2020-08-05 08:10:40', 1085, 'BR12', 'CQD', 900510, '2020-04-28 02:44:22', 976847.74, 13), 
	('2024-01-30 18:36:46', 1039, 'BR5', 'CW', NULL, NULL, 861204.65, 12), 
	('2021-01-11 16:04:06', 1002, 'BR10', 'CW', NULL, NULL, 778107.66, 13), 
	('2019-04-12 09:15:22', 1018, 'BR3', 'CQD', 375507, '2018-12-24 05:13:30', 499853.74, 7), 
	('2024-01-22 02:44:43', 1087, 'BR1', 'CQD', 602469, '2023-07-23 12:32:56', 214124.03, 9), 
	('2023-03-03 09:48:07', 1009, 'BR10', 'CD', NULL, NULL, 714230.85, 9), 
	('2023-03-10 12:12:37', 1027, 'BR1', 'CD', NULL, NULL, 59614.3, 9), 
	('2024-01-27 04:06:02', 1096, 'BR6', 'CD', NULL, NULL, 968909.73, 12), 
	('2022-06-25 13:31:17', 1073, 'BR11', 'CQD', 557982, '2022-01-03 01:22:04', 929478.61, 8), 
	('2023-06-08 01:13:13', 1036, 'BR12', 'CD', NULL, NULL, 934464.25, 7), 
	('2020-11-19 02:55:16', 1074, 'BR6', 'CQD', 886925, '2020-06-14 16:33:24', 624807.73, 8), 
	('2023-05-02 07:14:48', 1088, 'BR8', 'CD', NULL, NULL, 776501.23, 7), 
	('2023-10-18 03:45:39', 1063, 'BR7', 'CD', NULL, NULL, 576740.85, 7), 
	('2024-02-01 12:58:55', 1088, 'BR3', 'CD', NULL, NULL, 995922.32, 15), 
	('2019-12-23 13:57:51', 1043, 'BR6', 'CD', NULL, NULL, 945323.55, 7), 
	('2023-01-07 22:57:33', 1024, 'BR3', 'CD', NULL, NULL, 160255.49, 15), 
	('2021-12-15 09:23:30', 1085, 'BR5', 'CW', NULL, NULL, 295225.81, 7), 
	('2021-12-02 23:44:04', 1090, 'BR12', 'CQD', 848558, '2021-08-25 01:58:30', 983427.5, 8), 
	('2023-10-29 13:04:50', 1094, 'BR6', 'CD', NULL, NULL, 196197.99, 14), 
	('2021-09-10 08:01:03', 1067, 'BR2', 'CD', NULL, NULL, 476463.42, 15), 
	('2022-06-25 06:48:25', 1031, 'BR13', 'CW', NULL, NULL, 194360.07, 7), 
	('2023-10-27 21:27:56', 1009, 'BR9', 'CD', NULL, NULL, 965685.35, 14), 
	('2023-09-26 09:14:32', 1064, 'BR8', 'CQD', 431832, '2023-04-20 22:53:04', 391599.46, 11), 
	('2022-12-23 23:11:07', 1091, 'BR9', 'CQD', 645287, '2022-06-18 21:30:09', 661106.93, 12), 
	('2022-09-03 14:55:24', 1083, 'BR3', 'CD', NULL, NULL, 519766.31, 8), 
	('2024-01-27 12:33:58', 1040, 'BR4', 'CW', NULL, NULL, 570669.39, 9), 
	('2023-11-29 15:55:53', 1093, 'BR13', 'CQD', 703544, '2023-06-30 21:58:38', 738121.47, 13), 
	('2022-06-04 10:05:25', 1060, 'BR6', 'CD', NULL, NULL, 232808.85, 8), 
	('2022-07-16 13:14:35', 1026, 'BR6', 'CW', NULL, NULL, 54175.93, 12), 
	('2021-04-06 05:28:45', 1019, 'BR7', 'CD', NULL, NULL, 275686.8, 9), 
	('2021-12-20 21:19:49', 1053, 'BR10', 'CW', NULL, NULL, 132802.52, 15), 
	('2021-06-18 18:35:31', 1043, 'BR9', 'CQD', 17670, '2021-01-08 19:06:38', 84791.12, 13), 
	('2022-06-07 14:06:06', 1005, 'BR6', 'CD', NULL, NULL, 832177.65, 15), 
	('2020-05-29 12:15:55', 1094, 'BR12', 'CW', NULL, NULL, 274383.13, 14), 
	('2023-06-26 23:39:55', 1006, 'BR13', 'CW', NULL, NULL, 753903.72, 8), 
	('2023-12-14 21:41:36', 1016, 'BR2', 'CD', NULL, NULL, 430614.95, 10), 
	('2023-01-12 07:56:48', 1028, 'BR12', 'CQD', 584708, '2022-09-12 16:36:59', 14446.31, 12), 
	('2023-12-05 06:42:41', 1096, 'BR10', 'CW', NULL, NULL, 957335.59, 8), 
	('2024-02-10 03:42:11', 1074, 'BR8', 'CW', NULL, NULL, 456316.47, 12), 
	('2021-11-10 04:56:47', 1098, 'BR13', 'CQD', 679719, '2021-05-26 20:56:57', 446112.51, 14), 
	('2022-04-06 19:49:10', 1063, 'BR11', 'CW', NULL, NULL, 6363.92, 12), 
	('2022-12-03 22:23:37', 1005, 'BR8', 'CD', NULL, NULL, 858321.11, 8), 
	('2021-04-08 05:53:55', 1051, 'BR6', 'CW', NULL, NULL, 933586.82, 9), 
	('2022-10-22 10:45:40', 1080, 'BR5', 'CQD', 649033, '2022-06-13 23:46:19', 749841.59, 7), 
	('2021-02-20 20:40:21', 1095, 'BR4', 'CW', NULL, NULL, 247183.07, 7), 
	('2020-05-07 10:02:22', 1012, 'BR2', 'CW', NULL, NULL, 390773.96, 13), 
	('2021-07-08 23:04:13', 1100, 'BR3', 'CW', NULL, NULL, 785372.12, 12), 
	('2021-08-15 10:03:53', 1082, 'BR5', 'CD', NULL, NULL, 577383.39, 15), 
	('2022-06-15 23:25:58', 1093, 'BR3', 'CQD', 901209, '2021-12-29 16:26:06', 77357.85, 8), 
	('2023-11-26 00:23:45', 1028, 'BR10', 'CQD', 550033, '2023-06-13 06:34:16', 723492, 9), 
	('2020-09-13 14:23:41', 1011, 'BR12', 'CW', NULL, NULL, 457595.78, 7), 
	('2024-01-15 13:25:38', 1071, 'BR2', 'CQD', 653601, '2023-08-04 09:35:44', 285724.56, 15), 
	('2022-11-06 18:27:41', 1081, 'BR11', 'CQD', 690922, '2022-05-22 02:09:24', 741148.12, 10), 
	('2021-02-01 00:34:11', 1020, 'BR10', 'CQD', 656646, '2020-10-24 06:11:14', 860997.96, 11), 
	('2020-11-07 16:07:33', 1078, 'BR8', 'CW', NULL, NULL, 336450.94, 9), 
	('2022-07-06 04:31:17', 1060, 'BR2', 'CW', NULL, NULL, 116468.38, 13), 
	('2021-02-06 03:27:23', 1080, 'BR6', 'CW', NULL, NULL, 615770.25, 8), 
	('2022-07-23 14:03:09', 1047, 'BR6', 'CW', NULL, NULL, 646698.81, 9), 
	('2020-02-07 18:47:10', 1012, 'BR11', 'CD', NULL, NULL, 15645.13, 11), 
	('2024-02-15 02:42:38', 1022, 'BR9', 'CQD', 126238, '2023-08-12 13:37:53', 5366.49, 14), 
	('2021-09-12 22:21:48', 1072, 'BR1', 'CQD', 469360, '2021-03-30 01:00:47', 21980.46, 12), 
	('2023-05-25 12:01:44', 1005, 'BR13', 'CQD', 906705, '2022-12-12 12:38:46', 196965.33, 13), 
	('2019-06-09 04:17:04', 1061, 'BR9', 'CD', NULL, NULL, 624651.92, 10), 
	('2022-01-10 01:17:49', 1091, 'BR7', 'CD', NULL, NULL, 338336.94, 9), 
	('2021-05-23 09:11:03', 1015, 'BR4', 'CD', NULL, NULL, 355687.48, 8), 
	('2023-12-08 10:45:42', 1093, 'BR8', 'CD', NULL, NULL, 638970.41, 12), 
	('2021-03-09 01:45:59', 1030, 'BR10', 'CW', NULL, NULL, 853921.5, 12), 
	('2020-08-22 22:27:54', 1069, 'BR11', 'CD', NULL, NULL, 81133.74, 11), 
	('2022-04-28 09:29:26', 1051, 'BR12', 'CQD', 237732, '2021-12-10 11:19:34', 134983.71, 13), 
	('2022-11-02 00:57:08', 1081, 'BR8', 'CD', NULL, NULL, 569243.52, 13), 
	('2022-12-10 17:03:54', 1033, 'BR9', 'CW', NULL, NULL, 592762.08, 10), 
	('2022-02-24 04:11:01', 1045, 'BR9', 'CD', NULL, NULL, 263868.4, 7), 
	('2022-10-23 11:57:56', 1023, 'BR4', 'CD', NULL, NULL, 880190.16, 11), 
	('2022-06-27 23:07:59', 1076, 'BR5', 'CD', NULL, NULL, 760966.01, 9), 
	('2023-04-09 10:04:23', 1021, 'BR9', 'CD', NULL, NULL, 803174.58, 11), 
	('2023-03-06 14:51:54', 1081, 'BR11', 'CD', NULL, NULL, 718287.98, 15), 
	('2021-09-11 18:52:19', 1090, 'BR11', 'CD', NULL, NULL, 816666.26, 11), 
	('2023-10-03 01:18:16', 1064, 'BR1', 'CW', NULL, NULL, 712841.76, 7), 
	('2021-12-26 06:04:04', 1099, 'BR3', 'CD', NULL, NULL, 115173.38, 14), 
	('2022-08-25 05:33:08', 1078, 'BR10', 'CQD', 11453, '2022-04-13 15:21:55', 922487.75, 12), 
	('2021-11-02 01:53:00', 1075, 'BR3', 'CW', NULL, NULL, 285443.77, 8), 
	('2022-07-09 03:59:56', 1080, 'BR7', 'CW', NULL, NULL, 122395.99, 9), 
	('2022-01-18 09:26:41', 1061, 'BR1', 'CQD', 15332, '2021-09-06 18:31:01', 676582.18, 7), 
	('2022-04-02 08:45:05', 1009, 'BR12', 'CW', NULL, NULL, 171273.4, 11), 
	('2021-01-08 19:34:45', 1011, 'BR12', 'CD', NULL, NULL, 47391.84, 7), 
	('2024-01-15 16:37:02', 1089, 'BR12', 'CD', NULL, NULL, 287385.25, 12), 
	('2024-01-17 11:01:22', 1005, 'BR13', 'CW', NULL, NULL, 248346.94, 9), 
	('2021-07-04 19:11:16', 1098, 'BR2', 'CD', NULL, NULL, 807987.34, 8), 
	('2023-09-03 20:36:28', 1087, 'BR4', 'CQD', 691950, '2023-03-26 06:11:33', 437578, 14), 
	('2019-08-27 00:06:00', 1069, 'BR6', 'CD', NULL, NULL, 825291.57, 11), 
	('2021-02-05 07:05:57', 1029, 'BR13', 'CD', NULL, NULL, 928418.33, 15), 
	('2022-08-05 23:05:21', 1086, 'BR4', 'CW', NULL, NULL, 71732.98, 13), 
	('2023-06-29 12:02:02', 1022, 'BR7', 'CD', NULL, NULL, 876292.77, 8), 
	('2022-08-30 16:57:11', 1090, 'BR1', 'CD', NULL, NULL, 44856.27, 15), 
	('2024-02-14 06:14:21', 1023, 'BR5', 'CQD', 790406, '2023-09-05 19:48:22', 90373.3, 7), 
	('2020-08-18 01:23:25', 1069, 'BR2', 'CD', NULL, NULL, 1613.66, 10), 
	('2023-12-09 12:47:55', 1073, 'BR13', 'CD', NULL, NULL, 270189.75, 10), 
	('2023-03-19 19:31:32', 1060, 'BR11', 'CD', NULL, NULL, 506338.13, 10), 
	('2022-12-06 16:53:09', 1028, 'BR11', 'CD', NULL, NULL, 985531.3, 9), 
	('2021-12-05 18:48:29', 1055, 'BR13', 'CD', NULL, NULL, 717222.01, 11), 
	('2021-03-22 17:29:34', 1015, 'BR12', 'CD', NULL, NULL, 712553.34, 9), 
	('2022-12-07 07:44:00', 1077, 'BR10', 'CW', NULL, NULL, 844339.01, 11), 
	('2023-12-01 21:22:00', 1003, 'BR6', 'CQD', 868638, '2023-07-22 23:23:38', 996762.01, 15), 
	('2019-12-07 08:22:12', 1055, 'BR7', 'CW', NULL, NULL, 950301.78, 14), 
	('2023-12-13 16:08:50', 1066, 'BR7', 'CD', NULL, NULL, 97711.16, 11), 
	('2023-01-13 23:17:41', 1090, 'BR5', 'CD', NULL, NULL, 105508.33, 10), 
	('2021-09-05 03:02:53', 1039, 'BR9', 'CD', NULL, NULL, 164845.11, 7), 
	('2021-02-12 14:59:12', 1097, 'BR12', 'CQD', 749507, '2020-09-16 09:05:09', 933220.7, 11), 
	('2020-12-04 21:03:22', 1018, 'BR5', 'CQD', 46920, '2020-07-26 19:26:46', 827452.77, 14), 
	('2024-02-07 01:09:20', 1047, 'BR12', 'CW', NULL, NULL, 214382.63, 9), 
	('2022-07-28 03:01:26', 1031, 'BR6', 'CW', NULL, NULL, 882560.51, 14), 
	('2023-10-19 04:44:04', 1093, 'BR1', 'CW', NULL, NULL, 94302.52, 11), 
	('2020-07-03 04:38:13', 1035, 'BR7', 'CD', NULL, NULL, 57724.15, 8), 
	('2021-12-15 04:13:02', 1051, 'BR2', 'CW', NULL, NULL, 724454.09, 8), 
	('2022-12-16 02:00:51', 1085, 'BR13', 'CD', NULL, NULL, 803666.72, 12), 
	('2022-06-04 00:27:46', 1088, 'BR7', 'CW', NULL, NULL, 758559.95, 11), 
	('2023-04-11 10:58:22', 1059, 'BR1', 'CQD', 341146, '2022-12-01 23:00:56', 804445.72, 7), 
	('2023-12-18 17:12:38', 1010, 'BR12', 'CQD', 123253, '2023-08-27 20:40:58', 442853.08, 10), 
	('2019-01-07 17:03:21', 1062, 'BR11', 'CD', NULL, NULL, 350823.76, 7), 
	('2024-01-07 18:48:25', 1073, 'BR5', 'CQD', 450166, '2023-08-18 16:26:18', 154169.38, 9), 
	('2023-12-14 17:25:47', 1063, 'BR4', 'CQD', 884070, '2023-08-07 18:20:49', 677804.63, 9), 
	('2022-05-12 22:05:04', 1011, 'BR12', 'CW', NULL, NULL, 7997.56, 15), 
	('2023-07-21 22:09:38', 1064, 'BR6', 'CW', NULL, NULL, 9052.55, 7), 
	('2023-04-23 13:41:42', 1045, 'BR3', 'CD', NULL, NULL, 29077.88, 8), 
	('2023-11-14 23:24:41', 1031, 'BR6', 'CW', NULL, NULL, 777869.92, 11), 
	('2024-01-16 16:30:44', 1071, 'BR11', 'CQD', 864000, '2023-09-19 07:19:48', 801553.42, 11), 
	('2022-03-13 15:04:54', 1060, 'BR8', 'CQD', 133700, '2021-09-27 09:31:23', 274684.07, 13), 
	('2019-12-17 05:54:49', 1055, 'BR11', 'CW', NULL, NULL, 368607.72, 12), 
	('2023-07-02 19:22:33', 1099, 'BR4', 'CD', NULL, NULL, 649487.85, 9), 
	('2024-02-12 18:42:39', 1094, 'BR10', 'CD', NULL, NULL, 542385.58, 10), 
	('2020-04-12 14:13:02', 1019, 'BR4', 'CQD', 496044, '2019-10-18 00:17:22', 48566.02, 14), 
	('2021-12-09 03:53:52', 1092, 'BR2', 'CW', NULL, NULL, 49360.8, 9), 
	('2022-05-25 04:05:52', 1076, 'BR5', 'CD', NULL, NULL, 900608.56, 11), 
	('2024-02-08 11:38:13', 1028, 'BR1', 'CW', NULL, NULL, 8532.16, 10), 
	('2021-01-13 20:37:21', 1037, 'BR3', 'CW', NULL, NULL, 15388.84, 11), 
	('2023-03-06 03:13:20', 1003, 'BR10', 'CW', NULL, NULL, 272836.85, 8), 
	('2022-07-28 14:50:11', 1021, 'BR1', 'CQD', 189888, '2022-02-12 19:25:59', 884971.67, 8), 
	('2023-10-27 10:16:47', 1073, 'BR2', 'CQD', 894640, '2023-06-09 20:16:29', 860756.36, 11), 
	('2023-11-26 19:24:42', 1094, 'BR3', 'CD', NULL, NULL, 582301.27, 10), 
	('2020-07-21 14:38:16', 1047, 'BR2', 'CW', NULL, NULL, 150792.39, 8), 
	('2023-12-20 14:26:02', 1093, 'BR10', 'CD', NULL, NULL, 11430.23, 14), 
	('2021-06-23 05:17:19', 1091, 'BR3', 'CD', NULL, NULL, 59777.41, 15), 
	('2023-11-19 09:09:04', 1038, 'BR6', 'CW', NULL, NULL, 72118.59, 7), 
	('2022-07-08 18:02:39', 1086, 'BR4', 'CQD', 763446, '2022-01-22 12:36:26', 253515.91, 15), 
	('2020-02-16 02:41:06', 1049, 'BR2', 'CQD', 140551, '2019-09-18 09:57:10', 105676.88, 7), 
	('2021-06-15 08:36:55', 1086, 'BR2', 'CW', NULL, NULL, 452.8, 9), 
	('2021-01-05 16:01:05', 1100, 'BR13', 'CW', NULL, NULL, 627525.14, 8), 
	('2021-08-23 19:04:49', 1069, 'BR6', 'CD', NULL, NULL, 198218.24, 7), 
	('2022-04-20 20:44:19', 1063, 'BR7', 'CW', NULL, NULL, 990317.31, 10), 
	('2020-07-11 16:31:58', 1098, 'BR1', 'CQD', 695307, '2020-02-22 22:58:43', 987163.42, 12), 
	('2021-10-05 11:42:20', 1072, 'BR13', 'CW', NULL, NULL, 26367.99, 11), 
	('2022-11-24 15:55:00', 1040, 'BR13', 'CD', NULL, NULL, 615333.7, 8), 
	('2023-03-19 09:24:59', 1046, 'BR9', 'CD', NULL, NULL, 170537.71, 14), 
	('2022-06-19 20:25:23', 1026, 'BR4', 'CD', NULL, NULL, 196229.55, 11), 
	('2024-02-15 19:53:56', 1078, 'BR6', 'CW', NULL, NULL, 704517.7, 10), 
	('2022-06-21 04:57:49', 1088, 'BR9', 'CD', NULL, NULL, 677837.89, 9), 
	('2023-12-24 17:17:06', 1086, 'BR2', 'CD', NULL, NULL, 18187.69, 8), 
	('2023-04-23 12:40:48', 1085, 'BR8', 'CW', NULL, NULL, 514227.75, 10), 
	('2018-12-09 20:51:45', 1056, 'BR7', 'CQD', 178181, '2018-06-11 16:22:02', 30191.8, 8), 
	('2022-02-07 20:17:51', 1080, 'BR7', 'CQD', 389963, '2021-10-04 20:25:12', 38190.95, 7), 
	('2022-02-04 11:26:07', 1090, 'BR5', 'CW', NULL, NULL, 576990.8, 8), 
	('2021-02-11 22:24:15', 1070, 'BR3', 'CQD', 372832, '2020-10-20 08:06:54', 803323.13, 7), 
	('2023-04-22 00:14:42', 1068, 'BR7', 'CD', NULL, NULL, 8907.78, 15), 
	('2023-12-04 02:17:49', 1059, 'BR5', 'CQD', 10903, '2023-06-22 02:12:16', 207792.43, 8), 
	('2024-01-01 12:10:11', 1023, 'BR7', 'CW', NULL, NULL, 17795.52, 7), 
	('2023-03-11 07:38:09', 1060, 'BR3', 'CW', NULL, NULL, 632668.64, 11), 
	('2024-02-15 05:02:31', 1035, 'BR4', 'CW', NULL, NULL, 642640.82, 11), 
	('2022-01-12 01:19:35', 1014, 'BR8', 'CD', NULL, NULL, 3837.62, 13), 
	('2020-01-04 11:24:13', 1046, 'BR2', 'CW', NULL, NULL, 500901.71, 15), 
	('2024-01-05 07:40:34', 1023, 'BR8', 'CD', NULL, NULL, 90315.71, 7), 
	('2021-03-28 20:12:23', 1094, 'BR10', 'CQD', 996298, '2020-09-21 06:09:14', 406202.33, 10), 
	('2022-09-03 22:56:19', 1031, 'BR2', 'CD', NULL, NULL, 113344.06, 15), 
	('2021-01-14 00:19:36', 1056, 'BR7', 'CD', NULL, NULL, 323292.67, 12), 
	('2023-04-16 18:39:33', 1037, 'BR10', 'CW', NULL, NULL, 482928.76, 8), 
	('2023-09-17 20:00:19', 1087, 'BR5', 'CQD', 481005, '2023-05-02 14:08:57', 501888.43, 10), 
	('2021-01-03 20:55:24', 1030, 'BR7', 'CQD', 250951, '2020-08-03 01:40:34', 323719.91, 14), 
	('2022-04-11 02:02:46', 1017, 'BR11', 'CD', NULL, NULL, 373829.66, 12), 
	('2020-11-21 03:38:51', 1077, 'BR1', 'CQD', 428377, '2020-07-04 10:16:57', 380291, 11), 
	('2019-09-10 12:53:17', 1051, 'BR4', 'CD', NULL, NULL, 63780.38, 14), 
	('2023-12-18 06:43:01', 1034, 'BR13', 'CQD', 159695, '2023-06-07 20:53:55', 570323.21, 14), 
	('2021-03-15 23:47:24', 1097, 'BR5', 'CW', NULL, NULL, 652194.1, 13), 
	('2022-11-03 22:22:59', 1094, 'BR11', 'CW', NULL, NULL, 198306.18, 15), 
	('2021-03-15 09:42:37', 1011, 'BR5', 'CD', NULL, NULL, 63636.21, 9), 
	('2022-06-10 04:14:01', 1030, 'BR12', 'CQD', 419879, '2022-02-20 05:15:48', 422155.52, 11), 
	('2021-01-27 04:58:37', 1074, 'BR11', 'CQD', 396446, '2020-08-15 21:29:44', 439909.32, 11), 
	('2022-11-08 12:34:55', 1098, 'BR8', 'CW', NULL, NULL, 324996.63, 13), 
	('2022-03-28 17:28:52', 1080, 'BR7', 'CQD', 791684, '2021-11-07 18:08:11', 925116.14, 15), 
	('2023-11-20 09:10:53', 1006, 'BR3', 'CQD', 579885, '2023-07-13 19:31:10', 78125.79, 10), 
	('2022-01-28 07:59:59', 1036, 'BR6', 'CQD', 855744, '2021-07-18 15:59:36', 994711.09, 9), 
	('2023-11-02 10:14:14', 1024, 'BR8', 'CW', NULL, NULL, 827796.8, 14), 
	('2024-01-01 13:21:18', 1036, 'BR7', 'CD', NULL, NULL, 132777.81, 11), 
	('2021-02-24 14:01:06', 1032, 'BR13', 'CW', NULL, NULL, 693334.62, 10), 
	('2021-01-07 10:32:01', 1029, 'BR2', 'CQD', 348122, '2020-09-29 17:38:44', 688668.18, 8), 
	('2023-12-19 22:08:30', 1010, 'BR1', 'CQD', 372173, '2023-07-20 22:28:08', 12439.48, 14), 
	('2022-04-22 14:01:18', 1029, 'BR13', 'CD', NULL, NULL, 25335.54, 8), 
	('2020-11-29 15:53:07', 1026, 'BR7', 'CD', NULL, NULL, 919581.68, 15), 
	('2023-12-03 04:17:46', 1024, 'BR7', 'CD', NULL, NULL, 476872.12, 14), 
	('2023-08-01 17:47:27', 1099, 'BR1', 'CQD', 936534, '2023-02-28 17:20:14', 425800.15, 13), 
	('2020-12-10 02:29:43', 1067, 'BR2', 'CD', NULL, NULL, 910182.97, 11), 
	('2022-02-24 13:06:45', 1040, 'BR11', 'CW', NULL, NULL, 43821.4, 12), 
	('2021-06-16 17:59:15', 1095, 'BR4', 'CW', NULL, NULL, 238542.64, 13), 
	('2021-11-22 14:27:05', 1048, 'BR11', 'CD', NULL, NULL, 269554.69, 15), 
	('2023-11-23 11:31:46', 1093, 'BR9', 'CD', NULL, NULL, 65171.5, 8), 
	('2023-12-19 05:30:47', 1087, 'BR8', 'CQD', 477811, '2023-07-11 02:07:42', 173693.89, 10), 
	('2021-06-14 09:09:21', 1022, 'BR2', 'CD', NULL, NULL, 498868.38, 13), 
	('2023-09-12 01:47:46', 1059, 'BR8', 'CQD', 861678, '2023-05-28 13:59:59', 884675.27, 10), 
	('2021-12-13 21:04:47', 1078, 'BR13', 'CQD', 718365, '2021-06-24 14:36:04', 898688.94, 15), 
	('2023-02-28 19:15:40', 1087, 'BR10', 'CQD', 178422, '2022-09-25 22:13:35', 32863.15, 7), 
	('2022-01-26 20:01:02', 1061, 'BR5', 'CD', NULL, NULL, 787229.75, 14), 
	('2020-04-27 02:09:07', 1026, 'BR3', 'CW', NULL, NULL, 921951.12, 15), 
	('2023-02-05 18:01:33', 1009, 'BR5', 'CQD', 224509, '2022-10-24 22:59:18', 640487, 12), 
	('2021-08-03 12:08:39', 1097, 'BR5', 'CW', NULL, NULL, 603801.45, 13), 
	('2022-05-01 16:09:51', 1026, 'BR1', 'CQD', 865974, '2021-12-13 23:09:49', 951271.89, 15), 
	('2023-11-18 12:49:05', 1084, 'BR13', 'CQD', 552827, '2023-05-29 03:47:37', 64279.29, 8), 
	('2021-11-23 13:08:21', 1077, 'BR2', 'CQD', 422205, '2021-07-05 21:22:24', 907438.67, 11), 
	('2023-09-12 14:08:37', 1017, 'BR4', 'CD', NULL, NULL, 347512.07, 12), 
	('2022-03-25 10:23:06', 1090, 'BR11', 'CW', NULL, NULL, 57948.74, 13), 
	('2024-02-13 13:09:39', 1004, 'BR11', 'CD', NULL, NULL, 780852.23, 14), 
	('2019-02-06 12:52:09', 1062, 'BR12', 'CD', NULL, NULL, 838650.75, 14), 
	('2023-12-11 10:39:13', 1042, 'BR9', 'CW', NULL, NULL, 641319.1, 13), 
	('2020-12-18 10:53:01', 1031, 'BR1', 'CQD', 888962, '2020-07-20 21:09:26', 612184.93, 15), 
	('2022-04-27 00:07:18', 1026, 'BR10', 'CQD', 624338, '2021-10-03 10:15:30', 593873.61, 7), 
	('2021-12-25 21:06:04', 1096, 'BR13', 'CQD', 633738, '2021-07-18 11:30:48', 637886.56, 8), 
	('2021-05-15 23:24:15', 1002, 'BR10', 'CW', NULL, NULL, 804864.35, 9), 
	('2020-01-18 11:34:56', 1011, 'BR11', 'CQD', 122250, '2019-08-20 09:05:00', 394248.67, 8), 
	('2023-06-17 15:50:46', 1093, 'BR8', 'CQD', 758289, '2023-01-12 12:36:40', 545204.17, 11), 
	('2021-10-03 01:23:36', 1047, 'BR5', 'CD', NULL, NULL, 627262.5, 8), 
	('2021-04-02 03:37:29', 1014, 'BR10', 'CD', NULL, NULL, 14720.76, 14), 
	('2020-12-18 21:26:50', 1078, 'BR9', 'CQD', 462950, '2020-06-05 08:18:01', 75728.28, 11), 
	('2019-09-26 01:22:29', 1038, 'BR9', 'CW', NULL, NULL, 108941.1, 15), 
	('2023-02-17 17:10:20', 1014, 'BR2', 'CQD', 584238, '2022-08-05 07:28:14', 944991.36, 10), 
	('2021-04-14 21:27:21', 1030, 'BR6', 'CQD', 668655, '2020-10-03 16:21:31', 965245, 15), 
	('2021-05-01 06:16:50', 1017, 'BR12', 'CW', NULL, NULL, 393488.68, 14), 
	('2019-06-13 10:00:44', 1095, 'BR2', 'CQD', 507695, '2019-02-27 12:46:07', 812139.97, 13), 
	('2024-01-15 15:03:20', 1008, 'BR7', 'CD', NULL, NULL, 149698.56, 15), 
	('2023-10-17 18:52:00', 1049, 'BR7', 'CQD', 479056, '2023-05-10 06:55:33', 203318.9, 7), 
	('2021-07-02 11:49:52', 1029, 'BR7', 'CQD', 780164, '2021-01-05 22:22:23', 306820.15, 12), 
	('2024-01-19 12:03:04', 1087, 'BR2', 'CW', NULL, NULL, 287572.2, 7), 
	('2024-02-05 00:57:57', 1084, 'BR7', 'CW', NULL, NULL, 30527.08, 15), 
	('2022-01-17 00:42:33', 1034, 'BR13', 'CD', NULL, NULL, 91140.16, 14), 
	('2021-11-11 03:37:31', 1043, 'BR4', 'CD', NULL, NULL, 90570.39, 9), 
	('2021-08-17 06:02:51', 1074, 'BR4', 'CW', NULL, NULL, 69284.12, 14), 
	('2021-06-21 14:28:08', 1049, 'BR11', 'CQD', 515905, '2020-12-19 23:22:33', 55245.11, 15), 
	('2021-07-19 15:40:50', 1046, 'BR7', 'CW', NULL, NULL, 93489.47, 13), 
	('2019-12-26 07:41:37', 1049, 'BR5', 'CQD', 421438, '2019-07-14 15:20:43', 560383.74, 15), 
	('2019-03-23 21:58:21', 1012, 'BR1', 'CW', NULL, NULL, 302484.85, 13), 
	('2020-03-19 01:56:42', 1035, 'BR3', 'CQD', 707225, '2019-09-18 23:12:12', 534652.33, 10), 
	('2020-06-05 19:33:37', 1048, 'BR5', 'CQD', 678257, '2020-02-18 16:04:34', 886654.84, 13), 
	('2020-01-24 13:55:05', 1038, 'BR2', 'CW', NULL, NULL, 37974.69, 15), 
	('2023-06-02 18:28:29', 1054, 'BR5', 'CW', NULL, NULL, 942437.51, 8), 
	('2019-03-14 01:39:44', 1019, 'BR9', 'CW', NULL, NULL, 394106.94, 10), 
	('2024-01-06 08:43:16', 1094, 'BR4', 'CQD', 325306, '2023-09-20 00:19:20', 857375.26, 13), 
	('2022-10-03 06:06:12', 1018, 'BR1', 'CQD', 767881, '2022-05-21 20:43:57', 2851.7, 11), 
	('2024-01-12 22:02:43', 1028, 'BR1', 'CD', NULL, NULL, 568516.41, 15), 
	('2022-08-16 04:30:17', 1066, 'BR8', 'CW', NULL, NULL, 693438.78, 13), 
	('2021-08-21 13:53:12', 1047, 'BR6', 'CW', NULL, NULL, 718328.81, 13), 
	('2019-01-07 12:52:36', 1094, 'BR12', 'CQD', 17058, '2018-08-08 06:00:32', 20516.96, 7), 
	('2024-01-07 15:00:30', 1086, 'BR10', 'CW', NULL, NULL, 715937.17, 12), 
	('2021-02-09 23:23:45', 1080, 'BR3', 'CD', NULL, NULL, 903788.71, 12), 
	('2020-12-20 19:00:20', 1036, 'BR1', 'CD', NULL, NULL, 48916.29, 12), 
	('2019-04-16 09:21:32', 1095, 'BR5', 'CQD', 138851, '2019-03-28 16:57:51', 34859.08, 12), 
	('2019-10-26 18:35:57', 1037, 'BR5', 'CD', NULL, NULL, 988736.93, 11), 
	('2019-11-29 22:37:57', 1025, 'BR11', 'CD', NULL, NULL, 307639.66, 9), 
	('2024-01-28 16:06:57', 1075, 'BR3', 'CW', NULL, NULL, 21353.29, 10), 
	('2024-01-08 02:05:56', 1021, 'BR7', 'CW', NULL, NULL, 131290.42, 15), 
	('2022-07-31 04:33:28', 1065, 'BR7', 'CD', NULL, NULL, 16717.64, 13), 
	('2024-01-17 19:08:22', 1006, 'BR6', 'CQD', 314109, '2023-07-03 22:22:49', 14631.54, 11), 
	('2020-01-14 18:35:50', 1047, 'BR12', 'CW', NULL, NULL, 125813.32, 9), 
	('2020-06-23 12:04:43', 1018, 'BR4', 'CW', NULL, NULL, 528798.5, 10), 
	('2023-08-20 15:35:29', 1007, 'BR2', 'CD', NULL, NULL, 829485.67, 12), 
	('2018-11-25 14:13:06', 1058, 'BR11', 'CQD', 220777, '2018-11-07 13:09:08', 341330.75, 14), 
	('2023-09-13 13:49:44', 1044, 'BR7', 'CW', NULL, NULL, 214122.02, 12), 
	('2022-01-27 18:24:27', 1078, 'BR1', 'CD', NULL, NULL, 719210.46, 13), 
	('2020-01-31 03:31:34', 1057, 'BR11', 'CQD', 575462, '2020-01-19 18:20:00', 867110.28, 11), 
	('2023-02-12 04:23:58', 1079, 'BR5', 'CQD', 999291, '2022-10-26 22:54:45', 294682.47, 11), 
	('2024-01-19 04:29:42', 1014, 'BR10', 'CD', NULL, NULL, 553849.39, 9), 
	('2022-01-14 17:33:06', 1012, 'BR6', 'CQD', 564901, '2021-08-16 20:28:41', 152336.32, 15), 
	('2022-08-15 12:04:46', 1008, 'BR8', 'CW', NULL, NULL, 449733.67, 7), 
	('2022-06-02 11:29:35', 1001, 'BR13', 'CW', NULL, NULL, 921211.52, 7), 
	('2020-01-27 15:44:38', 1065, 'BR2', 'CQD', 558289, '2019-07-04 13:55:15', 683299.01, 15), 
	('2022-01-31 23:33:37', 1074, 'BR6', 'CW', NULL, NULL, 396347.28, 7), 
	('2020-06-25 08:36:45', 1048, 'BR8', 'CD', NULL, NULL, 133463.77, 7), 
	('2021-09-04 22:14:20', 1059, 'BR13', 'CW', NULL, NULL, 489865.52, 10), 
	('2020-06-16 06:33:45', 1035, 'BR5', 'CW', NULL, NULL, 818489.99, 9), 
	('2019-04-22 18:34:34', 1030, 'BR3', 'CQD', 668993, '2018-10-01 14:01:59', 955872.4, 12), 
	('2021-04-05 20:00:01', 1051, 'BR3', 'CQD', 619505, '2020-10-28 04:36:48', 743259.01, 13), 
	('2023-11-27 20:19:31', 1071, 'BR6', 'CD', NULL, NULL, 612140.02, 12), 
	('2018-08-30 02:47:29', 1057, 'BR2', 'CD', NULL, NULL, 108217.42, 14), 
	('2023-12-18 13:06:44', 1080, 'BR4', 'CD', NULL, NULL, 814781.74, 12), 
	('2020-08-16 22:01:23', 1037, 'BR6', 'CW', NULL, NULL, 780921.66, 12), 
	('2021-11-23 04:27:36', 1091, 'BR4', 'CD', NULL, NULL, 24962.04, 10), 
	('2021-09-02 03:26:19', 1026, 'BR9', 'CQD', 198518, '2021-03-02 19:44:31', 638630.96, 10), 
	('2024-02-06 18:50:22', 1005, 'BR11', 'CW', NULL, NULL, 442205.04, 7), 
	('2023-12-11 08:52:25', 1028, 'BR6', 'CD', NULL, NULL, 681199.84, 11), 
	('2021-07-25 00:45:32', 1063, 'BR6', 'CD', NULL, NULL, 320475.88, 8), 
	('2023-09-15 19:42:28', 1033, 'BR8', 'CW', NULL, NULL, 315894.98, 12), 
	('2021-12-22 12:37:30', 1007, 'BR7', 'CQD', 133798, '2021-07-28 19:29:54', 860639.58, 11), 
	('2020-05-13 06:24:48', 1049, 'BR1', 'CD', NULL, NULL, 608470.53, 7), 
	('2021-01-23 20:53:17', 1004, 'BR8', 'CQD', 699054, '2020-09-18 08:18:47', 251474.9, 11), 
	('2024-01-05 15:55:05', 1022, 'BR6', 'CD', NULL, NULL, 797296.43, 8), 
	('2023-09-22 14:06:23', 1059, 'BR2', 'CD', NULL, NULL, 59683.25, 8), 
	('2023-12-18 23:34:45', 1084, 'BR13', 'CQD', 707927, '2023-07-24 05:17:50', 64024.91, 10), 
	('2023-10-26 19:06:03', 1053, 'BR11', 'CW', NULL, NULL, 347463.29, 7), 
	('2021-11-18 03:03:31', 1021, 'BR2', 'CD', NULL, NULL, 459851.76, 8), 
	('2023-02-12 10:30:33', 1010, 'BR10', 'CW', NULL, NULL, 662923.45, 8), 
	('2022-04-23 23:06:25', 1090, 'BR6', 'CQD', 799758, '2021-11-04 09:18:04', 114439.23, 15), 
	('2021-01-06 11:47:40', 1074, 'BR13', 'CQD', 117687, '2020-09-12 19:00:19', 399410.6, 13), 
	('2022-10-04 11:54:29', 1100, 'BR5', 'CD', NULL, NULL, 4960.18, 11), 
	('2023-07-05 23:48:19', 1016, 'BR1', 'CW', NULL, NULL, 319237.55, 7), 
	('2023-11-24 12:52:06', 1060, 'BR6', 'CW', NULL, NULL, 391938.83, 10), 
	('2021-07-12 22:10:04', 1092, 'BR1', 'CD', NULL, NULL, 763962.5, 7), 
	('2024-02-15 16:28:35', 1090, 'BR9', 'CW', NULL, NULL, 795735.79, 12), 
	('2022-06-07 21:12:19', 1055, 'BR6', 'CW', NULL, NULL, 679133.05, 15), 
	('2022-02-24 06:09:23', 1040, 'BR9', 'CQD', 39045, '2021-08-29 09:44:57', 979540.52, 15), 
	('2024-01-01 19:10:19', 1005, 'BR6', 'CW', NULL, NULL, 518491.63, 7), 
	('2024-01-03 19:49:03', 1036, 'BR8', 'CQD', 473340, '2023-08-13 14:56:50', 65936.06, 12), 
	('2021-12-14 22:31:10', 1048, 'BR3', 'CW', NULL, NULL, 9914.06, 10), 
	('2023-11-15 04:26:52', 1088, 'BR10', 'CW', NULL, NULL, 744879.35, 7), 
	('2023-03-27 08:01:26', 1048, 'BR1', 'CW', NULL, NULL, 692210.62, 13), 
	('2022-11-06 22:54:46', 1046, 'BR6', 'CQD', 862133, '2022-05-07 23:24:34', 408393.76, 9), 
	('2022-11-09 03:46:01', 1050, 'BR1', 'CQD', 904544, '2022-06-24 15:04:37', 650714.45, 13), 
	('2023-11-29 12:53:11', 1080, 'BR10', 'CD', NULL, NULL, 402941.6, 14), 
	('2020-12-01 06:54:20', 1072, 'BR13', 'CD', NULL, NULL, 312876.98, 15), 
	('2019-05-03 12:33:23', 1035, 'BR7', 'CD', NULL, NULL, 133923.59, 11), 
	('2023-08-16 20:04:35', 1047, 'BR6', 'CD', NULL, NULL, 210374.81, 15), 
	('2018-03-01 15:29:52', 1020, 'BR9', 'CQD', 449140, '2017-10-22 17:41:02', 301360.23, 7), 
	('2023-10-21 16:12:31', 1092, 'BR5', 'CQD', 351314, '2023-05-12 08:34:26', 997841.08, 11), 
	('2020-07-04 20:46:01', 1047, 'BR7', 'CW', NULL, NULL, 344869.86, 9), 
	('2021-08-20 00:51:51', 1039, 'BR11', 'CW', NULL, NULL, 998744.83, 15), 
	('2021-04-16 16:43:34', 1026, 'BR8', 'CW', NULL, NULL, 325766.86, 8), 
	('2021-08-26 11:12:01', 1075, 'BR10', 'CW', NULL, NULL, 156423.46, 13), 
	('2022-11-07 06:09:54', 1005, 'BR13', 'CD', NULL, NULL, 894961.55, 15), 
	('2020-07-19 13:58:18', 1092, 'BR10', 'CQD', 924184, '2020-03-02 02:00:50', 794712.52, 14), 
	('2022-03-07 16:13:44', 1034, 'BR2', 'CQD', 730318, '2021-10-12 16:31:02', 34713.82, 8), 
	('2021-01-15 14:00:31', 1014, 'BR7', 'CQD', 523345, '2020-07-24 20:13:41', 585815.82, 12), 
	('2023-12-04 04:35:03', 1078, 'BR11', 'CW', NULL, NULL, 840119.93, 14), 
	('2021-04-28 09:38:27', 1078, 'BR7', 'CQD', 498833, '2020-10-05 13:06:51', 8253.27, 11), 
	('2023-12-14 17:58:02', 1076, 'BR13', 'CQD', 802703, '2023-08-11 14:19:49', 89473.82, 7), 
	('2020-09-21 11:53:27', 1061, 'BR11', 'CD', NULL, NULL, 436721.39, 10), 
	('2023-06-01 18:54:18', 1093, 'BR9', 'CD', NULL, NULL, 372720.89, 8), 
	('2021-05-14 11:34:07', 1026, 'BR1', 'CQD', 807792, '2021-01-07 21:32:54', 653363.86, 10), 
	('2023-12-28 09:48:14', 1076, 'BR4', 'CW', NULL, NULL, 890921, 14), 
	('2022-05-26 15:40:40', 1047, 'BR4', 'CD', NULL, NULL, 744468.29, 12), 
	('2023-12-30 00:15:56', 1060, 'BR8', 'CQD', 655459, '2023-07-03 23:26:20', 371365.27, 9), 
	('2022-08-28 09:14:24', 1085, 'BR8', 'CW', NULL, NULL, 351859.47, 15), 
	('2022-03-07 12:21:14', 1021, 'BR9', 'CQD', 190398, '2021-10-28 09:27:01', 84289.15, 8), 
	('2023-12-11 10:45:23', 1074, 'BR6', 'CW', NULL, NULL, 819678.37, 7), 
	('2024-01-01 03:46:53', 1087, 'BR5', 'CW', NULL, NULL, 725834.84, 11), 
	('2023-04-03 22:33:19', 1042, 'BR6', 'CW', NULL, NULL, 17374.84, 13), 
	('2021-08-14 13:13:01', 1086, 'BR3', 'CD', NULL, NULL, 866125.75, 13), 
	('2024-01-05 08:55:57', 1087, 'BR4', 'CD', NULL, NULL, 736400.46, 11), 
	('2024-02-11 20:04:50', 1082, 'BR8', 'CD', NULL, NULL, 483715.69, 7), 
	('2022-05-20 03:29:16', 1016, 'BR10', 'CW', NULL, NULL, 797767.07, 11), 
	('2022-01-10 15:29:59', 1067, 'BR11', 'CD', NULL, NULL, 363351.57, 11), 
	('2023-11-17 12:58:25', 1073, 'BR10', 'CQD', 898040, '2023-05-22 06:57:58', 168309.26, 12), 
	('2020-04-29 09:26:39', 1031, 'BR12', 'CQD', 222547, '2019-11-27 15:23:10', 512603.48, 11), 
	('2020-11-03 20:32:35', 1026, 'BR3', 'CW', NULL, NULL, 350286.67, 10), 
	('2021-12-20 21:52:33', 1005, 'BR12', 'CW', NULL, NULL, 571256.21, 8), 
	('2024-01-15 10:17:29', 1003, 'BR9', 'CQD', 325440, '2023-07-14 08:44:49', 567583.48, 15), 
	('2023-05-09 11:00:56', 1072, 'BR1', 'CD', NULL, NULL, 62627.33, 9), 
	('2023-07-14 08:27:48', 1040, 'BR10', 'CD', NULL, NULL, 606529.91, 15), 
	('2018-07-28 18:07:40', 1020, 'BR12', 'CD', NULL, NULL, 746365.58, 14), 
	('2022-05-05 04:07:30', 1059, 'BR13', 'CD', NULL, NULL, 269291.42, 8), 
	('2023-09-20 01:23:12', 1028, 'BR7', 'CQD', 378839, '2023-05-13 00:58:43', 416185.24, 7), 
	('2021-12-18 01:51:03', 1043, 'BR7', 'CD', NULL, NULL, 994972.23, 9), 
	('2023-12-01 08:42:47', 1088, 'BR8', 'CQD', 655899, '2023-08-19 14:08:58', 999415.79, 13), 
	('2021-10-19 20:43:25', 1011, 'BR4', 'CD', NULL, NULL, 465948.93, 7), 
	('2021-05-05 17:50:15', 1019, 'BR4', 'CW', NULL, NULL, 265106.17, 13), 
	('2021-07-11 14:40:08', 1047, 'BR2', 'CW', NULL, NULL, 519202.11, 7), 
	('2023-12-08 18:17:00', 1083, 'BR11', 'CQD', 634353, '2023-08-21 16:34:51', 52488.21, 12), 
	('2023-12-24 04:09:36', 1027, 'BR5', 'CQD', 886347, '2023-07-10 07:59:23', 521667.99, 11), 
	('2021-08-12 06:16:22', 1074, 'BR7', 'CQD', 326897, '2021-03-25 15:23:15', 476862.7, 15), 
	('2020-11-18 22:51:13', 1014, 'BR3', 'CQD', 486161, '2020-06-23 03:50:32', 463307.64, 14), 
	('2020-05-07 19:42:21', 1058, 'BR11', 'CW', NULL, NULL, 989893.77, 9), 
	('2021-01-21 16:11:32', 1100, 'BR5', 'CQD', 675150, '2020-08-16 00:42:59', 433869.92, 9), 
	('2019-01-04 17:07:05', 1069, 'BR10', 'CQD', 137270, '2018-09-16 15:11:28', 467237.77, 10), 
	('2023-12-04 07:15:12', 1093, 'BR3', 'CQD', 914271, '2023-08-30 20:50:56', 616791.05, 9), 
	('2023-01-09 15:27:21', 1052, 'BR11', 'CD', NULL, NULL, 268446.87, 14), 
	('2022-04-19 00:35:36', 1060, 'BR3', 'CW', NULL, NULL, 857364.74, 15), 
	('2019-08-24 05:41:09', 1011, 'BR6', 'CQD', 352840, '2019-08-07 08:01:15', 7945.52, 13), 
	('2023-02-06 14:50:44', 1021, 'BR5', 'CQD', 971482, '2022-10-22 10:26:33', 405156.42, 8), 
	('2020-05-06 07:31:08', 1052, 'BR1', 'CD', NULL, NULL, 990882.74, 9), 
	('2022-08-19 16:28:39', 1021, 'BR11', 'CQD', 864657, '2022-03-12 08:34:55', 800753.52, 11), 
	('2019-03-25 03:22:59', 1012, 'BR10', 'CW', NULL, NULL, 114618.51, 9), 
	('2021-04-06 00:19:24', 1050, 'BR11', 'CD', NULL, NULL, 218172.66, 8), 
	('2023-12-24 06:52:16', 1073, 'BR3', 'CW', NULL, NULL, 744631.16, 14), 
	('2020-10-06 10:02:56', 1061, 'BR11', 'CQD', 873098, '2020-05-16 11:23:47', 71817.7, 7), 
	('2021-01-07 22:20:08', 1032, 'BR13', 'CW', NULL, NULL, 167898.02, 11), 
	('2020-06-05 02:26:26', 1072, 'BR12', 'CQD', 144055, '2020-02-15 23:48:00', 383949.44, 13), 
	('2020-07-18 22:17:41', 1018, 'BR13', 'CD', NULL, NULL, 641353.78, 7), 
	('2018-10-06 14:22:09', 1096, 'BR3', 'CD', NULL, NULL, 138219.63, 14), 
	('2022-02-04 23:04:04', 1026, 'BR2', 'CD', NULL, NULL, 805967.16, 7), 
	('2023-12-05 19:29:12', 1023, 'BR10', 'CW', NULL, NULL, 909869.65, 15), 
	('2020-07-16 22:13:07', 1013, 'BR4', 'CW', NULL, NULL, 141143.9, 7), 
	('2023-02-15 08:04:12', 1099, 'BR4', 'CW', NULL, NULL, 488183.91, 8), 
	('2024-01-05 06:45:16', 1063, 'BR4', 'CW', NULL, NULL, 302142.14, 11), 
	('2022-06-06 10:13:31', 1069, 'BR8', 'CQD', 220012, '2022-02-04 10:09:29', 36566.17, 9), 
	('2021-12-11 15:27:08', 1048, 'BR7', 'CQD', 982859, '2021-06-28 01:26:08', 805951.87, 12), 
	('2023-07-03 12:39:45', 1042, 'BR12', 'CW', NULL, NULL, 913873.16, 11), 
	('2020-10-13 15:34:08', 1025, 'BR3', 'CQD', 540844, '2020-05-25 18:41:40', 97919.11, 8), 
	('2021-01-24 01:02:43', 1011, 'BR9', 'CQD', 158673, '2020-08-26 20:12:22', 350443, 11), 
	('2024-02-06 16:41:55', 1024, 'BR4', 'CW', NULL, NULL, 558799.1, 14), 
	('2018-05-27 05:40:02', 1020, 'BR13', 'CD', NULL, NULL, 611232.97, 9), 
	('2021-06-03 03:32:36', 1011, 'BR4', 'CQD', 428760, '2021-02-18 10:51:42', 179556.79, 7), 
	('2021-04-26 13:23:00', 1029, 'BR1', 'CQD', 63233, '2020-11-29 15:16:03', 892491.35, 11), 
	('2023-09-16 00:10:31', 1085, 'BR13', 'CQD', 453447, '2023-03-21 14:58:36', 68941.35, 13), 
	('2023-08-04 01:42:48', 1095, 'BR10', 'CW', NULL, NULL, 141416, 7), 
	('2021-03-25 16:27:16', 1098, 'BR4', 'CD', NULL, NULL, 764273.33, 12), 
	('2021-03-17 20:17:50', 1019, 'BR11', 'CD', NULL, NULL, 625260.61, 12), 
	('2022-12-30 16:41:41', 1064, 'BR7', 'CQD', 594821, '2022-06-20 13:20:50', 56465.02, 15), 
	('2023-12-03 01:45:34', 1065, 'BR2', 'CW', NULL, NULL, 361535.97, 8), 
	('2021-03-01 00:30:48', 1049, 'BR7', 'CD', NULL, NULL, 441457.25, 9), 
	('2022-08-02 15:01:54', 1084, 'BR12', 'CW', NULL, NULL, 467612.36, 13), 
	('2023-05-24 01:40:41', 1044, 'BR12', 'CW', NULL, NULL, 283149.8, 9), 
	('2023-12-16 08:14:16', 1075, 'BR12', 'CD', NULL, NULL, 17068.39, 14), 
	('2023-11-24 10:23:51', 1091, 'BR10', 'CW', NULL, NULL, 673385.31, 15), 
	('2022-09-07 15:15:02', 1040, 'BR13', 'CD', NULL, NULL, 518551.44, 8), 
	('2024-01-10 16:18:51', 1088, 'BR1', 'CW', NULL, NULL, 92329.97, 15), 
	('2020-06-24 21:22:40', 1065, 'BR8', 'CW', NULL, NULL, 56692.79, 14), 
	('2023-11-18 12:41:26', 1033, 'BR8', 'CW', NULL, NULL, 210903.19, 10), 
	('2021-07-29 02:47:59', 1046, 'BR13', 'CD', NULL, NULL, 53471.11, 12), 
	('2022-12-01 18:21:11', 1088, 'BR11', 'CD', NULL, NULL, 989421.56, 8), 
	('2023-12-13 07:18:02', 1072, 'BR10', 'CW', NULL, NULL, 707670.09, 7), 
	('2024-01-25 20:18:03', 1032, 'BR3', 'CW', NULL, NULL, 750903.75, 11), 
	('2023-12-26 01:22:16', 1041, 'BR8', 'CD', NULL, NULL, 879609.88, 11), 
	('2023-12-15 05:38:31', 1080, 'BR10', 'CQD', 455742, '2023-06-12 12:10:49', 789128.85, 10), 
	('2022-10-29 20:26:33', 1091, 'BR1', 'CQD', 720157, '2022-04-15 12:22:10', 611213.84, 13), 
	('2023-12-29 03:01:30', 1054, 'BR3', 'CD', NULL, NULL, 302756.89, 14), 
	('2023-06-23 16:00:40', 1055, 'BR13', 'CQD', 11383, '2023-01-13 17:34:38', 587359.81, 9), 
	('2024-01-02 21:42:48', 1003, 'BR7', 'CD', NULL, NULL, 774804.48, 13), 
	('2023-12-11 21:58:47', 1066, 'BR5', 'CD', NULL, NULL, 111686.29, 9), 
	('2019-08-24 09:34:44', 1061, 'BR10', 'CW', NULL, NULL, 39841.18, 8), 
	('2022-07-18 04:39:13', 1026, 'BR10', 'CD', NULL, NULL, 904198.06, 10), 
	('2023-10-17 06:17:16', 1052, 'BR11', 'CD', NULL, NULL, 830608.03, 9), 
	('2024-01-21 21:31:35', 1016, 'BR13', 'CW', NULL, NULL, 668809.74, 11), 
	('2019-02-18 08:20:22', 1011, 'BR1', 'CW', NULL, NULL, 79805.41, 10), 
	('2023-12-14 07:07:13', 1094, 'BR10', 'CD', NULL, NULL, 287559.07, 12), 
	('2020-03-30 09:13:19', 1094, 'BR10', 'CW', NULL, NULL, 971466.45, 13), 
	('2023-05-07 03:38:39', 1021, 'BR5', 'CQD', 358583, '2022-12-18 01:04:20', 745984.2, 15), 
	('2022-11-14 18:38:24', 1030, 'BR1', 'CW', NULL, NULL, 491668.31, 8), 
	('2021-09-15 18:37:03', 1092, 'BR6', 'CQD', 961778, '2021-05-28 10:02:15', 599962.77, 10), 
	('2022-06-12 00:18:52', 1006, 'BR12', 'CW', NULL, NULL, 7451.52, 8), 
	('2023-02-16 08:44:00', 1044, 'BR1', 'CQD', 23119, '2022-08-16 02:57:06', 645907.22, 9), 
	('2021-08-25 16:33:13', 1048, 'BR9', 'CD', NULL, NULL, 868311.19, 11), 
	('2024-02-12 23:35:50', 1044, 'BR4', 'CW', NULL, NULL, 755510.3, 9), 
	('2022-08-07 07:49:13', 1052, 'BR13', 'CD', NULL, NULL, 296378.09, 13), 
	('2022-10-25 14:47:23', 1054, 'BR10', 'CQD', 16588, '2022-06-10 13:06:15', 636168.75, 14), 
	('2022-01-23 18:54:17', 1029, 'BR3', 'CD', NULL, NULL, 937998.86, 13), 
	('2024-01-22 15:36:49', 1077, 'BR10', 'CD', NULL, NULL, 78884.63, 9), 
	('2022-01-31 00:38:51', 1050, 'BR11', 'CD', NULL, NULL, 945479.58, 10), 
	('2019-06-04 16:23:15', 1043, 'BR8', 'CQD', 772783, '2019-02-24 02:01:47', 675526.64, 14), 
	('2020-02-06 13:32:17', 1038, 'BR10', 'CQD', 305560, '2019-08-01 00:21:13', 366740.76, 15), 
	('2021-01-02 22:25:11', 1049, 'BR2', 'CW', NULL, NULL, 772338.38, 15), 
	('2022-02-22 13:19:38', 1079, 'BR1', 'CW', NULL, NULL, 98135.07, 15), 
	('2020-09-21 11:50:37', 1037, 'BR2', 'CD', NULL, NULL, 34835.54, 13), 
	('2020-05-10 03:28:50', 1015, 'BR3', 'CQD', 314721, '2019-11-30 20:19:41', 962107.68, 11), 
	('2023-01-26 19:41:41', 1088, 'BR2', 'CQD', 461659, '2022-08-11 09:05:48', 64751.77, 8), 
	('2022-11-14 00:47:26', 1030, 'BR7', 'CW', NULL, NULL, 64667.05, 13), 
	('2024-01-24 21:00:12', 1002, 'BR1', 'CQD', 839470, '2023-09-01 09:11:59', 93918.44, 14), 
	('2020-06-06 22:50:55', 1014, 'BR13', 'CD', NULL, NULL, 877372.98, 9), 
	('2023-03-12 11:40:30', 1023, 'BR9', 'CD', NULL, NULL, 961428.39, 14), 
	('2022-02-05 18:47:05', 1021, 'BR12', 'CW', NULL, NULL, 103866.92, 12), 
	('2024-02-13 08:04:32', 1093, 'BR11', 'CQD', 917318, '2023-09-17 09:13:18', 265565.96, 7), 
	('2021-04-08 12:37:59', 1015, 'BR13', 'CQD', 56896, '2020-12-30 12:31:55', 278943.64, 10), 
	('2019-11-17 03:32:55', 1051, 'BR10', 'CD', NULL, NULL, 454239.51, 10), 
	('2019-04-10 05:36:34', 1037, 'BR12', 'CD', NULL, NULL, 542868.65, 14), 
	('2023-07-17 06:21:59', 1084, 'BR3', 'CW', NULL, NULL, 70567.62, 8), 
	('2022-03-16 16:54:33', 1021, 'BR1', 'CQD', 39195, '2021-11-05 17:20:06', 325509.8, 15), 
	('2021-11-10 02:56:40', 1022, 'BR8', 'CD', NULL, NULL, 725874.45, 7), 
	('2023-01-31 11:37:03', 1099, 'BR8', 'CW', NULL, NULL, 738514.67, 14), 
	('2022-05-01 14:25:14', 1023, 'BR4', 'CQD', 930700, '2022-01-09 01:31:33', 353974.59, 10), 
	('2023-10-09 00:45:48', 1016, 'BR12', 'CD', NULL, NULL, 940833.8, 14), 
	('2023-10-08 16:24:34', 1007, 'BR1', 'CD', NULL, NULL, 85236.22, 14), 
	('2022-07-03 14:45:31', 1006, 'BR7', 'CQD', 148642, '2022-02-10 17:03:01', 905246.31, 9), 
	('2021-10-10 05:10:29', 1048, 'BR12', 'CQD', 659358, '2021-06-17 05:38:59', 980715, 7), 
	('2021-06-11 23:13:26', 1051, 'BR7', 'CW', NULL, NULL, 574219.21, 8), 
	('2022-02-03 14:53:08', 1073, 'BR8', 'CW', NULL, NULL, 406611.17, 13), 
	('2022-08-02 07:33:28', 1027, 'BR2', 'CD', NULL, NULL, 143430.52, 9), 
	('2021-02-05 13:43:42', 1004, 'BR9', 'CD', NULL, NULL, 982514.21, 12), 
	('2023-11-27 07:23:01', 1057, 'BR13', 'CD', NULL, NULL, 558293.86, 15), 
	('2022-09-10 01:31:36', 1004, 'BR5', 'CD', NULL, NULL, 926268.19, 10), 
	('2024-01-16 16:27:42', 1053, 'BR1', 'CD', NULL, NULL, 924325.59, 9), 
	('2024-01-03 19:06:19', 1016, 'BR6', 'CQD', 491546, '2023-07-14 02:56:48', 430436.86, 13), 
	('2020-07-31 00:53:14', 1037, 'BR2', 'CQD', 338128, '2020-03-07 08:12:03', 78768.76, 13), 
	('2024-02-14 01:02:31', 1059, 'BR9', 'CD', NULL, NULL, 691435.11, 14), 
	('2020-10-16 04:44:40', 1096, 'BR6', 'CQD', 712500, '2020-05-20 14:34:02', 461803.47, 12), 
	('2020-12-22 08:25:05', 1035, 'BR4', 'CW', NULL, NULL, 645815.55, 8), 
	('2020-09-19 17:00:40', 1062, 'BR11', 'CD', NULL, NULL, 757721.12, 7), 
	('2022-01-20 19:08:40', 1075, 'BR5', 'CD', NULL, NULL, 578247.6, 8), 
	('2023-11-23 11:32:18', 1005, 'BR12', 'CQD', 728439, '2023-05-16 05:36:09', 463739.17, 14), 
	('2023-12-01 10:37:10', 1024, 'BR9', 'CQD', 204172, '2023-08-19 00:50:46', 629903.67, 13), 
	('2023-05-09 23:40:26', 1085, 'BR3', 'CD', NULL, NULL, 350472.17, 7), 
	('2019-05-11 04:37:11', 1025, 'BR1', 'CW', NULL, NULL, 187857.56, 7), 
	('2021-11-15 17:12:21', 1075, 'BR9', 'CW', NULL, NULL, 238708.24, 11), 
	('2024-01-13 20:10:52', 1069, 'BR11', 'CD', NULL, NULL, 270365.72, 10), 
	('2023-07-07 12:26:12', 1009, 'BR6', 'CQD', 805555, '2023-01-07 23:30:16', 481682.22, 9), 
	('2021-02-16 16:20:23', 1007, 'BR13', 'CQD', 80403, '2020-08-28 06:55:06', 698342.39, 7), 
	('2023-03-28 15:12:03', 1021, 'BR1', 'CD', NULL, NULL, 966430.1, 9), 
	('2021-01-09 22:09:43', 1059, 'BR9', 'CD', NULL, NULL, 3107.28, 10), 
	('2024-02-08 19:16:20', 1066, 'BR1', 'CQD', 849936, '2023-10-07 16:26:14', 656642.5, 11), 
	('2019-06-08 04:47:30', 1015, 'BR12', 'CW', NULL, NULL, 978915.73, 13), 
	('2019-01-14 03:11:50', 1061, 'BR6', 'CQD', 724020, '2018-09-04 00:10:57', 366131.5, 11), 
	('2021-12-05 20:51:48', 1060, 'BR8', 'CD', NULL, NULL, 197819.29, 9), 
	('2024-01-28 01:36:14', 1023, 'BR6', 'CW', NULL, NULL, 777140.38, 15), 
	('2023-02-10 19:25:49', 1004, 'BR7', 'CW', NULL, NULL, 473707.78, 10), 
	('2023-06-01 05:20:25', 1051, 'BR7', 'CQD', 687891, '2023-01-05 10:49:55', 577720.41, 9), 
	('2023-10-16 21:36:59', 1079, 'BR12', 'CW', NULL, NULL, 319382.53, 7), 
	('2021-01-05 22:54:43', 1039, 'BR2', 'CD', NULL, NULL, 340511.95, 15), 
	('2021-06-25 04:07:04', 1081, 'BR10', 'CW', NULL, NULL, 525712.98, 10), 
	('2021-04-05 12:39:46', 1030, 'BR11', 'CW', NULL, NULL, 8437.79, 13), 
	('2023-12-06 23:59:43', 1072, 'BR10', 'CD', NULL, NULL, 565684.08, 8), 
	('2023-06-18 02:48:23', 1081, 'BR11', 'CW', NULL, NULL, 506248.48, 15), 
	('2023-11-24 22:45:50', 1003, 'BR11', 'CW', NULL, NULL, 542674.99, 15), 
	('2021-11-04 15:56:29', 1075, 'BR7', 'CD', NULL, NULL, 28634.38, 13), 
	('2024-01-17 22:03:26', 1047, 'BR7', 'CQD', 196623, '2023-08-22 15:53:52', 31289.1, 9), 
	('2022-08-07 20:53:35', 1037, 'BR9', 'CD', NULL, NULL, 54187.45, 7), 
	('2021-01-17 06:36:58', 1049, 'BR13', 'CW', NULL, NULL, 97529.76, 7), 
	('2022-11-21 22:45:23', 1077, 'BR7', 'CD', NULL, NULL, 479369.08, 15), 
	('2022-08-15 18:25:23', 1038, 'BR5', 'CW', NULL, NULL, 534398.16, 13), 
	('2020-12-25 00:47:31', 1094, 'BR2', 'CW', NULL, NULL, 732538.84, 10), 
	('2023-12-19 09:12:34', 1026, 'BR9', 'CQD', 835820, '2023-07-05 10:40:11', 142819.86, 8), 
	('2023-07-01 15:15:22', 1030, 'BR8', 'CW', NULL, NULL, 651678.45, 9), 
	('2019-06-01 18:17:46', 1071, 'BR11', 'CQD', 664515, '2018-12-13 05:05:57', 805549.51, 10), 
	('2023-08-17 00:40:16', 1004, 'BR10', 'CQD', 361792, '2023-03-10 21:09:26', 786834.19, 7), 
	('2022-08-14 03:48:52', 1098, 'BR3', 'CW', NULL, NULL, 947348.72, 14), 
	('2023-05-09 04:03:32', 1009, 'BR2', 'CW', NULL, NULL, 87655.44, 11), 
	('2021-06-13 08:23:56', 1055, 'BR8', 'CQD', 204765, '2021-02-18 09:27:30', 496700.62, 11), 
	('2024-01-24 14:37:57', 1027, 'BR4', 'CW', NULL, NULL, 226188.78, 12), 
	('2022-10-27 18:44:20', 1099, 'BR8', 'CW', NULL, NULL, 128150.03, 10), 
	('2023-11-15 07:01:27', 1034, 'BR5', 'CW', NULL, NULL, 594769.98, 12), 
	('2023-01-23 07:12:03', 1005, 'BR9', 'CD', NULL, NULL, 46360.56, 7), 
	('2021-09-25 09:23:15', 1002, 'BR11', 'CW', NULL, NULL, 942319.33, 7), 
	('2019-12-05 08:07:00', 1052, 'BR3', 'CD', NULL, NULL, 735398.18, 12), 
	('2023-12-07 18:37:35', 1024, 'BR13', 'CQD', 4549, '2023-08-21 18:39:12', 544852.1, 13), 
	('2022-12-24 20:48:13', 1085, 'BR4', 'CQD', 998301, '2022-07-28 03:43:06', 358861.69, 10), 
	('2023-03-11 11:26:46', 1002, 'BR11', 'CW', NULL, NULL, 559542.88, 14), 
	('2023-11-18 00:33:39', 1003, 'BR10', 'CQD', 602355, '2023-07-03 23:21:33', 300307.5, 7), 
	('2023-08-18 18:20:10', 1071, 'BR4', 'CW', NULL, NULL, 86632.72, 13), 
	('2023-11-24 05:24:26', 1010, 'BR7', 'CQD', 114372, '2023-07-26 23:02:30', 517875.61, 10), 
	('2023-11-23 07:34:32', 1027, 'BR11', 'CQD', 387577, '2023-06-04 04:24:18', 360279.41, 14), 
	('2024-01-10 10:37:24', 1093, 'BR11', 'CW', NULL, NULL, 47917.3, 10), 
	('2020-10-14 03:02:19', 1082, 'BR2', 'CQD', 606292, '2020-04-04 03:44:28', 190390.99, 10), 
	('2023-02-09 09:30:46', 1022, 'BR3', 'CQD', 357033, '2022-09-15 03:35:09', 707506.9, 10), 
	('2020-08-11 04:03:37', 1035, 'BR8', 'CD', NULL, NULL, 628573.3, 9), 
	('2024-01-27 00:40:44', 1085, 'BR10', 'CQD', 684664, '2023-09-07 03:19:00', 924396.14, 9), 
	('2023-11-28 17:17:05', 1088, 'BR13', 'CQD', 958475, '2023-07-28 03:17:06', 13233.56, 8), 
	('2019-12-16 05:41:13', 1013, 'BR1', 'CQD', 725620, '2019-06-16 07:19:11', 646614.76, 13), 
	('2024-01-09 12:06:02', 1093, 'BR11', 'CW', NULL, NULL, 461689.01, 15), 
	('2023-12-14 23:16:55', 1060, 'BR5', 'CD', NULL, NULL, 873654.6, 10), 
	('2020-10-22 12:34:14', 1048, 'BR5', 'CW', NULL, NULL, 287428.06, 13), 
	('2020-05-10 12:47:18', 1056, 'BR5', 'CW', NULL, NULL, 29646.95, 8), 
	('2024-01-19 23:56:09', 1083, 'BR8', 'CQD', 645668, '2023-07-30 14:22:58', 946367.75, 10), 
	('2019-12-08 10:15:55', 1030, 'BR6', 'CW', NULL, NULL, 700588.16, 14), 
	('2022-02-03 14:43:52', 1031, 'BR13', 'CQD', 650459, '2021-09-12 06:51:49', 368523.91, 13), 
	('2022-09-23 00:49:14', 1060, 'BR3', 'CW', NULL, NULL, 811647.03, 10), 
	('2022-03-14 15:15:21', 1043, 'BR5', 'CW', NULL, NULL, 948953.39, 15), 
	('2021-04-05 09:32:32', 1019, 'BR3', 'CW', NULL, NULL, 877798.91, 8), 
	('2024-02-07 18:54:37', 1009, 'BR2', 'CW', NULL, NULL, 661658.5, 13), 
	('2021-10-24 08:03:51', 1083, 'BR4', 'CD', NULL, NULL, 798620.17, 9), 
	('2019-04-06 09:42:53', 1046, 'BR7', 'CW', NULL, NULL, 796465.49, 9), 
	('2023-12-13 05:39:17', 1076, 'BR13', 'CQD', 261898, '2023-07-06 05:10:03', 809486.57, 9), 
	('2023-02-17 00:30:14', 1040, 'BR6', 'CW', NULL, NULL, 198644.1, 7), 
	('2023-06-21 00:44:36', 1004, 'BR11', 'CD', NULL, NULL, 158624.89, 11), 
	('2022-04-10 15:40:25', 1065, 'BR13', 'CQD', 271013, '2021-12-08 08:04:38', 81393.91, 11), 
	('2022-04-24 17:25:25', 1050, 'BR7', 'CD', NULL, NULL, 308711.55, 13), 
	('2022-06-18 05:41:19', 1047, 'BR13', 'CQD', 565395, '2022-02-23 07:30:15', 503544.64, 13), 
	('2023-12-25 17:49:40', 1033, 'BR9', 'CD', NULL, NULL, 225454.55, 14), 
	('2021-12-13 01:08:58', 1075, 'BR1', 'CQD', 881643, '2021-08-23 04:04:13', 714701.74, 9), 
	('2024-01-06 07:24:46', 1088, 'BR5', 'CD', NULL, NULL, 434908.38, 8), 
	('2019-03-11 20:03:51', 1095, 'BR9', 'CD', NULL, NULL, 115499.84, 14), 
	('2024-01-21 22:37:18', 1021, 'BR5', 'CD', NULL, NULL, 32860.94, 9), 
	('2021-10-01 20:34:22', 1013, 'BR6', 'CQD', 323875, '2021-05-20 06:30:09', 283303.44, 9), 
	('2021-06-16 10:24:37', 1049, 'BR2', 'CQD', 508584, '2020-12-21 08:12:05', 626191.47, 11), 
	('2023-03-23 02:20:18', 1090, 'BR2', 'CQD', 655771, '2022-10-01 22:43:58', 715771.44, 9), 
	('2021-08-21 07:00:33', 1031, 'BR7', 'CW', NULL, NULL, 681559.05, 14), 
	('2022-01-08 09:35:59', 1029, 'BR3', 'CW', NULL, NULL, 713489.47, 9), 
	('2019-09-25 09:03:41', 1018, 'BR2', 'CQD', 804996, '2019-04-09 11:39:11', 807395.59, 13), 
	('2022-10-13 15:10:30', 1049, 'BR12', 'CD', NULL, NULL, 578657.74, 10), 
	('2022-03-22 06:32:26', 1081, 'BR9', 'CW', NULL, NULL, 930944.99, 12), 
	('2022-12-26 14:06:57', 1099, 'BR5', 'CW', NULL, NULL, 884336.69, 7), 
	('2023-12-03 02:49:52', 1066, 'BR2', 'CW', NULL, NULL, 652446.76, 13), 
	('2023-12-05 23:21:16', 1053, 'BR3', 'CQD', 211314, '2023-07-22 04:11:32', 118633.06, 12), 
	('2022-10-16 16:40:30', 1029, 'BR5', 'CW', NULL, NULL, 265187.05, 11), 
	('2022-04-25 22:47:01', 1084, 'BR10', 'CQD', 141706, '2021-11-20 04:28:15', 624352.94, 12), 
	('2024-01-28 22:55:36', 1024, 'BR1', 'CQD', 96400, '2023-08-19 07:03:58', 659775.06, 13), 
	('2020-07-01 21:50:39', 1017, 'BR6', 'CD', NULL, NULL, 51675.72, 8), 
	('2022-08-18 10:48:48', 1080, 'BR4', 'CQD', 196001, '2022-02-25 05:01:44', 20339.11, 11), 
	('2023-04-22 13:30:00', 1086, 'BR8', 'CQD', 373247, '2022-11-15 03:49:43', 60230.43, 13), 
	('2022-12-30 14:18:45', 1033, 'BR7', 'CQD', 447225, '2022-08-15 14:08:49', 485185.6, 9), 
	('2020-06-10 23:17:59', 1069, 'BR9', 'CW', NULL, NULL, 333957.37, 9), 
	('2021-01-28 14:54:02', 1057, 'BR3', 'CQD', 738959, '2020-08-07 03:23:29', 366521.33, 10), 
	('2020-01-02 19:35:57', 1062, 'BR7', 'CD', NULL, NULL, 75355.14, 12), 
	('2022-07-19 13:12:31', 1039, 'BR10', 'CQD', 140295, '2022-01-21 05:55:17', 402777.54, 13), 
	('2023-02-07 11:03:41', 1092, 'BR6', 'CD', NULL, NULL, 860988.83, 13), 
	('2023-08-11 11:04:05', 1030, 'BR10', 'CQD', 287785, '2023-03-27 04:39:34', 792885.97, 7), 
	('2020-11-07 14:39:17', 1075, 'BR8', 'CQD', 473272, '2020-07-20 06:02:24', 963703.47, 8), 
	('2023-11-21 11:05:38', 1006, 'BR13', 'CD', NULL, NULL, 75977.67, 11), 
	('2022-06-09 13:36:41', 1041, 'BR4', 'CW', NULL, NULL, 12153.88, 14), 
	('2022-08-16 05:31:16', 1098, 'BR13', 'CQD', 918988, '2022-02-28 09:46:15', 706905.19, 8), 
	('2023-09-21 02:50:04', 1010, 'BR7', 'CD', NULL, NULL, 70785.44, 14), 
	('2022-10-07 01:59:02', 1011, 'BR12', 'CQD', 125656, '2022-06-22 15:34:23', 305161.41, 12), 
	('2023-03-03 08:23:14', 1083, 'BR8', 'CD', NULL, NULL, 92893.16, 15), 
	('2023-12-20 15:09:39', 1008, 'BR4', 'CQD', 277709, '2023-07-14 10:15:55', 79293.44, 10), 
	('2024-01-19 09:37:43', 1023, 'BR8', 'CW', NULL, NULL, 784809.59, 10), 
	('2022-09-04 16:40:06', 1047, 'BR5', 'CW', NULL, NULL, 632328.15, 10), 
	('2023-12-23 17:10:31', 1088, 'BR9', 'CD', NULL, NULL, 168603.6, 8), 
	('2021-04-07 13:16:27', 1096, 'BR2', 'CQD', 951394, '2020-10-21 20:01:39', 347239.38, 15), 
	('2019-08-07 11:56:04', 1069, 'BR7', 'CD', NULL, NULL, 932266.16, 15), 
	('2022-03-29 00:01:52', 1051, 'BR7', 'CW', NULL, NULL, 640441.57, 7), 
	('2022-05-04 16:35:14', 1045, 'BR7', 'CW', NULL, NULL, 252281.27, 13), 
	('2021-12-02 16:57:01', 1030, 'BR8', 'CQD', 446658, '2021-07-30 08:26:53', 878471.08, 7), 
	('2021-08-28 11:58:41', 1013, 'BR2', 'CD', NULL, NULL, 929312.93, 11), 
	('2021-06-30 19:14:32', 1015, 'BR8', 'CD', NULL, NULL, 882187.87, 13), 
	('2022-12-23 13:37:09', 1059, 'BR13', 'CW', NULL, NULL, 852221.93, 12), 
	('2021-04-21 00:27:34', 1017, 'BR7', 'CD', NULL, NULL, 663104.12, 7), 
	('2022-06-17 14:01:21', 1076, 'BR4', 'CW', NULL, NULL, 43564.06, 8), 
	('2023-03-11 15:41:08', 1016, 'BR3', 'CW', NULL, NULL, 10355.69, 12), 
	('2020-04-02 02:21:50', 1065, 'BR11', 'CW', NULL, NULL, 721265.72, 8), 
	('2022-01-11 05:08:39', 1039, 'BR5', 'CQD', 211064, '2021-08-07 09:44:02', 124711.69, 13), 
	('2021-05-09 10:27:40', 1089, 'BR4', 'CQD', 862940, '2020-12-01 02:13:55', 396143.36, 11), 
	('2023-11-24 11:21:20', 1079, 'BR6', 'CD', NULL, NULL, 66085.09, 15), 
	('2023-01-23 16:01:34', 1028, 'BR11', 'CD', NULL, NULL, 419831.43, 13), 
	('2024-01-02 11:45:36', 1010, 'BR6', 'CD', NULL, NULL, 847501.23, 7), 
	('2022-12-10 02:46:52', 1082, 'BR4', 'CW', NULL, NULL, 534375.56, 13), 
	('2021-09-19 10:26:19', 1051, 'BR1', 'CD', NULL, NULL, 15442.6, 8), 
	('2020-01-04 21:37:27', 1025, 'BR11', 'CW', NULL, NULL, 94868.9, 14), 
	('2022-10-25 15:11:34', 1044, 'BR7', 'CW', NULL, NULL, 24118.72, 7), 
	('2021-03-02 16:20:30', 1055, 'BR5', 'CW', NULL, NULL, 788475.3, 13), 
	('2022-07-14 23:00:08', 1023, 'BR2', 'CW', NULL, NULL, 347277.59, 12), 
	('2021-05-26 02:43:15', 1074, 'BR4', 'CD', NULL, NULL, 741111.05, 12), 
	('2019-05-19 23:48:23', 1058, 'BR12', 'CD', NULL, NULL, 60856.09, 13), 
	('2021-09-11 13:20:31', 1017, 'BR9', 'CD', NULL, NULL, 414989.02, 7), 
	('2022-06-24 04:59:18', 1002, 'BR8', 'CW', NULL, NULL, 123556.02, 7), 
	('2022-03-09 17:18:55', 1066, 'BR6', 'CD', NULL, NULL, 505522.5, 7), 
	('2021-08-29 15:51:27', 1085, 'BR1', 'CD', NULL, NULL, 292480.81, 7), 
	('2020-04-20 10:58:12', 1069, 'BR7', 'CW', NULL, NULL, 605495.81, 10), 
	('2021-06-26 03:14:53', 1092, 'BR12', 'CW', NULL, NULL, 534572.79, 11), 
	('2022-06-09 17:44:11', 1086, 'BR6', 'CD', NULL, NULL, 624662.07, 10), 
	('2024-01-09 13:57:42', 1063, 'BR3', 'CW', NULL, NULL, 395881.92, 15), 
	('2023-08-03 15:33:34', 1022, 'BR6', 'CQD', 844412, '2023-04-06 21:32:54', 210295.2, 15), 
	('2022-07-26 02:31:52', 1015, 'BR8', 'CD', NULL, NULL, 477269.48, 10), 
	('2024-02-03 21:17:36', 1055, 'BR12', 'CW', NULL, NULL, 618695.57, 11), 
	('2023-07-13 10:11:07', 1084, 'BR3', 'CW', NULL, NULL, 80214.19, 9), 
	('2020-07-04 18:43:51', 1039, 'BR9', 'CQD', 186869, '2020-02-15 10:10:40', 276616.1, 11), 
	('2020-02-19 22:24:39', 1096, 'BR8', 'CQD', 414405, '2019-10-29 14:32:00', 857181.17, 10), 
	('2021-05-15 04:19:30', 1086, 'BR6', 'CQD', 452540, '2020-11-17 05:59:43', 622522.79, 14), 
	('2021-11-07 23:57:38', 1013, 'BR13', 'CW', NULL, NULL, 650193, 15), 
	('2024-01-07 05:34:47', 1097, 'BR9', 'CD', NULL, NULL, 796317.25, 14), 
	('2023-07-26 09:48:34', 1010, 'BR13', 'CQD', 913431, '2023-02-12 10:23:21', 23416.34, 10), 
	('2022-03-26 02:57:10', 1080, 'BR13', 'CD', NULL, NULL, 721782.38, 14), 
	('2021-05-10 06:57:21', 1092, 'BR12', 'CQD', 422028, '2020-12-14 02:06:55', 448588.39, 13), 
	('2022-02-06 06:51:52', 1097, 'BR9', 'CQD', 63201, '2021-08-19 12:59:09', 673544.13, 15), 
	('2021-08-22 10:05:19', 1065, 'BR6', 'CD', NULL, NULL, 791511.63, 8), 
	('2021-03-15 07:14:34', 1046, 'BR1', 'CD', NULL, NULL, 52603.93, 12), 
	('2022-10-15 11:14:18', 1032, 'BR9', 'CD', NULL, NULL, 204113.13, 7), 
	('2022-01-14 00:55:03', 1005, 'BR6', 'CW', NULL, NULL, 411748.39, 10), 
	('2019-01-27 06:41:06', 1030, 'BR13', 'CW', NULL, NULL, 152334.49, 11), 
	('2022-03-19 03:36:19', 1014, 'BR1', 'CD', NULL, NULL, 628302.23, 13), 
	('2024-01-15 18:31:19', 1005, 'BR5', 'CW', NULL, NULL, 67256.55, 13), 
	('2024-01-05 11:23:14', 1033, 'BR12', 'CW', NULL, NULL, 647387.62, 9), 
	('2021-08-10 03:58:12', 1075, 'BR5', 'CD', NULL, NULL, 598658.95, 10), 
	('2021-08-05 16:41:58', 1072, 'BR1', 'CW', NULL, NULL, 10194.87, 13), 
	('2022-06-17 06:55:57', 1091, 'BR8', 'CW', NULL, NULL, 762501.42, 13), 
	('2024-01-01 06:09:34', 1024, 'BR8', 'CD', NULL, NULL, 783958.69, 9), 
	('2023-04-19 07:43:53', 1020, 'BR3', 'CW', NULL, NULL, 687645.02, 12), 
	('2023-05-08 18:09:52', 1050, 'BR3', 'CW', NULL, NULL, 412693.1, 15), 
	('2024-02-16 11:10:19', 1078, 'BR8', 'CD', NULL, NULL, 403312.33, 11), 
	('2020-09-19 14:43:27', 1052, 'BR11', 'CW', NULL, NULL, 79731.06, 9), 
	('2023-12-04 18:47:48', 1025, 'BR10', 'CW', NULL, NULL, 747630.83, 11), 
	('2022-07-21 12:30:12', 1051, 'BR6', 'CQD', 580564, '2022-01-13 01:21:00', 4837.33, 14), 
	('2021-08-05 10:58:07', 1069, 'BR13', 'CW', NULL, NULL, 633299.98, 13), 
	('2023-12-22 00:03:24', 1095, 'BR5', 'CD', NULL, NULL, 889681.42, 14), 
	('2022-04-01 10:01:38', 1100, 'BR11', 'CW', NULL, NULL, 577955.1, 13), 
	('2022-01-03 00:29:39', 1013, 'BR4', 'CW', NULL, NULL, 984207.05, 9), 
	('2019-06-08 20:00:10', 1065, 'BR2', 'CW', NULL, NULL, 603119.32, 7), 
	('2023-02-18 17:16:17', 1077, 'BR6', 'CD', NULL, NULL, 212878.92, 7), 
	('2022-01-06 14:08:32', 1004, 'BR1', 'CD', NULL, NULL, 740374.11, 11), 
	('2022-08-04 10:30:29', 1013, 'BR6', 'CD', NULL, NULL, 513480.11, 13), 
	('2022-09-23 16:03:13', 1031, 'BR2', 'CD', NULL, NULL, 235523.62, 7), 
	('2022-12-27 06:16:46', 1058, 'BR8', 'CW', NULL, NULL, 686777.02, 15), 
	('2023-12-07 19:11:57', 1069, 'BR3', 'CW', NULL, NULL, 541977.42, 13), 
	('2024-01-06 23:24:07', 1002, 'BR1', 'CW', NULL, NULL, 403960.97, 8), 
	('2022-12-13 07:38:44', 1076, 'BR10', 'CD', NULL, NULL, 790648.83, 14), 
	('2023-11-25 07:45:05', 1002, 'BR11', 'CW', NULL, NULL, 522257.78, 13), 
	('2022-11-23 06:20:58', 1082, 'BR4', 'CW', NULL, NULL, 288708.06, 9), 
	('2021-02-13 13:06:55', 1017, 'BR13', 'CQD', 405576, '2020-08-04 13:09:27', 723180.88, 14), 
	('2019-08-09 16:56:58', 1013, 'BR4', 'CD', NULL, NULL, 235986.62, 14), 
	('2021-04-09 11:39:45', 1085, 'BR9', 'CQD', 294774, '2020-11-18 14:37:59', 661965.69, 7), 
	('2021-11-22 03:39:37', 1040, 'BR4', 'CW', NULL, NULL, 880929.86, 7), 
	('2023-12-26 20:21:16', 1008, 'BR11', 'CQD', 427117, '2023-08-20 09:58:21', 961.31, 12), 
	('2020-03-06 11:26:01', 1056, 'BR2', 'CQD', 840764, '2019-09-13 10:05:53', 67118.57, 12), 
	('2021-04-25 13:44:51', 1004, 'BR4', 'CD', NULL, NULL, 554654.98, 7), 
	('2023-03-23 05:18:28', 1021, 'BR1', 'CD', NULL, NULL, 498486.41, 15), 
	('2022-05-24 22:26:00', 1039, 'BR4', 'CQD', 410173, '2022-01-27 16:28:54', 586608.49, 11), 
	('2019-04-19 04:26:18', 1069, 'BR8', 'CW', NULL, NULL, 347144.57, 11), 
	('2021-10-25 06:22:06', 1049, 'BR13', 'CW', NULL, NULL, 88061.54, 12), 
	('2021-06-01 19:05:16', 1082, 'BR10', 'CW', NULL, NULL, 384230.91, 12), 
	('2021-01-16 02:10:56', 1020, 'BR7', 'CQD', 707838, '2020-09-12 23:57:36', 28589.6, 9), 
	('2023-06-01 00:23:36', 1023, 'BR10', 'CW', NULL, NULL, 143681.36, 9), 
	('2019-01-09 00:05:07', 1025, 'BR3', 'CD', NULL, NULL, 400284.15, 7), 
	('2022-11-23 21:51:53', 1085, 'BR3', 'CW', NULL, NULL, 372195.46, 9), 
	('2021-10-07 18:25:00', 1015, 'BR4', 'CD', NULL, NULL, 669990.5, 13), 
	('2020-05-16 11:39:22', 1018, 'BR9', 'CD', NULL, NULL, 152699.54, 8), 
	('2023-07-28 14:24:29', 1085, 'BR12', 'CQD', 108074, '2023-01-02 15:02:29', 567650.86, 12), 
	('2021-11-10 01:51:23', 1012, 'BR1', 'CW', NULL, NULL, 111758.38, 11), 
	('2020-03-03 22:55:35', 1047, 'BR3', 'CQD', 240706, '2019-09-12 10:46:31', 41124.04, 10), 
	('2023-12-25 21:37:58', 1008, 'BR4', 'CW', NULL, NULL, 851870.6, 10), 
	('2022-02-14 23:57:26', 1072, 'BR10', 'CQD', 190030, '2021-08-18 06:38:18', 602661.09, 7), 
	('2024-01-25 18:56:01', 1049, 'BR3', 'CQD', 171388, '2023-08-18 04:39:00', 58588.43, 11), 
	('2023-11-20 13:39:00', 1063, 'BR11', 'CW', NULL, NULL, 670392.27, 7), 
	('2023-05-18 11:52:51', 1023, 'BR3', 'CW', NULL, NULL, 815318.49, 12), 
	('2023-05-21 13:30:46', 1092, 'BR1', 'CD', NULL, NULL, 192422.57, 13), 
	('2018-12-25 02:22:08', 1052, 'BR4', 'CQD', 851121, '2018-07-15 21:01:30', 275509.26, 9), 
	('2024-01-01 17:44:01', 1047, 'BR9', 'CD', NULL, NULL, 541748.87, 11), 
	('2021-04-08 11:45:49', 1018, 'BR4', 'CD', NULL, NULL, 63643.87, 9), 
	('2021-05-11 00:01:19', 1055, 'BR3', 'CD', NULL, NULL, 932196.71, 11), 
	('2022-07-02 12:34:13', 1048, 'BR10', 'CD', NULL, NULL, 195344.87, 15), 
	('2020-12-01 07:22:44', 1020, 'BR12', 'CW', NULL, NULL, 705998.08, 11), 
	('2020-03-05 11:11:19', 1012, 'BR8', 'CQD', 694993, '2019-11-02 11:14:22', 449199.56, 10), 
	('2023-09-17 13:40:49', 1012, 'BR2', 'CD', NULL, NULL, 815707.87, 12), 
	('2020-08-07 07:06:39', 1039, 'BR6', 'CQD', 966490, '2020-04-13 21:20:21', 571592.27, 8), 
	('2024-02-16 16:52:21', 1096, 'BR8', 'CW', NULL, NULL, 945741.33, 9), 
	('2023-08-04 21:08:31', 1004, 'BR11', 'CQD', 652209, '2023-02-27 01:22:55', 377576.41, 13), 
	('2024-01-04 17:30:23', 1023, 'BR8', 'CW', NULL, NULL, 987157.02, 8), 
	('2023-02-13 05:06:32', 1048, 'BR3', 'CQD', 115824, '2022-08-17 18:12:09', 726937.3, 14), 
	('2020-06-16 11:15:26', 1082, 'BR3', 'CQD', 36145, '2019-12-29 15:29:47', 93508.74, 8), 
	('2021-11-06 02:11:54', 1041, 'BR6', 'CQD', 954930, '2021-05-04 02:09:34', 571195.83, 13), 
	('2019-07-24 18:26:53', 1055, 'BR12', 'CW', NULL, NULL, 601953.69, 11), 
	('2021-08-04 13:57:43', 1070, 'BR9', 'CD', NULL, NULL, 600408.07, 13), 
	('2022-08-24 10:55:47', 1001, 'BR7', 'CD', NULL, NULL, 857899.79, 7), 
	('2022-01-01 05:10:35', 1089, 'BR13', 'CW', NULL, NULL, 174739.14, 8), 
	('2021-02-25 16:50:19', 1031, 'BR9', 'CQD', 183580, '2020-10-28 10:46:53', 335780.99, 7), 
	('2020-03-15 15:24:00', 1043, 'BR1', 'CD', NULL, NULL, 451985.09, 13), 
	('2023-01-25 14:00:53', 1067, 'BR2', 'CW', NULL, NULL, 960872.93, 15), 
	('2021-10-08 20:19:46', 1097, 'BR3', 'CQD', 483629, '2021-04-10 11:26:08', 935343.42, 12), 
	('2019-08-20 12:56:29', 1039, 'BR9', 'CW', NULL, NULL, 647802.61, 11), 
	('2019-12-07 09:22:37', 1043, 'BR8', 'CW', NULL, NULL, 582270.93, 8), 
	('2023-03-12 13:15:19', 1060, 'BR1', 'CW', NULL, NULL, 545881.15, 10), 
	('2023-12-30 08:21:23', 1028, 'BR8', 'CQD', 537373, '2023-06-28 05:35:13', 502102.55, 13), 
	('2021-09-03 13:37:16', 1098, 'BR10', 'CQD', 606892, '2021-05-12 03:25:14', 55625.45, 13), 
	('2022-04-23 12:48:32', 1073, 'BR2', 'CD', NULL, NULL, 890932.53, 9), 
	('2020-06-19 19:00:18', 1065, 'BR4', 'CD', NULL, NULL, 941884.71, 13), 
	('2024-02-01 06:54:32', 1063, 'BR3', 'CD', NULL, NULL, 24863.47, 11), 
	('2021-12-26 15:40:11', 1014, 'BR4', 'CW', NULL, NULL, 282105.29, 8), 
	('2021-08-29 09:15:03', 1051, 'BR6', 'CQD', 824047, '2021-03-15 22:30:15', 780600.78, 9), 
	('2021-01-18 21:48:16', 1018, 'BR13', 'CW', NULL, NULL, 716297.56, 14), 
	('2021-08-09 14:23:39', 1013, 'BR2', 'CQD', 805432, '2021-02-01 16:05:39', 289525.88, 7), 
	('2020-02-24 18:26:59', 1072, 'BR4', 'CD', NULL, NULL, 119674.2, 11), 
	('2024-01-14 03:04:36', 1048, 'BR9', 'CW', NULL, NULL, 128902.44, 14), 
	('2023-06-10 20:20:28', 1066, 'BR4', 'CQD', 500911, '2023-01-25 02:30:01', 734436.33, 8), 
	('2022-09-10 23:41:59', 1004, 'BR5', 'CD', NULL, NULL, 806634.31, 8), 
	('2021-03-11 08:53:50', 1077, 'BR5', 'CD', NULL, NULL, 31667.06, 15), 
	('2023-07-30 17:38:25', 1009, 'BR9', 'CW', NULL, NULL, 631738.27, 11), 
	('2021-09-23 00:14:47', 1025, 'BR2', 'CW', NULL, NULL, 207453.32, 7), 
	('2021-09-22 04:23:28', 1089, 'BR12', 'CW', NULL, NULL, 830465.74, 8), 
	('2019-05-26 13:12:45', 1043, 'BR11', 'CD', NULL, NULL, 269108.53, 12), 
	('2022-05-10 17:45:21', 1063, 'BR6', 'CQD', 838192, '2022-01-20 12:57:05', 634815.89, 13), 
	('2022-01-10 00:04:11', 1062, 'BR12', 'CW', NULL, NULL, 312147.68, 15), 
	('2020-02-10 03:25:39', 1039, 'BR13', 'CW', NULL, NULL, 824624.52, 9), 
	('2021-06-19 16:25:38', 1034, 'BR5', 'CD', NULL, NULL, 127626.66, 11), 
	('2021-06-03 03:37:22', 1063, 'BR7', 'CD', NULL, NULL, 641250.22, 9), 
	('2020-08-18 23:53:52', 1061, 'BR3', 'CW', NULL, NULL, 550846.05, 10), 
	('2022-11-28 07:18:41', 1099, 'BR10', 'CD', NULL, NULL, 862345.8, 12), 
	('2020-12-05 00:16:45', 1071, 'BR3', 'CD', NULL, NULL, 458630.92, 8), 
	('2022-03-11 21:16:05', 1032, 'BR12', 'CQD', 836313, '2021-10-10 12:01:17', 290105.69, 11), 
	('2021-02-20 15:43:57', 1058, 'BR13', 'CQD', 406216, '2020-08-08 17:47:12', 914229.28, 7), 
	('2018-11-27 12:12:55', 1038, 'BR3', 'CQD', 402734, '2018-07-08 10:25:39', 168496.33, 8), 
	('2023-11-08 09:10:23', 1076, 'BR4', 'CD', NULL, NULL, 584198.45, 11), 
	('2023-08-16 05:40:47', 1091, 'BR12', 'CW', NULL, NULL, 580737.08, 7), 
	('2018-09-24 12:55:23', 1069, 'BR7', 'CQD', 88828, '2018-04-28 01:04:43', 77771.07, 11), 
	('2023-02-12 20:44:05', 1028, 'BR9', 'CW', NULL, NULL, 381516.97, 7), 
	('2020-08-13 01:53:44', 1038, 'BR7', 'CD', NULL, NULL, 589523.99, 9), 
	('2021-01-05 13:08:10', 1089, 'BR13', 'CD', NULL, NULL, 384470.72, 7), 
	('2021-11-18 02:35:50', 1045, 'BR11', 'CQD', 904687, '2021-05-02 06:43:34', 948477.7, 8), 
	('2021-06-11 17:27:44', 1082, 'BR1', 'CW', NULL, NULL, 85267.25, 7), 
	('2023-12-28 20:21:28', 1026, 'BR1', 'CQD', 283560, '2023-06-07 14:30:45', 937142.95, 11), 
	('2022-08-05 18:03:12', 1075, 'BR2', 'CQD', 105614, '2022-03-12 10:48:06', 953779, 15), 
	('2022-03-01 07:54:06', 1080, 'BR4', 'CQD', 301886, '2021-11-09 11:23:33', 805545.53, 10), 
	('2018-03-14 09:58:47', 1020, 'BR9', 'CW', NULL, NULL, 543729.19, 7), 
	('2022-04-23 09:39:44', 1060, 'BR13', 'CQD', 142093, '2021-12-21 19:02:54', 169541.1, 11), 
	('2022-02-20 23:11:57', 1007, 'BR2', 'CQD', 405283, '2021-09-07 01:13:49', 455827.39, 15), 
	('2023-05-15 10:16:33', 1042, 'BR4', 'CW', NULL, NULL, 99178.08, 8), 
	('2023-12-21 02:40:56', 1084, 'BR1', 'CD', NULL, NULL, 969924.45, 9), 
	('2023-05-12 13:55:23', 1088, 'BR1', 'CW', NULL, NULL, 185355.87, 11), 
	('2024-02-04 19:00:38', 1024, 'BR6', 'CD', NULL, NULL, 885634.72, 9), 
	('2020-12-08 01:43:43', 1072, 'BR8', 'CQD', 577704, '2020-06-30 13:32:22', 880371.53, 10), 
	('2020-04-28 08:17:29', 1078, 'BR3', 'CW', NULL, NULL, 22068.37, 10), 
	('2021-05-18 14:52:33', 1038, 'BR13', 'CW', NULL, NULL, 412776.3, 15), 
	('2020-07-23 12:17:11', 1014, 'BR8', 'CD', NULL, NULL, 563777.77, 13), 
	('2019-10-31 19:24:41', 1061, 'BR13', 'CD', NULL, NULL, 129733.38, 8), 
	('2024-01-26 00:55:20', 1083, 'BR2', 'CD', NULL, NULL, 160174.56, 14), 
	('2023-03-21 10:34:33', 1001, 'BR8', 'CW', NULL, NULL, 968765.41, 14), 
	('2023-07-07 02:16:48', 1041, 'BR1', 'CD', NULL, NULL, 193666.07, 11), 
	('2024-01-25 23:40:32', 1074, 'BR2', 'CD', NULL, NULL, 315406.93, 15), 
	('2023-06-02 21:10:57', 1090, 'BR1', 'CW', NULL, NULL, 978623.28, 13), 
	('2023-11-26 13:09:36', 1073, 'BR7', 'CD', NULL, NULL, 193786.19, 9), 
	('2024-01-18 09:58:23', 1023, 'BR8', 'CW', NULL, NULL, 346788.6, 8), 
	('2024-01-22 11:26:25', 1006, 'BR8', 'CD', NULL, NULL, 589659.41, 12), 
	('2019-12-07 00:34:26', 1018, 'BR5', 'CQD', 567241, '2019-07-03 00:41:51', 866141.81, 14), 
	('2023-12-02 16:52:39', 1072, 'BR1', 'CW', NULL, NULL, 391293.23, 14), 
	('2021-08-20 20:33:08', 1100, 'BR12', 'CD', NULL, NULL, 790183.46, 12), 
	('2022-11-30 00:19:52', 1059, 'BR7', 'CW', NULL, NULL, 547863.45, 13), 
	('2022-12-01 20:13:21', 1057, 'BR2', 'CD', NULL, NULL, 186554.42, 8), 
	('2023-12-01 18:09:25', 1010, 'BR9', 'CD', NULL, NULL, 551121.88, 13), 
	('2022-01-27 11:03:33', 1046, 'BR3', 'CD', NULL, NULL, 621604.28, 10), 
	('2022-10-05 22:38:20', 1053, 'BR11', 'CW', NULL, NULL, 759100.63, 12), 
	('2023-12-28 19:25:34', 1053, 'BR1', 'CQD', 791722, '2023-08-16 03:08:11', 108790.73, 7), 
	('2022-08-11 09:56:57', 1067, 'BR1', 'CQD', 733106, '2022-04-09 15:09:32', 653280.25, 7), 
	('2023-11-28 02:30:01', 1099, 'BR3', 'CQD', 354579, '2023-05-03 15:49:11', 388133.61, 8), 
	('2024-01-10 14:22:49', 1044, 'BR13', 'CD', NULL, NULL, 92993.49, 12), 
	('2018-06-12 17:03:35', 1030, 'BR11', 'CD', NULL, NULL, 58315.36, 15), 
	('2021-09-04 22:07:32', 1077, 'BR2', 'CD', NULL, NULL, 868824.7, 9), 
	('2021-12-31 09:36:43', 1085, 'BR6', 'CW', NULL, NULL, 989200.28, 13), 
	('2023-03-12 17:25:50', 1098, 'BR10', 'CQD', 871665, '2022-10-06 18:26:37', 592457.51, 14), 
	('2023-04-09 23:18:42', 1091, 'BR10', 'CQD', 120535, '2022-10-09 20:16:04', 821135, 13), 
	('2023-12-21 11:31:53', 1066, 'BR1', 'CQD', 186734, '2023-06-30 18:12:35', 206128.43, 14), 
	('2022-12-22 00:06:24', 1006, 'BR8', 'CD', NULL, NULL, 895877.28, 9), 
	('2022-05-31 03:35:55', 1099, 'BR1', 'CD', NULL, NULL, 128795.38, 13), 
	('2020-10-01 08:46:06', 1097, 'BR12', 'CQD', 139672, '2020-04-16 06:37:24', 863861.87, 13), 
	('2020-11-22 02:43:37', 1055, 'BR5', 'CW', NULL, NULL, 905329.71, 7), 
	('2023-12-20 06:01:08', 1009, 'BR5', 'CQD', 565752, '2023-07-30 09:58:31', 822154.67, 7), 
	('2020-05-04 08:26:37', 1037, 'BR2', 'CQD', 189119, '2020-04-12 07:01:59', 187721.35, 14), 
	('2020-12-06 11:38:22', 1043, 'BR13', 'CW', NULL, NULL, 767669.69, 9), 
	('2023-06-19 09:18:17', 1016, 'BR2', 'CW', NULL, NULL, 66976.14, 9), 
	('2023-06-01 00:44:06', 1042, 'BR3', 'CW', NULL, NULL, 291573.8, 14), 
	('2022-07-03 17:37:59', 1009, 'BR10', 'CD', NULL, NULL, 661259.22, 14), 
	('2023-12-18 02:35:23', 1021, 'BR9', 'CW', NULL, NULL, 67197.65, 8), 
	('2022-08-19 11:45:54', 1058, 'BR6', 'CQD', 592404, '2022-03-29 06:10:54', 627640.17, 12), 
	('2024-02-06 19:34:35', 1055, 'BR4', 'CW', NULL, NULL, 406769.25, 15), 
	('2021-06-05 23:57:40', 1011, 'BR12', 'CW', NULL, NULL, 591978.25, 13), 
	('2023-02-16 03:55:09', 1009, 'BR6', 'CQD', 688065, '2022-08-07 08:18:07', 159393.48, 11), 
	('2022-01-25 16:42:47', 1059, 'BR3', 'CD', NULL, NULL, 689872.41, 7), 
	('2023-12-31 20:32:40', 1043, 'BR13', 'CD', NULL, NULL, 577684.43, 10), 
	('2022-02-02 10:26:18', 1017, 'BR6', 'CQD', 641276, '2021-10-21 17:44:18', 1571.34, 14), 
	('2022-08-31 08:53:22', 1093, 'BR7', 'CW', NULL, NULL, 704596.22, 9), 
	('2023-04-11 09:57:24', 1083, 'BR11', 'CQD', 318295, '2022-11-07 01:48:57', 817204.26, 10), 
	('2023-03-20 06:35:56', 1024, 'BR12', 'CD', NULL, NULL, 316823.94, 13), 
	('2022-05-25 05:22:47', 1090, 'BR12', 'CW', NULL, NULL, 783512.49, 12), 
	('2021-02-27 06:41:37', 1089, 'BR1', 'CD', NULL, NULL, 269233.24, 12), 
	('2020-02-06 02:55:55', 1011, 'BR5', 'CQD', 865158, '2019-08-02 11:04:45', 434667.94, 7), 
	('2022-11-12 22:31:18', 1084, 'BR3', 'CQD', 338778, '2022-05-24 23:34:07', 524277.37, 14), 
	('2023-07-19 00:41:56', 1040, 'BR3', 'CW', NULL, NULL, 364589.24, 10), 
	('2020-05-26 05:46:08', 1031, 'BR9', 'CW', NULL, NULL, 469364.21, 8), 
	('2023-02-06 00:34:23', 1075, 'BR13', 'CQD', 705257, '2022-10-05 05:16:24', 144125.91, 9), 
	('2022-11-29 11:44:27', 1081, 'BR6', 'CW', NULL, NULL, 337281.45, 13), 
	('2021-05-07 19:06:45', 1026, 'BR10', 'CQD', 617087, '2021-01-09 00:48:59', 784906.49, 12), 
	('2022-07-09 19:20:18', 1007, 'BR13', 'CQD', 902063, '2022-01-17 10:49:25', 896276.29, 7), 
	('2021-07-10 01:49:09', 1081, 'BR1', 'CQD', 157100, '2021-02-06 07:46:27', 987809.08, 14), 
	('2021-03-27 17:57:04', 1091, 'BR13', 'CD', NULL, NULL, 202250.94, 8), 
	('2021-03-14 07:44:58', 1091, 'BR9', 'CW', NULL, NULL, 237924.83, 9), 
	('2023-03-06 16:54:50', 1008, 'BR9', 'CW', NULL, NULL, 715824.36, 12), 
	('2023-08-26 14:50:08', 1064, 'BR13', 'CQD', 595469, '2023-03-19 22:49:32', 280980.75, 15), 
	('2020-09-21 14:09:11', 1061, 'BR13', 'CQD', 616125, '2020-04-07 08:35:16', 578277.18, 10), 
	('2022-10-22 04:58:54', 1023, 'BR10', 'CW', NULL, NULL, 517759.52, 10), 
	('2020-12-20 09:17:05', 1037, 'BR4', 'CQD', 45966, '2020-06-14 13:43:19', 811946.38, 13), 
	('2020-10-17 06:25:01', 1074, 'BR12', 'CW', NULL, NULL, 600123.83, 7), 
	('2021-05-12 11:17:16', 1013, 'BR2', 'CW', NULL, NULL, 10169.02, 15), 
	('2021-05-29 05:56:08', 1032, 'BR4', 'CW', NULL, NULL, 876938.08, 14), 
	('2023-12-13 13:37:30', 1016, 'BR12', 'CQD', 550890, '2023-07-13 23:07:09', 616826.54, 13), 
	('2023-05-18 18:25:02', 1083, 'BR2', 'CQD', 869565, '2023-01-25 06:31:11', 898246.77, 9), 
	('2018-12-29 17:59:01', 1046, 'BR5', 'CW', NULL, NULL, 379613.34, 10), 
	('2023-01-17 10:15:33', 1035, 'BR6', 'CD', NULL, NULL, 736577.29, 9), 
	('2023-03-17 01:42:23', 1064, 'BR8', 'CW', NULL, NULL, 204689.16, 13), 
	('2023-09-01 17:31:54', 1057, 'BR6', 'CW', NULL, NULL, 97904.98, 12), 
	('2023-12-22 20:15:04', 1099, 'BR9', 'CW', NULL, NULL, 562641.16, 8), 
	('2021-07-18 13:58:45', 1036, 'BR8', 'CW', NULL, NULL, 242898.47, 10), 
	('2023-11-28 18:19:14', 1087, 'BR2', 'CQD', 743768, '2023-06-11 21:03:32', 828586.91, 8), 
	('2020-12-11 01:43:41', 1001, 'BR3', 'CQD', 3471, '2020-06-24 15:11:54', 917107.71, 12), 
	('2024-01-28 22:24:47', 1005, 'BR6', 'CQD', 309115, '2023-09-25 20:32:35', 619378.17, 12)
    ```
   ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/d2e7190c-e686-4057-8907-ca9362261603)

### ﻿Data Cleaning or Preparation
In the initial data preparation phase, we performed Data loading and inspection, Handling missing values, Data cleaning and formatting.
1. Find is there have any products other than FD, LA, SB, CA, RD in account_table. If found then replace the same with ‘SB’.
   ```sql
   update account_table
   set prod_id = 'SB'
   where not prod_id in ('CA', 'FD', 'LA', 'RD', 'SB')
   ```
2. Find is there have any customer region other than North, East, South, West in region_table. If found then replace the same with ‘North’.
   ```sql
   update region_table
   set reg_name = 'North'
   where not reg_name in ('East', 'North', 'South', 'West')
   ```
3. Find if any branches other than (BR1, BR3, BR4, BR6) in North region.
   ```sql
   select * from region_table r join branch_table b on r.reg_id = b.reg_id
   where b.reg_id = 1 and br_id not in ('BR1', 'BR3', 'BR4', 'BR6')
   ```
4. Find if any branches other than (BR2, BR7, BR8) in East region.
   ```sql
   select * from region_table r join branch_table b on r.reg_id = b.reg_id
   where b.reg_id = 2 and br_id not in ('BR2', 'BR7', 'BR8')
   ```
5. Find if any branches other than (BR5, BR9, BR11) in South region.
   ```sql
   select * from region_table r join branch_table b on r.reg_id = b.reg_id
   where b.reg_id = 3 and br_id not in ('BR5', 'BR9', 'BR11')
   ```
6. Find if any branches other than (BR10, BR12, BR13) in West region.
   ```sql
   select * from region_table r join branch_table b on r.reg_id = b.reg_id
   where b.reg_id = 4 and br_id not in ('BR10', 'BR12', 'BR13')
   ```
7. Find any account id is less than 1001. If found store the same in ‘wrong_account’ table.
   ```sql
   select * into wrong_account from account_table
   where acc_id < 1001
   ```
8. Find any account status is other than (‘O’ for Operative, ‘I’ for Inoperative, ‘C’ for Closed). If found store the same in ‘wrong_account’ table.
   ```sql
   set identity_insert wrong_account on
   insert into wrong_account (acc_id, cust_name, cust_add, cust_state, cust_zipcode, br_id, prod_id, doo, clr_bal, unclr_bal, status)
   select acc_id, cust_name, cust_add, cust_state, cust_zipcode, br_id, prod_id, doo, clr_bal, unclr_bal, status from account_table
   where status not in ('I', 'O', 'C')
   ```
9. Find the wrong transaction entered where transaction mode is ‘Cash Withdrawal’ or ‘Cash Deposit’ but there have Cheque no. or Cheque Date. If found then store the same in 'wrong_transaction' table.
    ```sql
    select * into wrong_transaction from transaction_table
    where txn_type in ('CW', 'CD') and (chq_no is not null or chq_date is not null)
    ```
10. Find any Cheque number length is less than 6 digits.
    ```sql
    select * from transaction_table where len(chq_no) < 6
    ```
    ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/4b907306-8f55-465c-ba7a-88117c887643)
11. If found then write a query to display required 0 before cheque number and make them a 6 digits long cheque number.
    ```sql
    select *,
    case
    	when len(chq_no) = 5 then concat('0', chq_no)
    	when len(chq_no) = 4 then concat('00', chq_no)
    	when len(chq_no) = 3 then concat('000', chq_no)
    	when len(chq_no) = 2 then concat('0000', chq_no)
    	when len(chq_no) = 1 then concat('00000', chq_no)
    	else chq_no
    end as full_chq_no
    from transaction_table
    ```
    ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/caee96a8-4c22-4ffb-a769-c8ee2b842fcd)
12. Find duplicate values in Account ID.
    ```sql
    with cte as (select acc_id, count(*) cnt from account_table group by acc_id)
    select * from cte where cnt > 1
    ```
13. Find any transaction where transaction date is older than date of opening of account.
    ```sql
    select * from transaction_table t left join account_table a on t.acc_id = a.acc_id
    where dot < doo
    ```
14. If found then add 3 years to the transaction date as instructed by bank.
    ```sql
    update transaction_table
    set dot = dateadd(YY, 3, dot)
    from transaction_table t left join account_table a on t.acc_id = a.acc_id
    where dot < doo
    ```
15. Find cheque deposit discrepency like any cheque date is older than six month of transaction date.
    ```sql
    select *, datediff(DD, chq_date, dot) chq_dt_diff from transaction_table
    where datediff(DD, chq_date, dot) > 180
    ```
    ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/cfc56fff-7f1f-4c50-bfa5-d66754f6d100)
16. Find a Cash Withdrawal happened more than 3 times in a single day.
    ```sql
    select acc_id, format(dot,'yyyy-MM-dd') dates, count(*) tran_nos from transaction_table
    where txn_type = 'CW'
    group by acc_id, format(dot,'yyyy-MM-dd')
    having count(*) > 3
    ```
17. The transaction type 'Cheque Deposit' should not have 'NULL' in the 'Cheque Number' and 'Cheque Date' columns.
    ```sql
    update transaction_table
    set chq_no = null, chq_date = null
    where txn_type in ('CW','CD')
    ```
18. Find any transaction amount is in negative.
    ```sql
    select * from transaction_table where txn_amt < 0
    ```
19. Find any savings account has below the Rs. 1,000/ balance.
    ```sql
    select * from account_table where prod_id = 'SB' and clr_bal < 1000
    ```
20. Update all initial deposits for all account IDs where cash deposits were mistakenly entered as withdrawals.
    ```sql
    with cte as(select *, rank() over(partition by acc_id order by dot) rn from transaction_table)
    update cte
    set txn_type = 'CD'
    where rn = 1 and txn_type = 'CW'
    ```

### Explanatory Data Analysis
- SQL View requirements as required by Bank
  1. The account information includes the Account Number, Name, and Address from the Account table.
     ```sql
     create view vw_acc_info
     as
     select acc_id, cust_name, cust_add, cust_state, cust_zipcode from account_table
     ```
     Calling View
     ```sql
     select * from vw_acc_info
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/6a9e4ef4-0159-48ef-be71-90e0ab8e74af)
  2. The account information includes the account number, name, date of last transaction, and total number of transactions in the account.
     ```sql
     create view vw_acc_last_and_total_txn
     as
     with cte1 as (select acc_id, cust_name from account_table),
     cte2 as (select acc_id, format(dot, 'dd-MM-yyyy') last_txn_date from
     (select acc_id, dot, rank() over(partition by acc_id order by dot desc) rn from transaction_table) as k
     where rn = 1),
     cte3 as (select acc_id, COUNT(*) no_of_txn from transaction_table
     group by acc_id)
     select cte1.acc_id, cte1.cust_name, cte2.last_txn_date, cte3.no_of_txn from cte1
     join cte2 on cte1.acc_id = cte2.acc_id
     join cte3 on cte2.acc_id = cte3.acc_id
     ```
     Calling View
     ```sql
     select * from vw_acc_last_and_total_txn
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/343cb98a-af2c-4b1d-a6b9-29c8d36a7833)
  3. The sum of the uncleared balance can be calculated both branch-wise and product-wise.
     ```sql
     create view vw_br_prod_wise_unclr_bal
     as
     select br_id, prod_id, sum(unclr_bal) total_unclr_bal from account_table
     group by br_id, prod_id
     ```
     Calling View
     ```sql
     select * from vw_br_prod_wise_unclr_bal
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/39cbfbcb-91be-453a-9fff-a408d37023d4)
  4. The transaction type and account wise sum of transaction amount for current month.
     ```sql
     create view vw_txn_type_accid_total_txn
     as
     select txn_type, acc_id, sum(txn_amt) total_txn_amt from transaction_table
     where month(dot) = month(getdate()) and year(dot) = year(getdate())
     group by txn_type, acc_id
     ```
     Calling View
     ```sql
     select * from vw_txn_type_accid_total_txn
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/a76786dc-4ee3-4524-94c3-b88cec5f8d21)
  5. Find number of transactions made by customers in last 6 months.
     ```sql
     create view vw_no_of_txn_last_6_mnth
     as
     (select a.acc_id, a.cust_name, k.no_of_txn from account_table a join
     (select acc_id, count(*) no_of_txn from transaction_table
     where datediff(mm, dot, getdate()) = 6
     group by acc_id) k on a.acc_id = k.acc_id)
     ```
     Calling View
     ```sql
     select * from vw_no_of_txn_last_6_mnth
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/b5443e90-f8bb-4a22-a829-e542ab85f591)
  6. Find branch wise number of customers.
     ```sql
     create view vw_no_of_cust_br_wise
     as
     (select b.br_id, b.br_name, k.no_of_cust from branch_table b join
     (select br_id, count(*) no_of_cust from account_table
     group by br_id) k on b.br_id= k.br_id)
     ```
     Calling View
     ```sql
     select * from vw_no_of_cust_br_wise
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/7863c051-e614-421c-86ab-32ef5fd9d01a)

- SQL Query requirements
  1. Provide a list of transactions that took place in a Branch during the previous month.
     ```sql
     select * from transaction_table
     where year(dot) = year(getdate()) and month(dot) = month(getdate()) - 1
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/7be605aa-51c8-45e1-8ae5-3a396a58bf7c)
  2. Provide the total cash deposits made by each branch during the past 5 days.
     ```sql
     select br_id, SUM(txn_amt) total_amt from transaction_table
     where datediff(DD, dot, GETDATE()) <= 5 and txn_type = 'CD'
     group by br_id
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/73452af8-9c05-4b8c-9a4b-96b6688b003e)
  3. Please provide the total cash withdrawals by branch during the previous month, where the total withdrawals exceeded Rs 10 lakh.
     ```sql
     select br_id, sum(txn_amt) from transaction_table
     where txn_type = 'CW' and datediff(MM, dot, getdate()) = 1
     group by br_id
     having sum(txn_amt) > 1000000
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/b1747e3e-86af-44f6-87df-6b5c0014ce05)
  4. List the names of the account holders and their branch names, along with their maximum and minimum transaction amounts if there have any difference in maximum and minimum amount.
     ```sql
     select a.cust_name, b.br_name, k.max_amt, k.min_amt
     from account_table a join
     (select acc_id, br_id, max(txn_amt) max_amt, min(txn_amt) min_amt from transaction_table
     group by acc_id, br_id) as k on a.acc_id = k.acc_id
     join branch_table b on k.br_id = b.br_id
     where k.max_amt <> k.min_amt
     order by k.acc_id, b.br_name
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/456547b4-3455-492a-9f06-6cf6f48e3059)
  5. List the account holders' names and branch names for the second-highest maximum transaction amount.
     ```sql
     select a.acc_id, a.cust_name, b.br_name, k.txn_amt sec_max_amt
     from account_table a join
     (select acc_id, br_id, txn_amt, RANK() over(partition by acc_id , br_id order by txn_amt desc) max_rn from transaction_table) as k
     on a.acc_id = k.acc_id
     join branch_table b on k.br_id = b.br_id
     where max_rn = 2
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/803fbcfa-f738-4c16-bf67-6e31adf2e56a)
  6. Provide the total transaction amount for the last day of previous month according to the type of transaction and branch.
     ```sql
     select * from transaction_table
     where dot = eomonth(dateadd(mm, -1, getdate()))
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/189dc895-e832-4022-9084-abe79d958851)
  7. Give the names of the account holders who have not made a single cash deposit transaction in the last 15 days.
     ```sql
     select distinct cust_name from transaction_table t
     left join account_table a on a.acc_id = t.acc_id
     where txn_type = 'CD' and DATEDIFF(dd, dot, getdate()) > 15
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/562d75be-29e9-4dde-a57a-d9d6346b7005)
  8. Identify the product that has the most accounts.
     ```sql
     select p.prod_name from product_table p
     join (select prod_id, COUNT(*) cnt from account_table
     group by prod_id
     order by COUNT(*) desc
     offset 0 rows fetch first 1 rows only) as k
     on p.prod_id = k.prod_id
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/106e1ae2-ebe8-4e14-88bd-553a79112355)
  9. List the product with the highest monthly average number of transactions (based on data from the past 6 months).
      ```sql
      select txn_type, mnth, count(*) txn_cnt from
      (select txn_type, dot, format(dot, 'MM-yyyy') mnth, count(*) over(partition by txn_type, datepart(mm, dot)) cnt from transaction_table
      where datediff(DD, dot, getdate()) <= 180) as k
      group by txn_type, mnth
      ```
      ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/a4ac86c4-cdc5-4290-a2d2-244c3e930b9b)
  10. Identify the products that are experiencing a rise in the average number of transactions per month in year 2023.
      ```sql
      select txn_type, year(dot) year, month(dot) mnth, count(*) cnt_txn,
      ((count(*) - lag(count(*)) over(partition by txn_type, year(dot) order by year(dot), month(dot))) * 1.0 / count(*))
      from transaction_table
      where year(dot) = 2023
      group by txn_type, year(dot), month(dot)
      order by 1, 2, 3
      ```
      ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/5fc45b7f-ab6f-4ab7-922e-e4ea7df79600)
  11. Provide the names of the account holders and the number of transactions they carried out on every day of last month.
      ```sql
      select cust_name, cnt as last_mnth_cnt from account_table a join
      (select acc_id, count(*) cnt from transaction_table
      where datediff(mm, dot, getdate()) = 1
      group by acc_id) as k on a.acc_id = k.acc_id
      order by a.acc_id
      ```
      ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/642c4c71-a000-4aab-85b4-3c31e868ba06)
  12. For customers who have made multiple cash withdrawal transactions on the same day, provide the account holder's name, account number, and sum amount. (For this query, consider the transactions made in the last 20 days)
      ```sql
      select a.acc_id, a.cust_name, k.sum_txn from account_table a join
      (select acc_id, day(dot) day, count(*) cnt_no_of_txn, sum(txn_amt) sum_txn from transaction_table
      where txn_type = 'CW' and datediff(dd, dot, getdate()) <= 20
      group by acc_id, day(dot)
      having count(*) > 1) k on a.acc_id = k.acc_id
      ```
      ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/d8c77327-8a4f-4b06-98aa-d06b5b1c7832)
  13. For customers who have made at least one transaction within past 10 days in at least two each transaction type, provide them with their account holder name, account number, and amount.
      ```sql
      select a.cust_name, a.acc_id, k.total_amt from account_table a join
      (select acc_id, sum(txn_amt) total_amt, count(txn_type) cnt from transaction_table
      where DATEDIFF(dd, dot, getdate()) <= 10
      group by acc_id
      having count(txn_type) > 1) as k on a.acc_id = k.acc_id
      ```
      ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/59422e11-3b7c-4abe-a928-181eb2d3f1b6)
  14. Specify how many transactions the manager has authorized 1st week of current month. Remember, above 500000 withdrawal needs manager authorization.
      ```sql
      select count(*) high_val_txn_cnt from transaction_table
      where txn_type = 'CW' and month(dot) = month(getdate()) and year(dot) = year(getdate())
      and (datepart(day,dot)-1)/7 + 1 = 1
      and txn_amt >= 500000
      ```
      ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/ef60cb4b-6fe1-4393-8447-53334f54be1f)
  15. Provide a breakdown of transactions by region and branch for regions with over 10 transactions that occurred in the last month.
      ```sql
      select r.reg_name, b.br_name, cnt as no_of_txn from branch_table b
      join region_table r on b.reg_id = r.reg_id
      join (select br_id, count(0) cnt from transaction_table
      	where datediff(mm, dot, getdate()) = 1
      	group by br_id
      	having count(0) > 10) as k on b.br_id = k.br_id
      ```
      ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/64a350c4-5e2e-45ac-bb1f-5b7f2f291137)
  16. Create a list of accounts that will be considered 'inactive' next month. Remember, these customers failed to make any transactions within the last 365 days.
      ```sql
      select * from
      (select acc_id, max(dot) mx_dt from transaction_table
      group by acc_id) as k
      where datediff(dd, mx_dt, getdate()) >= 365
      ```
      ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/fe3a2842-b73b-4413-a11d-213fd5fa100c)
  17. Identify the customer’s name and account id who entered the most transactions on the last year.
      ```sql
      select a.acc_id, a.cust_name, cnt from account_table a join
      (select acc_id, count(*) cnt from transaction_table
      where year(dot) = (year(getdate()) - 1)
      group by acc_id) k on a.acc_id = k.acc_id
      order by cnt desc
      offset 0 rows fetch first 1 rows only
      ```
      ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/9fd21307-e92c-4c48-ab99-712f1dfe477c)
  18. Identify the day with the highest amount of transactions or cash deposits made at a specific branch in the past month.
      ```sql
      select format(dot, 'dd-MM-yyyy') day, sum(txn_amt) total_amt from transaction_table
      where datediff(mm, dot, getdate()) = 1
      group by format(dot, 'dd-MM-yyyy')
      order by 2 desc
      offset 0 rows fetch first 1 rows only
      ```
      ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/e2e1269b-a687-4a45-afdb-5bb1b031a453)
  19. Provide a list of clients whose cheque books have not been used by them in the past 15 days.
      ```sql
      select * from account_table
      where acc_id in
      (select acc_id from transaction_table
      where txn_type in ('CD', 'CW') and datediff(dd, dot, getdate()) <= 15)
      ```
      ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/ca7b8df0-e950-4b40-90d5-5395cc5a746d)
  20. Indicate the transactions that have taken place in branches that are not the same as the ones that opened the account, but within the same region.
      ```sql
      with cte1 as
      (select t.tran_id, t.acc_id, t.br_id, r.reg_name from transaction_table t
      join branch_table b on t.br_id = b.br_id
      join region_table r on b.reg_id = r.reg_id),
      cte2 as
      (select a.acc_id, a.br_id, r.reg_name from account_table a
      join branch_table b on a.br_id = b.br_id
      join region_table r on b.reg_id = r.reg_id)
      select cte1.tran_id from cte1 join cte2 on cte1.acc_id = cte2.acc_id
      where cte1.br_id <> cte2.br_id and cte1.reg_name = cte2.reg_name
      order by cte1.acc_id
      ```
      ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/9e7661c3-29e5-45e0-ab0b-804d00b20e26)
  21. Indicate the transactions that have occurred in branches that are not the same as the branch that opened the account and the two branches, and the two branches are situated in distinct regions
      ```sql
      with cte1 as
      (select t.tran_id, t.acc_id, t.br_id, r.reg_name from transaction_table t
      join branch_table b on t.br_id = b.br_id
      join region_table r on b.reg_id = r.reg_id),
      cte2 as
      (select a.acc_id, a.br_id, r.reg_name from account_table a
      join branch_table b on a.br_id = b.br_id
      join region_table r on b.reg_id = r.reg_id)
      select cte1.tran_id from cte1 join cte2 on cte1.acc_id = cte2.acc_id
      where cte1.br_id <> cte2.br_id and cte1.reg_name <> cte2.reg_name
      order by cte1.acc_id
      ```
      ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/4d369d3d-a14f-49b5-a643-63e751bd67fa)
  22. Indicate the average amount of each transaction type for the BR7 branch and date 27th Dec 2023.
      ```sql
      select txn_type, sum(txn_amt) from transaction_table
      where br_id = 'BR7' and format(dot, 'yyyy-MM-dd') = '2023-12-13'
      group by txn_type
      ```
      ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/f999f639-9379-47a9-879f-56b80317aeb4)
  23. Provide the following information from the account table:
      1. Number of accounts for each product and month
         ```sql
         select prod_id, year(doo) year, month(doo) month, count(*) no_of_acc from account_table
         group by prod_id, year(doo), month(doo)
         order by 1, 2, 3
         ```
         ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/2f5a3266-d551-4a26-9a4c-c16903c3093c)
      2. The total number of accounts for every product
         ```sql
         select prod_id, count(*) total_acc from account_table
         group by prod_id
         ```
         ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/03783be0-38db-4db1-bc9b-0ec37c571e9a)
      3. Bank's total number of accounts.
         ```sql
         select count(*) total_no_acc from account_table
         ```
         ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/9bdc50e2-97ee-404d-b141-43f8b536b392)

- SQL Tigger requirements
  1. Accounts marked as "Inoperative" or "Closed" should not be used for transactions.
  2. The 'Account Table' should be updated implicitly when an account transaction amount is inserted or updated.
  3. If the account does not have a minimum balance, withdrawals should be prohibited and an appropriate message should be displayed.
  4. The policy prohibits more than three cash withdrawal transactions in a single account on the same day.
  5. The policy prohibits more than three cash deposit transactions in a single account on the same day.
  6. The cash deposit transaction amount exceeding Rs. 500,000/= should be inserted into the 'High Value Transaction' table.
  7. The daily maximum cash withdrawal limit is Rs.100,000/-, and any excess will result in a 1% of debit charge of transaction amount.
     Trigger for 1 to 7
     ```sql
     Create trigger tg_Update_Insert_Delete
     on transaction_table
     after insert, update
     as
     begin
     	declare @acc_id int
     	declare @txn_type varchar(3)
     	declare @txn_amt money
     	declare @dot datetime
     	select @acc_id = acc_id, @dot = dot, @txn_type = txn_type, @txn_amt = txn_amt from inserted

     	declare @status char(1)
     	declare @clr_bal money
     	select @clr_bal = clr_bal, @status = status from account_table where acc_id = @acc_id

     	if @status = 'O'
     		begin
     			if @txn_type = 'CW'
     				begin
     					if @clr_bal >= @txn_amt
     						begin
     							declare @cw_cnt int

     							select @cw_cnt = count(*) from transaction_table
     							where acc_id = @acc_id
     							and format(@dot, 'yyyy-MM-dd') = format(getdate(), 'yyyy-MM-dd')
     							and txn_type = 'CW'

     							if @cw_cnt < 3
     								begin
     									if @txn_amt >= 500000
     										begin
     											set identity_insert high_tran on
     											insert into high_tran (tran_id, dot, acc_id, br_id, txn_type, chq_no, chq_date, txn_amt, staff_id)
     											select tran_id, dot, acc_id, br_id, txn_type, chq_no, chq_date, txn_amt, staff_id from inserted

     											update account_table
     											set clr_bal = clr_bal - @txn_amt - (@txn_amt * 0.01)
     											where acc_id = @acc_id
     										end
     									else
     										begin
     											if @txn_amt >= 100000
     												begin
     													update account_table
     													set clr_bal = clr_bal - @txn_amt - (@txn_amt * 0.01)
     													where acc_id = @acc_id
     												end
     											else
     												begin
     													update account_table
     													set clr_bal = clr_bal - @txn_amt where acc_id = @acc_id
     												end
     										end
     								end
     							else
     								begin
     									print('maximum cash withdrawal limits exceeds, transaction declined')
     									rollback
     								end
     						end
     					else
     						begin
     							print('insufficient funds, transaction declined')
     							rollback
     						end
     				end
     			else
     				begin
     					declare @cd_cqd_cnt int

     					select @cd_cqd_cnt = count(*) from transaction_table
     					where acc_id = @acc_id
     					and format(@dot, 'yyyy-MM-dd') = format(getdate(), 'yyyy-MM-dd')
     					and txn_type in('CD', 'CQD')

     					if @cd_cqd_cnt > 3
     						begin
     							print('maximum cash deposit limits exceeds, transaction declined')
     							rollback
     						end
     					else
     						begin
     							if @txn_amt >= 500000
     								begin
     									update account_table
     									set clr_bal = clr_bal + @txn_amt where acc_id = @acc_id

     									set identity_insert high_tran on
     									insert into high_tran (tran_id, dot, acc_id, br_id, txn_type, chq_no, chq_date, txn_amt, staff_id)
     									select tran_id, dot, acc_id, br_id, txn_type, chq_no, chq_date, txn_amt, staff_id from inserted
     								end
     							else
     								begin
     									update account_table
     									set clr_bal = clr_bal + @txn_amt where acc_id = @acc_id
     								end
     						end
     				end
     		end
     	else
     		begin
     			print('account is inoperative/closed, please contact to bank')
     			rollback
     		end
     end
     ```
  8. The monthly transaction limit should be less than 5, and if it exceeds Rs. 50/= will be debited.
      ```sql
      create trigger tg_monthly_tran
      on transaction_table
      after insert, update
      as
      begin
      	declare @acc_id int
      	declare @txn_type varchar(3)
      	declare @txn_amt money
      	declare @dot datetime
      	select @acc_id = acc_id, @dot = dot, @txn_type = txn_type, @txn_amt = txn_amt from inserted

      	declare @mnth_cnt int
      	select @mnth_cnt =  count(*) from transaction_table
      	where format(@dot, 'yyyy-MM') = format(getdate(), 'yyyy-MM') and @acc_id = acc_id

      	if @mnth_cnt < 5
      		begin
      			print('transaction successful')
      		end
      	else
      		begin
      			update account_table
      			set clr_bal = clr_bal - 50

      			print('INR 50 debited from your bank as monthly 5 transactions exceed')
      		end
      end
      ```
      Ordering Triggers' firing Sequence
     ```sql
     sp_settriggerorder @triggername = 'tg_Update_Insert_Delete', @order = 'first', @stmttype = 'insert', @namespace = null
     sp_settriggerorder @triggername = 'tg_monthly_tran', @order = 'last', @stmttype = 'insert', @namespace = null
     ```

- T-SQL / Stored Procedure requirements
  1. Create a stored procedure to get the customer’s name, cleared and uncleared balance for a given account id.
     ```s
     create proc sp_getaccinfo
     (
     	@acc_id int
     )
     as
     begin
     	select clr_bal, unclr_bal from account_table where acc_id = @acc_id
     end
     ```
     Calling SP
     ```sql
     select * from vw_acc_info
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/0debdfc8-87ae-441f-97c2-c6550ec31107)
  2. Create a stored process to get all the account details of the account id and the transaction for the desired month and year, as well as number of transactions the customer has made in different transaction types. If the account id or year or month is incorrect, print the statement ‘Please enter correct account id’, ‘Please enter correct year’ and ‘Please enter correct month’ respectively.
     ```sql
     create proc sp_accdetail_txndetail_nooftxntype
     (
     	@acc_id int,
     	@year int,
     	@month varchar(3)
     )
     as
     begin
     	if not exists(select * from transaction_table where acc_id = @acc_id)
     		begin
     			print 'please enter the correct account id'
     		end
     	else
     		if not exists(select * from transaction_table where acc_id = @acc_id and year(dot) = @year)
     			begin
     				print 'please enter the correct year'
     			end
     		else
     			if exists(select * from transaction_table where acc_id = @acc_id and year(dot) = @year and format(dot, 'MMM') = @month)
     				begin
     					select * from account_table where acc_id = @acc_id

     					select * from transaction_table
     					where acc_id = @acc_id and year(dot) = @year and format(dot, 'MMM') = @month

     					select txn_type, count(*) no_of_txn from transaction_table
     					where acc_id = @acc_id and year(dot) = @year and format(dot, 'MMM') = @month
     					group by txn_type
     				end
     			else
     				print 'please enter the correct month'
     end
     ```
     Calling SP
     ```sql
     exec sp_accdetail_txndetail_nooftxntype 1010, 2023, 'dec'
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/fa27c1fd-de80-4eac-98b2-d0327b481949)
  3. Create a stored procedure to print the below mentioned bank statement
     ![stored_procedure_requirements](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/5e3136ea-c67f-4805-85fe-3d70a5d58016)
     ```sql
     create proc [dbo].[sp_get_bank_statement]
     (
     	@acc_id int,
     	@fromdate datetime,
     	@todate datetime = null -- optional input parameter
     )
     as
     begin
     	if @todate is null
     	set @todate = getdate() -- set value for optional input parameter

     	declare	@cust_name varchar(30)
     	declare	@br_id varchar(4)
      declare	@br_name varchar(20)
     	declare	@prod_id char(2)
     	declare	@clr_bal money
     	declare	@unclr_bal money
     	print('------------------------------------------------------------------------------------')
     	print('                                     The Bank                                       ')
     	print('List of transactions from '+cast(@fromdate as varchar)+' to '+cast(@todate as varchar))
     	-- fetch cutomer information
     	select
     	@cust_name = a.cust_name,
     	@br_id = a.br_id,
     	@br_name = b.br_name,
     	@prod_id = a.prod_id,
     	@clr_bal = a.clr_bal,
     	@unclr_bal = a.unclr_bal
     	from account_table a join branch_table b on a.br_id = b.br_id
     	where acc_id = @acc_id
     	-- print first few headers
     	print('------------------------------------------------------------------------------------')
     	print('     Account No: ') + cast(@acc_id as varchar)
     	print('A/C Holder Name: ') + @cust_name
     	print('    Branch Name: ') + @br_name
     	print('     Product ID: ') + @prod_id
     	print('  Clear Balance: INR ') + cast(@clr_bal as varchar)
     	print('------------------------------------------------------------------------------------')
     	print('Sl No	Date					Txn Type	Cheque No		Amount			Running Balance')
     	print('------------------------------------------------------------------------------------')
     	-- fetch transaction info and put into a temp table for looping the same
     	select row_number() over(order by dot asc) rn, dot, txn_type, chq_no, txn_amt,
     			sum(case
     					when txn_type in ('CW') then (txn_amt * -1)
     					else txn_amt
     					end) over(order by dot) running_bal into #temp
     	from transaction_table where acc_id = @acc_id and dot between @fromdate and @todate
     	-- initialize loop
     	declare @x int = 1

     	declare @sl_no int
     	declare @dot datetime
     	declare @txn_type varchar(3)
     	declare @chq_no varchar(6)
     	declare @txn_amt money
     	declare @running_balance money
     	--looping starts
     	while @x <= (select count(*) from #temp)
     	begin
     		select @sl_no = rn, @dot = dot, @txn_type = txn_type,
     		@chq_no = chq_no, @txn_amt = txn_amt, @running_balance = running_bal
     		from #temp where rn = @x
     		-- print data
     		print(cast(@sl_no as varchar)+space(7)+cast(@dot as varchar)+space(5)+@txn_type+space(10)
     		+isnull(@chq_no, 'na')+space(14)+cast(@txn_amt as varchar)+space(8)+cast(@running_balance as varchar))
     		-- increment for looping
     		set @x = @x + 1
     	end
     	-- print rest data
     	print('')
     	declare @total_txn int = (select count(*) from #temp)
     	print('     Total Number of Transaction: ')+cast(@total_txn as varchar)
     	declare @cd int = (select count(*) from #temp where txn_type = 'CD')
     	print('      Total Number Cash Deposits: ')+cast(@cd as varchar)
     	declare @cqd int = (select count(*) from #temp where txn_type = 'CQD')
     	print(' Total Number of Cheque Deposits: ')+cast(@cqd as varchar)
     	declare @cw int = (select count(*) from #temp where txn_type = 'CW')
     	print('Total Number of Cash Withdrawals: ')+cast(@cw as varchar)
     	declare @below_1000 int = (select count(*) from #temp where running_bal < 1000)
     	print('Dates when the product''s minimum balance was not met: ')+cast(@below_1000 as varchar)
     	declare @rb money = (select running_bal from #temp where rn = (select max(rn) from #temp))
     	print('                 Closing Balance: ')+cast(@rb as varchar)
     	print('------------------------------------------------------------------------------------')
     	print('                          Thank You for Connecting Us'                               )
     end
     ```
     Calling SP
     ```sql
     exec sp_get_bank_statement 1005, '2020-01-01', '2024-12-31'
     ```
     ![image](https://github.com/sumanndass/Bank-Operations-Analysis/assets/156992689/d094210f-fe9d-4a67-be3e-efebe3106f5b)
