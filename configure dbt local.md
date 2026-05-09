go to [conda.io/miniconda](https://anaconda.com/api/installers/Miniconda3-latest-Windows-x86_64.exe)

open Anaconda
cd /d D:\dbtproject

check
conda --version

conda create -n dbt_duck python=3.11 -y
conda activate dbt_duck

conda create -n dbt-env python=3.11 -y
conda activate dbt-env

pip install dbt-duckdb

pip install dbt-core dbt-duckdb

dbt --version

python -c "import duckdb; print(duckdb.__version__)"

Open VS Code from this same Anaconda Prompt (don't open VS Code from Start menu)

cd D:\dbtproject
code .

inside vscode terminal under d:/dbtproject

This is important — launching VS Code this way makes the integrated terminal inherit your activated conda env automatically.

mkdir %USERPROFILE%\.dbt
notepad %USERPROFILE%\.dbt\profiles.yml

in the opened notepad, paste below

my_dbt_project:
  target: dev
  outputs:
    dev:
      type: duckdb
      path: D:/dbtproject/mydb.duckdb
      threads: 4


cd D:\dbtproject\my_dbt_project
dbt debug


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

<img width="1337" height="682" alt="image" src="https://github.com/user-attachments/assets/72957d86-9906-4c75-aa4c-8c27242b5129" />

dbt show --select employees

models/staging/stg_employees.sql

with source as (
    select * from {{ ref('employees') }}
)
select
    id                              as employee_id,
    name                            as employee_name,
    department                      as department,
    cast(salary as decimal(10, 2))  as salary
from source


dbt run --select stg_employees




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

