Hello there,

Lets talk about something interesting : Me !

I am **Kareem Abdelsalam**

![my photo](me.jpeg)

A **Data Analytics Engineer** at

![Rubikal Logo](Rubikal.png)

Working remotely for about 2 years with our partners in the US:
![Insidetrack Logo](insidetrack.png)

![Concentrical Logo](concentrical.jpeg)

After I grab my morning coffee I start writing things like this:
```
{{
  config(
    alias='cool_data_mart_name',
    materialized: table
  )
}}
WITH fact_table AS (
  SELECT * from {{ref('fact_table_name')}} WHERE [condition]
  ),

dimention_table_1 AS (
  SELECT * from {{ref('dimention_table_1_name')}} WHERE [condition]
  ),

dimention_table_2 AS (
  SELECT * from {{ref('dimention_table_2_name')}} WHERE [condition]
  ),
final AS (
  SELECT
    f.col1,
    d1.col2,
    COALESCE(d2.col3, 'default_value') AS col3,
    ROW_NUMBER() OVER( PARTITION BY id ORDER BY event_at DESC) AS deduplicate
  FROM
    fact_table AS f
  INNER JOIN
    dimention_table_1 AS d1
  ON
    f.foreign_1 = d1.primary
  LEFT JOIN
    dimention_table_2 AS d2
  ON
    f.foreign_2 = d2.primary
  WHERE
    [insert mart conditions]
  QUALIFY
   deduplicate = 1
)
SELECT * FROM final
  ```

but it is not always this easy, is it ?
