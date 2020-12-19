Hello there,

Lets talk about something interesting : Me !

I am **Kareem Abdelsalam**

![my photo](me.jpeg =180x260)

A **Data Analytics Engineer** at

![Rubikal Logo](Rubikal.png)

Working remotely for about 2 years with our partners in the US:


![Concentrical Logo](concentrical.jpeg)

![Insidetrack Logo](insidetrack.png)

After I grab my morning coffee I checkout a branch and start writing things like this (yes it is the cool **dbt jinja-SQL** in the flesh):
```
{{
  config(
    alias='cool_data_mart_name',
    materialized: table
  )
}}
WITH fact_table AS (
  SELECT * FROM {{ref('fact_table_name')}} WHERE [condition]
  ),

dimention_table_1 AS (
  SELECT
  {{ dbt_utils.star(
      from=ref('dimention_table_1_name'),
      except=[UNWANTED_COLUMN_NAMES],
      , relation_alias='d1'
      )}},
   FROM {{ref('dimention_table_1_name')}} AS d1 WHERE [condition]
  ),

dimention_table_2 AS (
  SELECT * FROM {{ref('dimention_table_2_name')}} WHERE [condition]
  ),
final AS (
  SELECT
    f.col1,
    d1.*,
    COALESCE(d2.col123, 'default_value') AS col123,
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


then onto **cool_data_mart_name.yml**

```
version: 2

models:
  - name: cool_data_mart_name
    columns:
      - name: id
      description: unique identifier of record
        tests:
          - unique
          - not_null
          - dbt_utils.relationships_where:
              to: source('mixpanel', 'events')
              field: event_id

      - name: is_condition
        description: boolean column shows some condition is true or false
        tests:
        - accepted_values:
            values: [TRUE, FALSE]
            quote: false

    tests:
      - dbt_utils.expression_is_true:
          expression: "price >= 0"
          condition: "price IS NOT NULL"

```

but it is not always this easy, is it ?

This is just a sample of fully covering the columns with documentation, explanation and tests that communicate and check for assumptions about the data.

Other custom data and schema tests are written to guarantee data integrity for our customers and to alert us if any error happens.
