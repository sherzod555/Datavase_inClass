# Datavase_inClass
Database Inclass




Mock test - possible solutions

1.	Count how many different jobtitles exist among employees.. [5 marks]
Select count (distinct jobtitle) from HumanResources.Employee

2.	Retrieve all information from Product table regarding all products not containing Metal in their names.
Select * from Production.Product
Where name not like '%metal%'
3.	Show JobTitle, HireDate, FirstName, LastName of those employees hired before first of January 2002. Order records by HireDate.
Select jobtitle, HireDate, pp.FirstName, pp.LastName from HumanResources.Employee he
join person.Person pp on he.BusinessEntityID=pp.BusinessEntityID
where hiredate < '2002-01-01'
order by HireDate
4.	Display all product names and corresponding culture names. 
Select pp.Name as product, pc.Name as model
from Production.Product pp 
join production.ProductModelProductDescriptionCulture pm on pp.ProductModelID=pm.ProductModelID
join Production.Culture pc on pm.CultureID=pc.CultureID

5.	Show JobTitle, BirthDate, Age of Employee, FirstName, LastName of those Employees who are older than 60. Order records by years.
Select jobtitle, BirthDate, DATEDIFF(YEAR, BirthDate, GETDATE()) as Age, pp.FirstName, pp.LastName from HumanResources.Employee he
join person.Person pp on he.BusinessEntityID=pp.BusinessEntityID
where DATEDIFF(YEAR, BirthDate, GETDATE())>60
order by Age

6.	Show Rates of Employees’ salary and corresponding JobTitle when Rate is lower than average rate.
select e.JobTitle, rate 
from HumanResources.EmployeePayHistory eph join HumanResources.Employee e 
	on eph.BusinessEntityID=e.BusinessEntityID
where rate<(select avg(eph.Rate) from HumanResources.EmployeePayHistory eph)

7.	Show the number of products per subcategory sorted in descending order only for those subcategories, which have more than 20 products.
Select count(pp.productID), ps.Name 
from Production.Product pp join Production.ProductSubcategory ps
on pp.ProductSubcategoryID=ps.ProductSubcategoryID
group by ps.Name
having count(pp.productID)>20
order by count(pp.productID) desc


8.	Create views on any two queries and comment whether they are updatable or not.
create view products_per_subcat_view as
Select pp.FirstName, pp.LastName,  rate, min(rate) over (partition by hd.name) as [minimum in its department], rate-min(rate) over (partition by hd.name) as difference  from Person.Person pp
join HumanResources.EmployeePayHistory hep
on pp.BusinessEntityID=hep.BusinessEntityID
join HumanResources.EmployeeDepartmentHistory hed
on hed.BusinessEntityID=pp.BusinessEntityID
join HumanResources.Department hd
on hd.DepartmentID=hed.DepartmentID

create view product_names_view as
Select pp.Name as product, pc.Name as model
from Production.Product pp 
join production.ProductModelProductDescriptionCulture pm 
on pp.ProductModelID=pm.ProductModelID
join Production.Culture pc on pm.CultureID=pc.CultureID


9.	Show list of employees ordered by department, employee rate and minimum rate for department this employee works at. For each employee show the difference between lowest rate in the department where employee works and his own salary.
Select pp.FirstName, pp.LastName,  rate, min(rate) over (partition by hd.name) as [minimum in its department], rate - min(rate) over (partition by hd.name) as difference  
from Person.Person pp
join HumanResources.EmployeePayHistory hep
on pp.BusinessEntityID=hep.BusinessEntityID
join HumanResources.EmployeeDepartmentHistory hed
on hed.BusinessEntityID=pp.BusinessEntityID
join HumanResources.Department hd
on hd.DepartmentID=hed.DepartmentID
order by hd.Name, rate

10.	Retrieve the BusinessEntityID, LoginID, JobTitle for those employees who are job candidates as well. You should use INTERSECT operator to combine the two queries and CTE.

WITH 
  cteCandidates (BusinessEntityID)
  AS
  (
    SELECT BusinessEntityID
    FROM HumanResources.Employee
    INTERSECT
    SELECT BusinessEntityID
    FROM HumanResources.JobCandidate
  )
SELECT 
  c.BusinessEntityID,
  e.LoginID,
  e.JobTitle
FROM 
  HumanResources.Employee AS e
  INNER JOIN cteCandidates AS c
    ON e.BusinessEntityID = c.BusinessEntityID
ORDER BY
  c.BusinessEntityID;
