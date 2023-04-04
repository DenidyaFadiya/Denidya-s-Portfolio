# Denidya-s-Portfolio
Analytics's Portfolio


--Turkey Sales Data Exploration

--select the database that we are going to be using

use CustomerData

--select the data that we are going to be using

select *
from dbo.customerdata
order by customer_id 


-- change the category to the data that represents it by using this query,
-- but i will not do it since i will use join later

select customer_id, category_id,
case
	when Category_Id = '1' then 'Clothing'
	when Category_Id = '2' then 'Shoes'
	when Category_Id = '3' then 'Books'
	when Category_Id = '4' then 'Cosmetics'
	when Category_Id = '5' then 'Food and Beverage'
	when Category_Id = '6' then 'Toys'
	when Category_Id = '7' then 'Technology'
	when Category_Id = '8' then 'Shoes'
	else 'Souvernir'
end as CategoryFixed
from dbo.CustomerData
order by Customer_Id


--join all of the tables and specify which columns to be selected

select cus.customer_id, 
		pay.Invoice_date,
		gender, 
		age, 
		price, 
		category,
		quantity,
		payment_method, 
		Shopping_mall
from dbo.customerdata cus
join dbo.Category cat
	on Cat.category_id = Cus.category_id
join dbo.payment pay
	on pay.customer_id = cus.customer_id
join dbo.ShoppingMall shop
	on Shop.ShoppingMall_Id = Cus.ShoppingMall_Id



DROP TABLE IF EXISTS dbo.customerdata
DROP TABLE IF EXISTS dbo.Category
DROP TABLE IF EXISTS dbo.Customer_Shopping_Data
DROP TABLE IF EXISTS dbo.payment
DROP TABLE IF EXISTS dbo.shoppingmall


-- total amount spent

select SUM(price) as TotalSpent
from dbo.customerdata
where gender like '%male'


--calculating total amount spent by gender 

select gender,
		COUNT(gender) as OrderByGender,
		SUM(price) as TotalSpentByGender
from dbo.customerdata
group by gender


--total spent by gender in percent

select gender,
		COUNT(gender) as OrderByGender,
		(SUM(price) * 100) / SUM(SUM(price)) over () as TotalSpentByGender
from dbo.customerdata
group by gender


--total spent based on amount and percent by gender 

select gender,
		COUNT(gender) as OrderByGender,
		SUM(price) as TotalSpentByGender,
		(SUM(price) * 100) / SUM(SUM(price)) over () as PercentSpentByGender
from dbo.customerdata
group by gender



--Find the category bought by gender

select gender, 
		cat.category,
		COUNT(category) as CategoryBought
from dbo.customerdata cus
join dbo.category cat
	on cat.category_id = Cus.category_id
group by gender, category
order by gender, CategoryBought desc


--category bought based on age

select age,
case
	when age < 26 then 'Young Adult'
	when age < 50 then 'Middle Age Adult'
	when age < 61 then 'Senior Adult'
	else 'Senior'
end as AgeGroup
from dbo.CustomerData



---spreading of category contribution

select category,
		COUNT(Category) as OrderByCategory,
		(COUNT(Category) * 100) / COUNT(COUNT(Category)) over () as PercentSpentByGender
from dbo.customerdata cus
join dbo.Category cat
	on Cat.category_id = Cus.category_id
group by category


---sum of transactions amount paid by method

select  payment_method,
		SUM(price) as PaidByMethod,
		(SUM(price) * 100) / SUM(SUM(price)) over () as PercentPaidByMethod
from dbo.customerdata cus
join dbo.payment pay
	on pay.customer_id = cus.customer_id
group by payment_method


--total quantity per category 
--but in this query the quantity will be separated in 5 category each
--meaning this query does not sum the specific criteria but calculating
--based on the number of criteria on Customer Data. 
--hence why im using the CTE so the calculation will be based on the category

select  category,
		SUM(quantity) as TotalQuantity
from dbo.customerdata cus
join dbo.category cat
	on cat.category_id = cus.category_id
group by category, quantity
order by category


--CTE to sum it in specific criteria

with CTE_QuantityPerCategory
as 
(
select category,
		SUM(quantity) as TotalQuantity
from dbo.customerdata cus
join dbo.category cat
	on cat.category_id = cus.category_id
group by category 
)
select *
from CTE_QuantityPerCategory
order by TotalQuantity desc


