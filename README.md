# mySQLscripts


-- create a new database
drop database vinaydb
create database vinaydb

-- activate your database
use vinaydb
go

create table #MyFirstTable 
(
	CustID int Primary Key, 
	FirstName Varchar(50), 
	LastName Varchar(50),
	Contact int
)

Select * from #MyFirstTable

Insert into #MyFirstTable Values
(4,'Vinay','Aneja',98766846)


drop table MyFirstTable

Use AdventureWorks2012

Select DepartmentID, count(*) as TotalByDept from [HumanResources].[EmployeeDepartmentHistory]
group by DepartmentID
order by TotalByDept

Select * from [HumanResources].[Department]
-- convert Date to varchar
Select (Name + ' ' + GroupName + ' ' + Convert(Varchar(50), [ModifiedDate]))  as Everything, *
From [HumanResources].[Department]

Select
Convert(smalldatetime, Convert(varchar(50),Year(GETDATE())) + '-' + 
Convert(varchar(50),Month([ModifiedDate])) + '-' + 
Convert(varchar(50),Day([ModifiedDate]))) as Birthday, *
From [HumanResources].[Department]

Create table #Sample
(
SNo int,
Name Varchar(50),
Groupname Varchar(50),
Datetype smalldatetime
)

Select * from #Sample

Insert into #Sample (Sno, Name, Groupname, Datetype)
 Select * from [HumanResources].[Department]

 Select * from #Sample
 -- Indexing

 Create Index MyFirstIndex
 on [HumanResources].[Employee] (NationalIDNumber)

  Create Index MySecondIndex
 on [HumanResources].[Employee] (BirthDate)
 
 Select *  From [HumanResources].[Employee]
 where BirthDate < '1963-01-01'
 order by BirthDate
 
 -- declaring variables

 declare @x int

 set @x = 100

 print @x

declare @y int

set @y = (select count(currentflag) from [HumanResources].[Employee])

Print @y

-- sub query - when there is a select query within a select statement
-- sub query return only a single column


select(Select sum(SalesQuota) from [Sales].[SalesPerson]) as [Total Sales],* 
from [Sales].[SalesPerson]

Select (Select count(BusinessEntityID) from [Sales].[SalesPerson] SP
Where SP.TerritoryID = S.TerritoryID) as Compats, * from [Sales].[SalesPerson] S
order by TerritoryID

select * from  [Sales].[SalesPerson]
where BusinessEntityID in 
(Select BusinessEntityID From [Sales].[SalesPerson] where TerritoryID is not NULL)
---group by TerritoryID
order by TerritoryID

Select * from [Sales].[SalesPerson]
order by TerritoryID

Select S.TerritoryID, Q.TerritoryID, S.BusinessEntityID,*
From [Sales].[SalesPerson] S
inner join
[Sales].[SalesTerritory] Q
on S.TerritoryID = Q.TerritoryID
order by S.TerritoryID

select * from [Sales].[SalesTerritory]
order by TerritoryID

-- Udemy working data will be used from here ---- 
use Terra
Go

select * from [dbo].[tblContacts]

-- using correlated subqueries

select * from [dbo].[tblContacts]
select * from [dbo].[tblContracts]
select * from [dbo].[tblPayments] where paymentContract =9
order by paymentContract
select *, (select count(paymentid) from tblPayments where paymentContract = contractid) as payments
from [dbo].[tblContracts]
order by contractID

select *, 
(select sum(contractvalue) from tblContracts C where C.contractClient = Co.contractClient)
From tblContracts Co

select *, 
(select sum(contractvalue) from tblContracts C where Year(C.contractSigndate) = Year(Co.contractSigndate))
From tblContracts Co
order by contractSigndate

-- using the EXISTS keyword

select * from tblcontracts 
where exists
(
select * from tblcontacts  where region='teesside' 
)

select * from tblcontracts c1
where exists
(
select * from tblcontacts c2 where region='teesside' and c1.contractclient=c2.contactid
)
order by contractClient, contractID



select * from tblcontracts c1
where contractclient in
(select contactid from tblcontacts c2 where region='teesside' )




-- finding duplicates
select * from tblcontacts
select fn+sn, count(*) from tblcontacts
group by fn+sn
having count(*) > 1

select * from tblcontacts
where fn+sn in(select fn+sn from tblcontacts
group by fn+sn
having count(*) > 1)

select * from tblcontacts A
where exists ( select B.contactid from tblcontacts B
	where B.fn=A.fn
	and B.sn=A.sn
	and B.contactid < A.contactid)

	--Find Duplicates

select contractID, count(*) from tblcontracts
group by contractID
having count(*) >1

-- summary statistics

select * from tblContracts

select contractClient, count(*) as Counts, sum([contractValue]) as Total, max(contractValue) as Maxm, min(contractValue) as Minm, avg(contractValue)  as Mean
From [dbo].[tblContracts]
group by contractClient
order by contractClient
---
	-- case statement

Select * from [dbo].[tblContacts]

	select
	case Gender
	when 1 then 'Male'
	when 2 then 'Female'
	else
	'Undefined'
	end
	
	as GENDER,

	Case Region
	when 'teesside' then 'UK'
	when 'north yorkshire' then 'UK'
	when 'tyne and wear' then 'UK'
	else
	'USA'
	End as COUNTRY,*
	
	from [dbo].[tblContacts]

	-- case statement style 2

	select
	case 
	when region = 'Teesside' then 'USA'
	when right(convert(varchar(200),email),4) = '.com' then 'USA'		
	else
	'RestOfWorld'
	end
	as COUNTRY,*
	
	from [dbo].[tblContacts]


	select 
case
when contractsigndate between '2014-04-01' and '2015-03-31' then '2014/15'
when contractsigndate between '2013-04-01' and '2014-03-31' then '2013/14'
when contractsigndate between '2012-04-01' and '2013-03-31' then '2012/13'
else
'Older Contract'
END as ContractYear
,* from tblcontracts

	-- Multiple Criteria within the case statement

select (select count(*) from tblcontracts b where b.contractclient=a.contractclient) as Counts,
case
when contractsigndate between '2014-04-01' and '2015-03-31' then '2014/15'
when contractsigndate between '2013-04-01' and '2014-03-31' then '2013/14'
else
'Older Contract'
end as ContractYear
,
case 
when (select count(*) from tblcontracts b where b.contractclient=a.contractclient) >10
	then 'Large'
	else 'Small'
	end as 'Client Size'
,*
from tblcontracts a
order by contractClient
	
-- using case statement in where, group by and order by statements
select
case 
when gender=1 and maritalstatus=1 then 'Unmarried Male'
when gender=2 and maritalstatus=2 then 'Married Female'
when gender=1 and maritalstatus=2 then 'Married Male'
else 'Undefined' end as Maritalstatus, count(*) as Counts
from tblcontacts
where (case 
when gender=1 and maritalstatus=1 then 'unmarried male'
when gender=2 and maritalstatus=2 then 'married female'
when gender=1 and maritalstatus=2 then 'married male'
else 'undefined' end) = 'undefined'
group by 
(case 
when gender=1 and maritalstatus=1 then 'Unmarried Male'
when gender=2 and maritalstatus=2 then 'Married Female'
when gender=1 and maritalstatus=2 then 'Married Male'
else 'Undefined' end)
-----------------------


select * from #deals

select 
case
when contractsigndate between '2014-04-01' and '2015-03-31' then '2014/15'
when contractsigndate between '2013-04-01' and '2014-03-31' then '2013/14'
when contractsigndate between '2012-04-01' and '2013-03-31' then '2012/13'
else
'Pre 2012' 
END
as Years
,sum(contractvalue) as Totals from tblcontracts
group by 
(case
when contractsigndate between '2014-04-01' and '2015-03-31' then '2014/15'
when contractsigndate between '2013-04-01' and '2014-03-31' then '2013/14'
when contractsigndate between '2012-04-01' and '2013-03-31' then '2012/13'
else
'Pre 2012'
END)

----------------------
select 
(case
when weekstartdate between '2014-04-01' and '2015-03-31' then '2014/15'
when weekstartdate between '2013-04-01' and '2014-03-31' then '2013/14'
when weekstartdate between '2012-04-01' and '2013-03-31' then '2012/13'
else
'Pre 2012'
END) as Years
,count(weekid) as Total_weekid from tblweeks
group by 
(case
when weekstartdate between '2014-04-01' and '2015-03-31' then '2014/15'
when weekstartdate between '2013-04-01' and '2014-03-31' then '2013/14'
when weekstartdate between '2012-04-01' and '2013-03-31' then '2012/13'
else
'Pre 2012'
END)

-- use case statement in insert and update statements

create table #DEALS
(dealyear varchar(10), dealSales float, dealWeeks int)

select * from #deals

insert into #deals(dealyear,dealsales,dealweeks)
select v.conyear,v.convalue,w.conweeks from 
(
select 
case
when contractsigndate between '2014-04-01' and '2015-03-31' then '2014/15'
when contractsigndate between '2013-04-01' and '2014-03-31' then '2013/14'
when contractsigndate between '2012-04-01' and '2013-03-31' then '2012/13'
else
'Pre 2012'
END as ConYear
,sum(contractvalue) as ConValue from tblcontracts
group by 
(case
when contractsigndate between '2014-04-01' and '2015-03-31' then '2014/15'
when contractsigndate between '2013-04-01' and '2014-03-31' then '2013/14'
when contractsigndate between '2012-04-01' and '2013-03-31' then '2012/13'
else
'Pre 2012'
END)
) as V
,
(
select 
(case
when weekstartdate between '2014-04-01' and '2015-03-31' then '2014/15'
when weekstartdate between '2013-04-01' and '2014-03-31' then '2013/14'
when weekstartdate between '2012-04-01' and '2013-03-31' then '2012/13'
else
'Pre 2012'
END) as conYear
,count(weekid) as conWeeks from tblweeks
group by 
(case
when weekstartdate between '2014-04-01' and '2015-03-31' then '2014/15'
when weekstartdate between '2013-04-01' and '2014-03-31' then '2013/14'
when weekstartdate between '2012-04-01' and '2013-03-31' then '2012/13'
else
'Pre 2012'
END)
) as W
where v.conyear = w.conyear

-- Ordering and Ranking


Update tblContracts
set
contractYear = Year([contractSigndate])

-- Row_Number is used to number your dataset
Select
ROW_NUMBER() over (order by [contractValue] desc, contractSigndate desc) as RowID, * 
From [dbo].[tblContracts]

-- Rank is used for Ranking the data set
drop table tblcontracts_v1 
Select
Rank() over (order by [contractValue] desc) as RowID, * 
into tblcontracts_v1
From [dbo].[tblContracts]


Select * from tblcontracts_v1

Select RowID, [contractValue], Count(*) as Duplicates
From [dbo].[tblContracts_v1]
group by RowID, [contractValue]
order by RowID

Select [contractValue], Count(*) as Duplicates
From [dbo].[tblContracts_v1]
group by [contractValue]
order by [contractValue] desc

-- Filtering techniques using sub query

Select * from 
(
Select
Rank() over (order by [contractValue] desc) as RowID, * 
From [dbo].[tblContracts]
) as R
 where R.RowID <=10

 --OR Filter using the WITH Clause

 With R as 
 (
 Select
Rank() over (order by [contractValue] desc) as RowID, * 
From [dbo].[tblContracts]
)
Select * from R where R.RowID <=10

-- use of with clause

Select [contactID]
      ,[fn]
      ,[sn]
      ,[House]
      ,[Street]
into tblcontacts_1
From [dbo].[tblContacts]

Select [contactID],
		[Town]
      ,[Region]
      ,[Zip-Postcode]
      ,[Country]
into tblcontacts_2
From [dbo].[tblContacts]


With 
A as 
(Select * From tblcontacts_1),
B as 
(Select * From tblcontacts_2)
Select * from A 
inner join B
on A.contactID = B.contactID

-- Ranking by groups and within groups

Select rank() over (order by sum(contractvalue) desc) as Ranking, contractYear,sum(contractValue) as Sales
From tblContracts
group by contractyear

-- creating a rank within a group
Select rank() over (partition by contractyear order by contractID) as Ranking, *
From tblContracts

-- when using rank, it will rank the data by region and will give same rank number to same sn values and will ofcourse sort by sn
select rank() over(partition by region order by sn) as RowID, *
From tblContacts

-- when using row_number, it will just rank the data by region sequentially and will ofcourse sort by sn
select row_number() over(partition by region order by sn) as RowID, *
From tblContacts

Select * from tblcontacts
order by contactID
Select * from tblContracts
order by contractClient

With A as
(
Select Contractclient, count(*) as counts, sum(contractValue) as Total
From tblcontracts_v1
group by contractClient
),
B as
(
Select * from tblContacts
)
SELECT A.CONTRACTCLIENT, A.COUNTS, A.TOTAL, REGION, FN+ ' ' + SN AS FULLNAME
into #Combined_Table
FROM A
INNER JOIN
B
ON
A.contractClient = B.contactID

Select Region, sum(Total) as TotalSales
from #Combined_Table
Where region is not null
group by region
order by Region desc

-- Filter a Rank column 

-- create a sub query using With clause
With A as
(
Select Rank() Over(partition by region order by total desc) as Position, * 
from Combined_Table
) 
Select * from A
where A.Position = 1

-- OR - create a sub query

Select Top 10 * from 
(Select Rank() Over(partition by region order by total desc) as Position, * 
from Combined_Table) A
where A.Position = 1


-- functions

select * from tblcontacts

/*
Deterministic functions 
-always return the same result any time they are called 
with a specific set of input values. 

Nondeterministic functions
- may return different results each time they are called 
with a specific set of input values. 
*/
select pi()as PI
select getdate() as NOW
select pi() * power(3,2)
select left('fred',3)
select power(3,2)
select month(getdate()) as Mth
use terra
select
pi() as PI,
getdate() as nowDate,
left(fn,1) as Initial,
right([zip-postcode],3) as WalkCode,
upper(sn) as UpperCase,
lower(sn) as lowercase,
substring(email,3,10) as MidString, --mid in some SQL 
len(town) as LenTown,
day(dateofbirth) as BirthDay,
month(dateofbirth) as BirthMonth,
year(dateofbirth) as BirthYear,
* from tblcontacts

--aggregate functions
select 
  sum(contractvalue) as TotalContractValue,
avg(contractvalue) as AvgContracts,
count(contractid) as NumberContracts,
max(contractvalue) as highestContractValue,
min(contractvalue) as lowestContractValue
  from tblcontracts
