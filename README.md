# Automobile Sales Analysis

![](https://github.com/Makindetitilayo/Automobile-Sales-Analysis/blob/main/Cover%20Image.png)

## Introduction:

The goal of this project was to perform a detailed analysis of an automobile dataset by cleaning and preparing the data in Microsoft SQL Server, then building an interactive dashboard in Power BI. 

The analysis focuses on pricing, fuel efficiency, performance, and body style trends in the automobile market. This report documents the step-by-step process, including SQL queries written, the design of KPIs in DAX, and the final dashboard.

## Key Metrics:

The following metrics were selected as the foundation for the analysis:

- Average Price by Make to understand brand positioning.
- Price Distribution to analyze affordability ranges.
- Average Combined MPG by Make to compare fuel efficiency.
- Horsepower vs Price to highlight performance vs cost trade-offs.
- Price per Horsepower by Make to evaluate performance value.
- Car Count by Body Style and Fuel Type to study categorical distributions.

## Skills Demonstrated:

This project highlighted the following skills:

- SQL (Microsoft SQL Server): Data import, cleaning, transformation, aggregation, and query optimization.
- Data Modeling: Designing metrics (KPIs) and defining relationships between attributes.
- Data Cleaning: Handling null values, ensuring correct data types, and preventing division errors.
- Power BI: Creating DAX measures, visualizations, filters, slicers, and interactive dashboards.
- Analytical Thinking: Selecting meaningful metrics to balance affordability, efficiency, and performance insights.
- Storytelling with Data: Presenting technical results in an accessible and visually appealing dashboard.

## Data Preparation:

Dataset: Automobile CSV file containing attributes such as make, body style, price, horsepower, MPG, and engine size.

Import: Imported the CSV file into Microsoft SQL Server Management Studio (SSMS) using the Import Wizard.

Cleaning:
- Ensured correct data types (numeric vs categorical).
- Replaced or retained NULL values as needed.
- Prevented division by zero in calculations using NULLIF().
- Verified that there are no duplicates in primary fields.

## SQL Queries for Metrics

Wrote SQL scripts to calculate key insights that later powered the Power BI visuals.

### 1. Average Car Price by make 
```Sql
SELECT
	make,
	AVG(CAST(price AS FLOAT)) AS avg_price,
    COUNT(*) AS model_count
FROM
	Automobile_raw
WHERE
	price IS NOT NULL
GROUP BY
	make
ORDER BY 
	avg_price DESC;
```

### 2. Number of cars according to Price Distribution 
```Sql
SELECT 
    CASE 
        WHEN price < 10000 THEN '< 10k'
        WHEN price BETWEEN 10000 AND 19999 THEN '10k - 19.9k'
        WHEN price BETWEEN 20000 AND 29999 THEN '20k - 29.9k'
        ELSE '30k+'
    END AS price_range,
    COUNT(*) AS cars_in_price_range
FROM
	Automobile_raw
WHERE
	price IS NOT NULL
GROUP BY
	CASE 
        WHEN price < 10000 THEN '< 10k'
        WHEN price BETWEEN 10000 AND 19999 THEN '10k - 19.9k'
        WHEN price BETWEEN 20000 AND 29999 THEN '20k - 29.9k'
        ELSE '30k+'
    END
ORDER BY 
    MIN(price);
```

### 3. Fuel Efficiency (Avg MPG) by Make
```Sql
SELECT 
    make,
    AVG( (CAST(city_mpg AS FLOAT) + CAST(highway_mpg AS FLOAT)) / 2.0 ) AS avg_combined_mpg,
    AVG(CAST(city_mpg AS FLOAT)) AS avg_city_mpg,
    AVG(CAST(highway_mpg AS FLOAT)) AS avg_highway_mpg,
    COUNT(*) AS model_count
FROM 
	Automobile_raw
WHERE
	city_mpg IS NOT NULL 
	AND highway_mpg IS NOT NULL
GROUP BY
	make
ORDER BY 
	avg_combined_mpg DESC;
```
### 4. Car Count by Body Style and MPG
```Sql
SELECT 
    body_style,
    AVG( (CAST(city_mpg AS FLOAT) + CAST(highway_mpg AS FLOAT)) / 2.0 ) AS avg_combined_mpg,
    COUNT(*) AS model_count
FROM
	Automobile_raw
WHERE 
	city_mpg IS NOT NULL 
	AND highway_mpg IS NOT NULL
GROUP BY 
	body_style
ORDER BY 
	avg_combined_mpg DESC;
```
### 5. Comparing the relationship between horsepower and price by make (Horsepower v Price)
```Sql
SELECT 
    make,
    body_style,
    horsepower,
    price,
    engine_size
FROM 
	Automobile_raw
WHERE
	horsepower IS NOT NULL 
	AND price IS NOT NULL;
```
### 6. Fuel Type Distribution by Body Style
```Sql
SELECT 
    body_style,
    fuel_type,
    COUNT(*) AS car_count
FROM 
	Automobile_raw
GROUP BY 
	body_style, 
	fuel_type
ORDER BY 
	body_style, 
	fuel_type;
```
### 7. Price per horsepower by make
```Sql
SELECT 
    make,
    AVG(price / horsepower) AS price_per_horsepower
FROM 
	Automobile_raw
WHERE price IS NOT NULL 
		AND horsepower IS NOT NULL 
		AND horsepower > 0
GROUP BY 
	make
ORDER BY 
	price_per_horsepower ASC;
```

## Visualisation & Analysis:
![](https://github.com/Makindetitilayo/Automobile-Sales-Analysis/blob/main/Screenshot%202025-10-02%20002001.png)

After cleaning and preparing data with SQL, i imported it into Power BI. Then created DAX measures to provide dynamic KPIs:
- Total Number of Cars:
```DAX
COUNTROWS(AutomobileRaw)
```
- Average Price of Cars: 
```DAX
AVERAGE(AutomobileRaw[price])
```
-Average MPG: 
```DAX
AVERAGEX(AutomobileRaw, (AutomobileRaw[city_mpg] + AutomobileRaw[highway_mpg]) / 2)
```
- Average Horsepower:
```DAX
AVERAGE(AutomobileRaw[horsepower])
```
- Filters/Slicers Added: Make, Fuel System, Aspiration, and  Body Style.

### Analysis:

The analysis revealed distinct patterns across price, efficiency, and performance.

### Average Car Price by Make: 
Jaguar, Mercedes-Benz, Porsche, BMW, Volvo, Audi, and Mercury emerge as the most expensive brands in the dataset. These makes dominate the luxury segment, with prices substantially higher than the overall mean. This positions them as premium and aspirational brands.

### Price Distribution:
The majority of cars (98) fall under the $10,000 price bracket, with 78 cars priced between $10k–19.9k. Only 11 cars are found between $20k–29.9k, while 14 fall into the $30k+ range. This indicates that the dataset is heavily skewed toward economy vehicles, with only a small representation of luxury or premium models.

### Fuel Efficiency by Make: 
Chevrolet, Isuzu, Honda, Volkswagen, Plymouth, Dodge, and Toyota are the top performers in fuel efficiency (average MPG). These brands consistently deliver economical vehicles, reinforcing their value in markets where cost of ownership and running expenses are major decision drivers.

### Horsepower vs Price: 
High-performance and luxury-oriented brands dominate here, with Peugeot, Volvo, BMW, Mercedes-Benz, Audi, Saab, Porsche, Alfa Romeo, Jaguar, and Mercury appearing at the top. Higher horsepower paired with higher prices, shows the expected trade-off between performance and affordability.

### Fuel Type Distribution by Body Style: 
Sedans lead overwhelmingly with 96 cars, followed by hatchbacks (70), wagons (25), hardtops (8), and convertibles (6). This confirms sedans as the most common and accessible body style, dominating the dataset’s mid- and low-price tiers.

### Price per Horsepower by Make: 
Mercedes-Benz, BMW, Jaguar, Porsche, Peugeot, Audi, and Volvo record the highest ratios, reflecting a higher cost of performance. These makes charge more per unit of horsepower, reinforcing their luxury positioning but reducing their appeal for cost-conscious buyers.

### Car Count by Body Style and MPG: 
Sedans and hatchbacks not only dominate in sheer volume but also align with higher average fuel efficiency. In contrast, wagons, hardtops, and convertibles are fewer in number and tend to be associated with lower MPG, further distinguishing mass-market from niche offerings.

## Recommendation & Conclusion:

The findings show a clear divide between economy and premium segments. On one side, luxury brands like Jaguar, Mercedes-Benz, Porsche, BMW, and Audi dominate pricing and performance metrics but at a steep price-per-horsepower cost. On the other side, economy and mid-market brands such as Honda, Toyota, Chevrolet, and Volkswagen lead in fuel efficiency and affordability, with sedans and hatchbacks dominating the accessible segment.

For investors, the strongest opportunities lie in the mid-market brands like Honda, Toyota, Chevrolet, and Volkswagen. They deliver consistent demand drivers: affordability, strong MPG, and wide availability in sedan and hatchback body styles. These qualities ensure resilience in both high-volume consumer markets and fuel-conscious regions.

For buyers, the recommendations depend on priorities. Budget-conscious buyers should look at sedans and hatchbacks under $10,000, particularly from Honda, Toyota, and Chevrolet, which combine efficiency with low cost of ownership. For buyers seeking performance without luxury premiums, Peugeot and Volvo offer horsepower levels competitive with luxury makes but without the extreme price tags. For prestige-focused consumers, Jaguar, Mercedes-Benz, Porsche, and BMW provide exclusivity and high horsepower but demand a significantly higher cost per performance unit.

In conclusion, the dataset confirms that economy-oriented Japanese and American makes dominate the affordable, efficient segment, while European luxury brands secure the premium market. Investors should target the mid-range efficiency-driven space, while consumers should weigh cost against performance, with sedans and hatchbacks offering the best balance for the majority of buyers.


You can interact with the live visualisation [HERE](https://github.com/Makindetitilayo/Automobile-Sales-Analysis/blob/main/Automobile.pbix)

