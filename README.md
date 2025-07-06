# carboon_emission_analysis
## 1. Introduction
This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends.
![image](https://github.com/user-attachments/assets/01b99959-3bb2-4c65-88d2-d193a7fb0e5d)
## 1.1 Data model
The dataset consists of 4 tables containing information regarding carbon emissions generated during the production of goods.
![image](https://github.com/user-attachments/assets/5f2b21b3-0615-45b9-b51b-f1fd01678430)
## 1.2 Data structure
Table 'product_emissions'
```SQL
SELECT * FROM product_emissions LIMIT 10;
```
|id|company_id|country_id|industry_group_id|year|product_name|weight_kg|carbon_footprint_pcf|upstream_percent_total_pcf|operations_percent_total_pcf|downstream_percent_total_pcf|
|--|----------|----------|-----------------|----|------------|---------|--------------------|--------------------------|----------------------------|----------------------------|
|10056-1-2014|82|28|2|2014|Frosted Flakes(R) Cereal|0.7485|2|57.50|30.00|12.50|
|10056-1-2015|82|28|15|2015|"Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)"|0.7485|2|57.50|30.00|12.50|
|10222-1-2013|83|28|8|2013|Office Chair|20.68|73|80.63|17.36|2.01|
|10261-1-2017|14|16|25|2017|Multifunction Printers|110.0|1488|30.65|5.51|63.84|
|10261-2-2017|14|16|25|2017|Multifunction Printers|110.0|1818|25.08|4.51|70.41|
|10261-3-2017|14|16|25|2017|Multifunction Printers|110.0|2274|20.05|3.61|76.34|
|10324-1-2016|15|16|19|2016|KURALON  fiber|1500.0|10000|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|
|10418-1-2013|84|9|19|2013|Portland Cement|1000.0|1102|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|
|10661-10-2014|85|28|11|2014|Regular Straight 505® Jeans – Steel (Water<Less™)|0.7665|15|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|
|10661-10-2015|85|28|6|2015|Regular Straight 505® Jeans – Steel (Water<Less™)|0.7665|15|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|

## 2. Data explore
Data duplicate
```SQL
SELECT *,
    COUNT(*) AS duplicate_count 
FROM product_emissions pe
GROUP BY
     id,
     company_id,
     country_id,
     industry_group_id,
     year,
     product_name,
     weight_kg,
     carbon_footprint_pcf,
     upstream_percent_total_pcf,
     operations_percent_total_pcf,
     downstream_percent_total_pcf
HAVING COUNT(*) > 1
LIMIT 10;
```
|id|company_id|country_id|industry_group_id|year|product_name|weight_kg|carbon_footprint_pcf|upstream_percent_total_pcf|operations_percent_total_pcf|downstream_percent_total_pcf|duplicate_count|
|--|----------|----------|-----------------|----|------------|---------|--------------------|--------------------------|----------------------------|----------------------------|---------------|
|10056-1-2014|82|28|2|2014|Frosted Flakes(R) Cereal|0.7485|2|57.50|30.00|12.50|2|
|10056-1-2015|82|28|15|2015|"Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)"|0.7485|2|57.50|30.00|12.50|2|
|10222-1-2013|83|28|8|2013|Office Chair|20.68|73|80.63|17.36|2.01|2|
|10261-1-2017|14|16|25|2017|Multifunction Printers|110.0|1488|30.65|5.51|63.84|2|
|10261-2-2017|14|16|25|2017|Multifunction Printers|110.0|1818|25.08|4.51|70.41|2|
|10261-3-2017|14|16|25|2017|Multifunction Printers|110.0|2274|20.05|3.61|76.34|2|
|10324-1-2016|15|16|19|2016|KURALON  fiber|1500.0|10000|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|2|
|10418-1-2013|84|9|19|2013|Portland Cement|1000.0|1102|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|2|
|10661-1-2014|85|28|11|2014|501® Original Jeans – Dark Stonewash|0.997|16|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|2|
|10661-1-2015|85|28|6|2015|501® Original Jeans – Dark Stonewash|0.997|16|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|2|

Duplicate result
```SQL
SELECT COUNT(product_name) AS 'Total number of products',
       COUNT(DISTINCT product_name) AS 'Number of unique products'
FROM product_emissions pe
```
|Total number of products|Number of unique products|
|------------------------|-------------------------|
|1037|661|

## 3. Data analysis
### 3.1 Which products contribute the most to carbon emissions?
```SQL
select product_name,
       ROUND(AVG(carbon_footprint_pcf),2) as 'avg_PCF'
from product_emissions pe
group by product_name
order by carbon_footprint_pcf DESC
limit 10
```
|product_name|avg_PCF|
|------------|-------|
|Wind Turbine G128 5 Megawats|3718044.00|
|Wind Turbine G132 5 Megawats|3276187.00|
|Wind Turbine G114 2 Megawats|1532608.00|
|Wind Turbine G90 2 Megawats|1251625.00|
|Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.|191687.00|
|Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall|167000.00|
|TCDE|99075.00|
|Mercedes-Benz GLE (GLE 500 4MATIC)|91000.00|
|Mercedes-Benz S-Class (S 500)|85000.00|
|Mercedes-Benz SL (SL 350)|72000.00|

- As you can see, most of Wind Turbine and Mercedes-Benz contributes to carbon emissions.

### 3.2 What are the industry groups of these products?
```SQL
select ig.industry_group,
       pe.product_name,
       ROUND(AVG(carbon_footprint_pcf),2) as 'avg_PCF'
from product_emissions pe
join industry_groups ig on ig.id = pe.industry_group_id
group by product_name
order by carbon_footprint_pcf DESC
limit 10;
```
|industry_group|product_name|avg_PCF|
|--------------|------------|-------|
|Electrical Equipment and Machinery|Wind Turbine G128 5 Megawats|3718044.00|
|Electrical Equipment and Machinery|Wind Turbine G132 5 Megawats|3276187.00|
|Electrical Equipment and Machinery|Wind Turbine G114 2 Megawats|1532608.00|
|Electrical Equipment and Machinery|Wind Turbine G90 2 Megawats|1251625.00|
|Automobiles & Components|Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.|191687.00|
|Materials|Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall|167000.00|
|Materials|TCDE|99075.00|
|Automobiles & Components|Mercedes-Benz GLE (GLE 500 4MATIC)|91000.00|
|Automobiles & Components|Mercedes-Benz S-Class (S 500)|85000.00|
|Automobiles & Components|Mercedes-Benz SL (SL 350)|72000.00|

### 3.3 What are the industries with the highest contribution to carbon emissions?
```SQL
select ig.industry_group,
       ROUND(SUM(pe.carbon_footprint_pcf),2) as 'total_PCF'
from product_emissions pe
join industry_groups ig on ig.id = pe.industry_group_id
group by ig.industry_group
order by ROUND(SUM(pe.carbon_footprint_pcf),2) DESC
limit 10;
```
|industry_group|total_PCF|
|--------------|---------|
|Electrical Equipment and Machinery|9801558.00|
|Automobiles & Components|2582264.00|
|Materials|577595.00|
|Technology Hardware & Equipment|363776.00|
|Capital Goods|258712.00|
|"Food, Beverage & Tobacco"|111131.00|
|"Pharmaceuticals, Biotechnology & Life Sciences"|72486.00|
|Chemicals|62369.00|
|Software & Services|46544.00|
|Media|23017.00|


### 3.4 What are the companies with the highest contribution to carbon emissions?
```SQL
select c.company_name ,
       pe.company_id,
       ROUND(SUM(pe.carbon_footprint_pcf),2) as 'total_PCF'
from companies c 
join product_emissions pe on c.id = pe.company_id
group by company_name
order by carbon_footprint_pcf DESC
limit 10;
```
|company_name|company_id|total_PCF|
|------------|----------|---------|
|"Gamesa Corporación Tecnológica, S.A."|10|9778464.00|
|"Hino Motors, Ltd."|11|191687.00|
|Daimler AG|61|1594300.00|
|Weg S/A|141|160655.00|
|General Motors Company|71|137007.00|
|CJ Cheiljedang|53|94817.00|
|Volkswagen AG|139|655960.00|
|Waters Corporation|140|72486.00|
|"Kuraray Co., Ltd."|15|20000.00|
|Bloomberg|41|22684.00|


### 3.5 What are the countries with the highest contribution to carbon emissions?
```SQL
select c.country_name ,
       pe.country_id ,
       ROUND(SUM(pe.carbon_footprint_pcf),2) as 'total_PCF'
from countries c 
join product_emissions pe on c.id = pe.country_id 
group by country_name 
order by carbon_footprint_pcf DESC
limit 10;
```
|country_name|country_id|total_PCF|
|------------|----------|---------|
|Germany|10|2251225.00|
|South Korea|22|186965.00|
|Brazil|3|169337.00|
|Japan|16|653237.00|
|India|12|24574.00|
|Netherlands|20|70417.00|
|France|9|2715.00|
|South Africa|21|12312.00|
|Ireland|14|5130.00|
|Indonesia|13|721.00|


### 3.6 What is the trend of carbon footprints (PCFs) over the years?
```SQL
select 
	ig.industry_group as 'indus_group' ,
	ROUND(SUM(case when pe.year = 2013 then pe.carbon_footprint_pcf else 0 end), 2) as '2013_emission' ,
	ROUND(SUM(case when pe.year = 2014 then pe.carbon_footprint_pcf else 0 end), 2) as '2014_emission' ,
	ROUND(SUM(case when pe.year = 2015 then pe.carbon_footprint_pcf else 0 end), 2) as '2015_emission' ,
	ROUND(SUM(case when pe.year = 2016 then pe.carbon_footprint_pcf else 0 end), 2) as '2016_emission' ,
	ROUND(SUM(case when pe.year = 2017 then pe.carbon_footprint_pcf else 0 end), 2) as '2017_emission' 
from product_emissions as pe 
left join industry_groups as ig on ig.id = pe.industry_group_id 
group by ig.industry_group 
order by
	`2017_emission`,
	`2016_emission`,
	`2015_emission`,
	`2014_emission`,
	`2013_emission`;
```
|indus_group|2013_emission|2014_emission|2015_emission|2016_emission|2017_emission|
|-----------|-------------|-------------|-------------|-------------|-------------|
|Household & Personal Products|0.00|0.00|0.00|0.00|0.00|
|"Pharmaceuticals, Biotechnology & Life Sciences"|32271.00|40215.00|0.00|0.00|0.00|
|Tobacco|0.00|0.00|1.00|0.00|0.00|
|Semiconductors & Semiconductors Equipment|0.00|0.00|3.00|0.00|0.00|
|Retailing|0.00|19.00|11.00|0.00|0.00|
|Gas Utilities|0.00|0.00|122.00|0.00|0.00|
|Food & Beverage Processing|0.00|0.00|141.00|0.00|0.00|
|Telecommunication Services|52.00|183.00|183.00|0.00|0.00|
|Trading Companies & Distributors and Commercial Services & Supplies|0.00|0.00|239.00|0.00|0.00|
|"Textiles, Apparel, Footwear and Luxury Goods"|0.00|0.00|387.00|0.00|0.00|
|"Consumer Durables, Household and Personal Products"|0.00|0.00|931.00|0.00|0.00|
|Tires|0.00|0.00|2022.00|0.00|0.00|
|Containers & Packaging|0.00|0.00|2988.00|0.00|0.00|
|"Mining - Iron, Aluminum, Other Metals"|0.00|0.00|8181.00|0.00|0.00|
|"Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber"|0.00|0.00|8909.00|0.00|0.00|
|Chemicals|0.00|0.00|62369.00|0.00|0.00|
|Electrical Equipment and Machinery|0.00|0.00|9801558.00|0.00|0.00|
|Food & Staples Retailing|0.00|773.00|706.00|2.00|0.00|
|Semiconductors & Semiconductor Equipment|0.00|50.00|0.00|4.00|0.00|
|Utilities|122.00|0.00|0.00|122.00|0.00|
|Consumer Durables & Apparel|2867.00|3280.00|0.00|1162.00|0.00|
|Media|9645.00|9645.00|1919.00|1808.00|0.00|
|Energy|750.00|0.00|0.00|10024.00|0.00|
|Automobiles & Components|130189.00|230015.00|817227.00|1404833.00|0.00|
|Software & Services|6.00|146.00|22856.00|22846.00|690.00|
|Commercial & Professional Services|1157.00|477.00|0.00|2890.00|741.00|
|"Food, Beverage & Tobacco"|4995.00|2685.00|0.00|100289.00|3162.00|
|Technology Hardware & Equipment|61100.00|167361.00|106157.00|1566.00|27592.00|
|Capital Goods|60190.00|93699.00|3505.00|6369.00|94949.00|
|Materials|200513.00|75678.00|0.00|88267.00|213137.00|





