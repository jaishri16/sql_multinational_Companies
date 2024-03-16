# sql_multinational_Companies
task is to first identify the three best-performing industries based on the number of new Companies created in 2019, 2020, and 2021 combined.
```
WITH top_industries AS
(
    SELECT i.industry, 
        COUNT(i.*)
    FROM industries AS i
    INNER JOIN dates AS d
        ON i.company_id = d.company_id
    WHERE EXTRACT(year FROM d.date_joined) in ('2019', '2020', '2021')
    GROUP BY industry
    ORDER BY count DESC
    LIMIT 3
),

yearly_rankings AS 
(
    SELECT COUNT(i.*) AS num_unicorns,
        i.industry,
        EXTRACT(year FROM d.date_joined) AS year,
        AVG(f.valuation) AS average_valuation
    FROM industries AS i
    INNER JOIN dates AS d
        ON i.company_id = d.company_id
    INNER JOIN funding AS f
        ON d.company_id = f.company_id
    GROUP BY industry, year
)

SELECT industry,
    year,
    num_unicorns,
    ROUND(AVG(average_valuation / 1000000000), 2) AS average_valuation_billions
FROM yearly_rankings
WHERE year in ('2019', '2020', '2021')
    AND industry in (SELECT industry
                    FROM top_industries)
GROUP BY industry, num_unicorns, year
ORDER BY year DESC, num_unicorns DESC

```
result:
|    | Industry                            | Year | Num_Unicorns | Average_Valuation_Billions |
|----|-------------------------------------|------|--------------|----------------------------|
| 0  | Fintech                             | 2021 | 138          | 2.75                       |
| 1  | Internet Software & Services        | 2021 | 119          | 2.15                       |
| 2  | E-commerce & Direct-to-Consumer     | 2021 | 47           | 2.47                       |
| 3  | Internet Software & Services        | 2020 | 20           | 4.35                       |
| 4  | E-commerce & Direct-to-Consumer     | 2020 | 16           | 4                          |
| 5  | Fintech                             | 2020 | 15           | 4.33                       |
| 6  | Fintech                             | 2019 | 20           | 6.8                        |
| 7  | Internet Software & Services        | 2019 | 13           | 4.23                       |
| 8  | E-commerce & Direct-to-Consumer     | 2019 | 12           | 2.58                       |
