Hello there,

Let me tell you a story about someone interesting : Me !

I am **Kareem Abdelsalam**

![my photo](images/me.jpeg)

A **Data Analytics Engineer** at

![Rubikal Logo](images/Rubikal.png)

Working remotely for about 2 years with our partners in the US:


![Concentrical Logo](images/concentrical.jpeg)

![Insidetrack Logo](images/insidetrack.png)

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

      - name: first_name
        description: string column showing name of the action taker
        tests:
        - trimmed_spaces


    tests:
      - dbt_utils.expression_is_true:
          expression: "price >= 0"
          condition: "price IS NOT NULL"

```

but it is not always this easy, is it ?

AS you can see above we needed a **custom schema test/ macro** to check for string columns coming from backend without leading or trailing spaces (so that we could easily group by them)

implementation:
```
{% macro test_trimmed_spaces(model, column_name) %}

WITH untrimmed AS (
    SELECT
        DISTINCT COALESCE({{ column_name }}, '') as untrimmed_name

    FROM {{ model }}

),
trimmed AS (

    SELECT
        DISTINCT TRIM( COALESCE({{ column_name }}, '') ) as trimmed_name

    FROM {{ model }}
)
SELECT
    COUNT(*)
FROM
    untrimmed
LEFT JOIN
    trimmed
ON
    untrimmed.untrimmed_name = trimmed.trimmed_name
WHERE
    trimmed_name IS NULL
{% endmacro %}
```
This is just a sample of fully covering the columns with documentation, explanation and tests that communicate and check for assumptions about the data.

Other custom data and schema tests are written to guarantee data integrity for our customers and to alert us if any error happens.

After passing code review and merging into our code base the table/view is materialized in
![Snowflake Logo](images/snowflake.png)
 and the tests run daily to check for assumptions about the data.

It is also my responsibility to monitor our **ETL manager/ Orchestrator** which is something like Airflow and fix any problems that may come and investigate any broken assumptions.


Speaking of Data Warehouses we previously used ![Redshift Logo](images/redshift.png)
so I am experienced with that as well :)


No I am not done yet !

The rest of my role is building charts and dashboards for our clients in
![Tableau Logo](images/tableau.png)
and
![Metabase Logo](images/metabase.png)
