# Mint Classics Company - Model Car
This is Coursera Project, _Analyze Data in a Model Car Database with MySQL Workbench_, where I will be will stepping into the shoes of an entry-level data analyst at the fictional Mint Classics Company, helping to analyze data in a relational database with the goal of supporting inventory-related business decisions that lead to the closure of a storage facility.

## Project Scenario
Mint Classics Company, a retailer of classic model cars and other vehicles, is looking at closing one of their storage facilities. 

To support a data-based business decision, they are looking for suggestions and recommendations for reorganizing or reducing inventory, while still maintaining timely service to their customers. For example, they would like to be able to ship a product to a customer within 24 hours of the order being placed.

As a data analyst, you have been asked to use MySQL Workbench to familiarize yourself with the general business by examining the current data. You will be provided with a data model and sample data tables to review. You will then need to isolate and identify those parts of the data that could be useful in deciding how to reduce inventory. You will write queries to answer questions like these:

1) Where are items stored and if they were rearranged, could a warehouse be eliminated?

2) How are inventory numbers related to sales figures? Do the inventory counts seem appropriate for each item?

3) Are we storing items that are not moving? Are any items candidates for being dropped from the product line?

The answers to questions like those should help you to formulate suggestions and recommendations for reducing inventory with the goal of closing one of the storage facilities. 

## Project Objectives
1. Explore products currently in inventory.

2. Determine important factors that may influence inventory reorganization/reduction.

3. Provide analytic insights and data-driven recommendations.


## Project Challenge
Conduct an exploratory data analysis to investigate if there are any patterns or themes that may influence the reduction or reorganization of inventory in the Mint Classics storage facilities. To do this, you will import the database and then analyze data. You will also pose questions, and seek to answer them meaningfully using SQL queries to retrieve data from the database provided.

&nbsp;
&nbsp;
&nbsp;

# 1. Database Import
For this part, we are given the required database, **mintclassicsDB**, which I will import to MySQL Workbench. Once MySQL Workbench is setup, go to `Server > Data Import` and select `Import from Self-Contained File` and choose the database file, and click `Start Import` from the bottom of the window. This will import the required schema and database to your MySQL server. 

## 1.1 Extended Entity-Relationship Diagram
![EER Diagram for mintclassicsDB](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/jBRNreo5Sh-41b3l0jBkCw_c3a54862d56945659bdb44bc07a368f1_MintClassicsDataModel.png?expiry=1705536000000&hmac=VqGS_0g-c8JQEgveX_BYYfkiR_K3I4LKiypqxexSyeQ)

From 1.1, it is clear that **Warehouses**, **Products**, **OrderDetails**, and **Order** tables will be helpful answering our business question.

&nbsp;
&nbsp;
&nbsp;

# 2. Data Understanding
In this section, we will be looking into each of the mention tables in _1.1_ in details and try to understand the business information capture in those tables. 

## 2.1 Warehouses Table
```sql
SELECT *
FROM warehouses;
```

As expect, _warehouses_ table holds the information regarding warehouse - such as name, location, and what looks like current capacity percentage. 

<img width="540" alt="Warehouses Table" src="https://github.com/Richa-CM/Mint-Classics-Model-Car/assets/156695804/1630dd37-c04f-4504-aafd-aa91db058b45">

Warehouse `C` has lowest percentage capacity, at 50%, and Warehouse `D` has highest capacity, at 75%. From that informaiton alone we can identify that Warehouse `C` will be use to hold extra inventory from warehouse we identify to close, if additional space is needed then we can utlize other two warehouses. 

## 2.2 Products Table
```sql
-- Sort by quantity in stock and warehouse code
SELECT *
FROM products p
WHERE quantityInStock >= 9000
OR quantityInStock <= 1000
ORDER BY quantityInStock desc, warehouseCode;
```

Result

<img width="1080" alt="Products table filter by quantityInStock" src="https://github.com/Richa-CM/Mint-Classics-Model-Car/assets/156695804/cb430826-95b5-483c-9df4-577fe28ae30a">


As seen from the image above, products with high and low quantity in stock are scattered across warehouses `b`, `c`, and `a`. There are few products from warehouse `d` in the above query but not in the end of the spectrum. 

```sql
-- Filter by selling price greater than 150 or buying price less than 30
SELECT *
FROM products p
WHERE p.MSRP >= 150
OR p.buyPrice <= 30
ORDER by p.MSRP desc;
```
<img width="1150" alt="Products table filter by MSRP and buyPrice" src="https://github.com/Richa-CM/Mint-Classics-Model-Car/assets/156695804/8aa8997b-869a-480a-ad69-e0b0ef623616">

Again, this result is scatter across warehouses other than warehouse `D`. It is clear from above two result set that there is no correlation between product (buying/selling) price and warehouse it is utlized. 

## 2.3 Order Details Table
```sql
SELECT *
FROM orderdetails;
```

Result

<img width="627" alt="Orderdetails table" src="https://github.com/Richa-CM/Mint-Classics-Model-Car/assets/156695804/7df09ac7-b497-4fdd-b592-459519ebd01e">

This table will be useful to identity products' demand if we sum up the quantity _ordered_ so far. 

## 2.4 Orders Table
```sql
SELECT *
FROM orders;
```

Result
<img width="1222" alt="Orders Table" src="https://github.com/Richa-CM/Mint-Classics-Model-Car/assets/156695804/c3c947e7-7737-4b08-a0a1-675afe929e2f">

This table stores some useful information related to orders such as time it took from order placement to shippment, order status since not all orders are shipped so inventory count in products table will not reflect orders that are in progress or pending. 

## 2.5 Unkown Variables
Even though table name and column name are self-explaintory, we are unsure what _warehousePctCap_ means, nor do we know what _orderLineNumber_ means in **orderdetails** table. Though, this should not affect data validity. 

&nbsp;
&nbsp;
&nbsp;

# 3. Analysis Techniques

&nbsp;

## 3.1 Could a warehouse be eliminated?
To answer this question, we need to find out current products in warehouse and total capacity (100%) of warehouse. To do this, we need to utlize **warehouses**, **products**, **orderdetails**, and **order** tables as following:

```sql
-- Query to find total items in each warehouse 
SELECT 
    SUM(CASE WHEN p.warehouseCode = 'a' THEN p.quantityInStock ELSE 0 END) AS inventoryCountA,
    SUM(CASE WHEN p.warehouseCode = 'b' THEN p.quantityInStock ELSE 0 END) as  inventoryCountB,
    SUM(CASE WHEN p.warehouseCode = 'c' THEN p.quantityInStock ELSE 0 END) AS inventoryCountC, 
    SUM(CASE WHEN p.warehouseCode = 'd' THEN p.quantityInStock ELSE 0 END) AS inventoryCountD
FROM
    mintclassics.products p;
```

Result

<img width="436" alt="Inventory Count for each warehouse" src="https://github.com/Richa-CM/Mint-Classics-Model-Car/assets/156695804/dd147451-4ac2-4fc1-b00b-ea664892efc7">

Converting this into a simple table, and using information from `2.1` then we get

| Warehouse | Current Capacity (%) | Available Capacity (%) | Full Capacity |
| --- | --- | --- | --- |
| Warehouse A | 131,688 (72%) | 51,212 (28%) | 182,900 (100%) |
| Warehouse B | 219,183 (67%) | 107,957 (33%) | 327,140 (100%) |
| Warehouse C | 124,880 (50%) | 124,880 (50%) | 249,760 (100%) | 
| Warehouse D | 79,380 (75%) | 26,460 (25%) | 105,840 (100%) |

This answers our first question - if [items] are rearranged, could a warehouse be eliminated. Yes, we can eliminate/close **Warehouse D** and move inventory to either Warehouse `C` or `B` since both have available space. 

## 3.2 How are inventory numbers related to sales figures? Do the inventory counts seem appropriate for each item?
```sql
-- Find total number of item sold, percentage in stock, and markup percentage
SELECT 
    p.productCode,
    p.productName,
    p.warehouseCode,
    SUM(od.quantityOrdered) AS totalItemsSold,
    p.quantityInStock,
    (p.quantityInStock / (SUM(od.quantityOrdered) + p.quantityInStock)) * 100 AS stockPct,
    p.buyPrice,
    p.MSRP,
    ROUND((p.MSRP - p.buyPrice) * 0.01 * 100, 2) AS markupPct
FROM orderdetails od
JOIN products p
    ON od.productCode = p.productCode
JOIN orders o
    ON o.orderNumber = od.orderNumber
WHERE o.status in ('Shipped', 'Resolved')
GROUP BY p.productCode , p.productName , p.quantityInStock
ORDER BY totalItemsSold desc, stockPct DESC , markupPct DESC;
```

Result

<img width="800" alt="Total number of each product sold, percentage in stock and markup percentage" src="https://github.com/Richa-CM/Mint-Classics-Model-Car/assets/156695804/fd5acf95-ab66-496c-b694-c67ecec3b2e0">

&nbsp; 

As we can see from the result, majority of products sold so far are around 1,000 quantities, and we have over 9,000 quantities in stock. That is over 90% stock in warehouse, which seems too high considering that demand is not that different than other products. 

&nbsp; 

<img width="805" alt="Sort by stockPct" src="https://github.com/Richa-CM/Mint-Classics-Model-Car/assets/156695804/ce89c53a-2cf4-4e74-a28f-276137b0eae9">

<img width="800" alt="Quantity sold vs in stock graph" src="https://github.com/Richa-CM/Mint-Classics-Model-Car/assets/156695804/168478f7-1a77-4612-b257-58e303360c88">

<img width="800" alt="Quantity in stock" src="https://github.com/Richa-CM/Mint-Classics-Model-Car/assets/156695804/79e506ea-5f68-47e1-b344-531a4694adf0">

<img width="800" alt="Price vs quantity in stock graph" src="https://github.com/Richa-CM/Mint-Classics-Model-Car/assets/156695804/3413d989-92c1-4b8b-9119-0cc0ca63f78b">

<img width="800" alt="Warehouse code vs quantity in stock graph" src="https://github.com/Richa-CM/Mint-Classics-Model-Car/assets/156695804/d92450e3-052f-4978-8dea-6f8fc788fc7d">

&nbsp;
&nbsp;

As we can see from graphs above, there is no clear correlation between quantity in stock and factors such as quantity sold, warehouse it is stored, neither prices. Majority of products are overstock, **inventory count for each item is not appropriate** according to graphs above.


## 3.3 Are we storing items that are not moving? Are any items candidates for being dropped from the product line?
As we can see from _3.2_, there were no items that are not moving. Thus, there are no candidates from being dropped from the product line. However, quantities are overstock, thus, could be lowered to accommodate new or other products, potentially allowing to close additional warehouse. 

&nbsp;
&nbsp;
&nbsp;

# 4. Insights and Conclusions
Based on our findings from _Section 3_, there is clear indication that:

1. Warehouse D can be closed/eliminated and move inventory to Warehouse C or B.
2. There is no correlation between quantity in stock and products' demand.
3. There is no correlation between quantity in stock and products' buying price.
4. There is no correlation between quantity in stock and products' selling price.
5. There is no inventory management at the company.

## Recommendation
Implementing inventory management principles and best practices, reduce overhead quantity in stock and increase quantity in stock for understock products would help business achieve higher revenue and profit and decrease operating cost. 
