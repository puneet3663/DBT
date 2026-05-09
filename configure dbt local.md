go to [conda.io/miniconda](https://anaconda.com/api/installers/Miniconda3-latest-Windows-x86_64.exe)

check
conda --version

conda create -n dbt-env python=3.11 -y
conda activate dbt-env

pip install dbt-core dbt-duckdb

dbt --version

cd C:\Users\YourName\Documents
dbt init my_project
# Select "duckdb" when asked which database to use
cd my_project


Configure profiles.yml — located at C:\Users\YourName\.dbt\profiles.yml

my_project:
  target: dev
  outputs:
    dev:
      type: duckdb
      path: C:/Users/YourName/Documents/my_project/dev.duckdb
      # DuckDB creates this file automatically — no server needed!


Create seeds/employees.csv:
id,name,department,salary
1,Alice,Engineering,90000
2,Bob,Marketing,70000
3,Carol,Engineering,95000
4,Dave,HR,65000

dbt seed


Create a model — models/dept_summary.sql

{{ config(materialized='table') }}

SELECT
    department,
    COUNT(*)        AS headcount,
    AVG(salary)     AS avg_salary
FROM {{ ref('employees') }}
GROUP BY department

Create a macro — macros/cents_to_dollars.sql

{% macro cents_to_dollars(col) %}
    ({{ col }} / 100.0)
{% endmacro %}


dbt run          # builds all models
dbt test         # runs tests
dbt build        # seed + run + test in one command
dbt docs generate && dbt docs serve  # opens docs in browser

