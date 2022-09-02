# Answering business questions with SQL 

Structured Query Language (SQL) is one of the most useful skills, not only for data analysts, but for everyone that deal with relational databases. We never produced as much data as we do nowadays and being able to retrieve, explore, filter and clean them is going to be a necessary skill for all sort of professionals.  

I strongly believe that, in a near future, SQL is going to be a pre-requisite for many job positions, as much as we see for spreadsheets now. Considering this scenario and while working to improve my data analysis skills, I created this file to showcase how I could answer some business questions/requests, using SQL queries.

I did the queries using Microsoft SQL Server Management Studio and the dataset OLTP AdventureWorks2019.bak, from the [AdventureWorks sample databases](https://docs.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver16&tabs=ssms), provides by Microsoft.


![image](https://user-images.githubusercontent.com/100521949/188220512-c5cc4c31-3ef4-42a9-b246-9d264ff09762.png)


## QUESTIONS/REQUESTS 

1. Get the following informations about the employees: First and Last Name, Phone Number, Email Address and Address
```sql
SELECT FirstName, LastName, phone.PhoneNumber, EmailAddress, AddressLine1, AddressLine2, City, StateProvinceID, PostalCode 
FROM Person.Person person
LEFT JOIN Person.PersonPhone phone ON person.BusinessEntityID=phone.BusinessEntityID
LEFT JOIN Person.EmailAddress email ON person.BusinessEntityID=email.BusinessEntityID
LEFT JOIN Person.BusinessEntityAddress e_address ON person.BusinessEntityID=e_address.BusinessEntityID
LEFT JOIN Person.Address address on e_address.AddressID=address.AddressID
```

2. The company wants to do a RFM analysis like the one I did [here](https://github.com/belabarbosa/RFM-Analysis). 
To do so, it is necessary to retrieve the following information:
  - customer identification;
  - the date of their purchases; and,
  - the amount of each purchase;
```sql
SELECT oh.CustomerID, oh.OrderDate, oh.TotalDue
FROM sales.SalesOrderHeader oh
LEFT JOIN Sales.Customer c ON oh.CustomerID=c.CustomerID
ORDER BY oh.OrderDate
```

3. The company wants to see if the employees are having salary increases over time.
```sql
SELECT ph.BusinessEntityID, e.HireDate, COUNT(ph.BusinessEntityID) as salarychanges
FROM HumanResources.Employee e
	LEFT JOIN HumanResources.EmployeePayHistory ph ON e.BusinessEntityID=ph.BusinessEntityID
GROUP BY ph.BusinessEntityID, HireDate
ORDER BY salarychanges DESC
```

4. Find out how many customers we have that haven't made any purchase.
```sql
SELECT COUNT (*) FROM  Sales.Customer c
LEFT JOIN Sales.SalesOrderHeader as oh ON c.CustomerID=oh.CustomerID
WHERE oh.SalesOrderID IS NULL 
```

5. How many males and how many females employees do we have in the company?
```sql
SELECT Gender, COUNT(Gender)
FROM HumanResources.Employee
GROUP BY Gender
```
	
6. Marital status of the emploees by gender
```sql
SELECT Gender, MaritalStatus, Count(Gender)
FROM HumanResources.Employee
GROUP BY Gender, MaritalStatus
```

7. How many employees do we have for each job title?

```sql
SELECT JobTitle, COUNT(JobTitle) AS "CountJobTitle"
FROM HumanResources.Employee
GROUP BY JobTitle
ORDER BY CountJobTitle DESC
```

8. Undestand what are the main reasons the customers choose to buy with the company.
```sql
SELECT oh.SalesOrderID, oh.OrderDate, oh.TotalDue, sr.Name AS Reason
FROM sales.SalesOrderHeader oh
INNER JOIN sales.SalesOrderHeaderSalesReason ohsr ON oh.SalesOrderID=ohsr.SalesOrderID
INNER JOIN sales.SalesReason sr ON ohsr.SalesReasonID=sr.SalesReasonID
```
