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

# 3. Analysis Techniques

# 4. Insights and Conclusions
