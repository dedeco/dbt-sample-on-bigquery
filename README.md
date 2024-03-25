Quickstart

 - Use a Google Cloud Platform (GCP) project.
 - Access sample data in a public dataset.
 - Connect dbt Cloud to BigQuery.
 - Take a sample query and turn it into a model in your dbt project. A model in dbt is a select statement.
 - Add tests to your models.
 - Document your models.

### Steps:

1. Config connection profiles:

 - When you invoke dbt from the command line, dbt parses your **dbt_project.yml** and obtains the **profile name,** which dbt needs to connect to your data warehouse.
 - dbt then checks your **profiles.yml** file for a profile with the same name. A profile contains all the details required to connect to the data warehouse.
 - dbt will search the current working directory for the **profiles.yml** file and will default to the **~/.dbt/** directory if not found.

```yaml
# example profiles.yml file
sample_bv:
  outputs:
    dev:
      dataset: <DATASET>
      job_execution_timeout_seconds: 300
      job_retries: 1
      location: US
      method: oauth
      priority: interactive
      project: <PROJECT_ID>
      threads: 4
      type: bigquery
  target: dev
```

2. Edit models and transformations: 

```ssh
├── customers.sql
├── schema.yml
└── staging
    ├── stg_customers.sql
    └── stg_orders.sql
```

Sample dbt_project.yml file:
```yaml
name: 'jaffle_shop'
version: '1.0.0'
config-version: 2

profile: 'sample_bv'

model-paths: ["models"]
analysis-paths: ["analyses"]
test-paths: ["tests"]
seed-paths: ["seeds"]
macro-paths: ["macros"]
snapshot-paths: ["snapshots"]

clean-targets:
  - "target"
  - "dbt_packages"

models:
  jaffle_shop:
    staging:
      materialized: table
```

3. Enter the dbt run command. The models should now be built as a table!

```ssh
>> dbt run 
21:03:37  Running with dbt=1.7.10
21:03:38  Registered adapter: bigquery=1.7.6
21:03:38  Found 3 models, 9 tests, 0 sources, 0 exposures, 0 metrics, 454 macros, 0 groups, 0 semantic models
21:03:38  
21:03:40  Concurrency: 4 threads (target='dev')
21:03:40  
21:03:40  1 of 3 START sql table model sample_dbt_bv.stg_customers ....................... [RUN]
21:03:40  2 of 3 START sql table model sample_dbt_bv.stg_orders .......................... [RUN]
21:03:43  2 of 3 OK created sql table model sample_dbt_bv.stg_orders ..................... [CREATE TABLE (99.0 rows, 3.3 KiB processed) in 3.55s]
21:03:43  1 of 3 OK created sql table model sample_dbt_bv.stg_customers .................. [CREATE TABLE (100.0 rows, 1.9 KiB processed) in 3.73s]
21:03:43  3 of 3 START sql view model sample_dbt_bv.customers ............................ [RUN]
21:03:45  3 of 3 OK created sql view model sample_dbt_bv.customers ....................... [CREATE VIEW (0 processed) in 1.08s]
21:03:45  
21:03:45  Finished running 2 table models, 1 view model in 0 hours 0 minutes and 6.64 seconds (6.64s).
21:03:45  
21:03:45  Completed successfully
21:03:45  
21:03:45  Done. PASS=3 WARN=0 ERROR=0 SKIP=0 TOTAL=3
```

4. Run tests

```ssh
dbt test
21:05:03  Running with dbt=1.7.10
21:05:04  Registered adapter: bigquery=1.7.6
21:05:04  Found 3 models, 9 tests, 0 sources, 0 exposures, 0 metrics, 454 macros, 0 groups, 0 semantic models
21:05:04  
21:05:05  Concurrency: 4 threads (target='dev')
21:05:05  
21:05:05  1 of 9 START test accepted_values_stg_orders_status__placed__shipped__completed__return_pending__returned  [RUN]
21:05:05  2 of 9 START test not_null_customers_customer_id ............................... [RUN]
21:05:05  3 of 9 START test not_null_stg_customers_customer_id ........................... [RUN]
21:05:05  4 of 9 START test not_null_stg_orders_customer_id .............................. [RUN]
21:05:07  3 of 9 PASS not_null_stg_customers_customer_id ................................. [PASS in 1.87s]
21:05:07  5 of 9 START test not_null_stg_orders_order_id ................................. [RUN]
21:05:07  1 of 9 PASS accepted_values_stg_orders_status__placed__shipped__completed__return_pending__returned  [PASS in 1.96s]
21:05:07  6 of 9 START test relationships_stg_orders_customer_id__customer_id__ref_stg_customers_  [RUN]
21:05:08  4 of 9 PASS not_null_stg_orders_customer_id .................................... [PASS in 2.08s]
21:05:08  7 of 9 START test unique_customers_customer_id ................................. [RUN]
21:05:08  2 of 9 PASS not_null_customers_customer_id ..................................... [PASS in 2.16s]
21:05:08  8 of 9 START test unique_stg_customers_customer_id ............................. [RUN]
21:05:09  5 of 9 PASS not_null_stg_orders_order_id ....................................... [PASS in 1.61s]
21:05:09  9 of 9 START test unique_stg_orders_order_id ................................... [RUN]
21:05:09  6 of 9 PASS relationships_stg_orders_customer_id__customer_id__ref_stg_customers_  [PASS in 1.77s]
21:05:09  7 of 9 PASS unique_customers_customer_id ....................................... [PASS in 1.90s]
21:05:09  8 of 9 PASS unique_stg_customers_customer_id ................................... [PASS in 1.87s]
21:05:10  9 of 9 PASS unique_stg_orders_order_id ......................................... [PASS in 1.54s]
21:05:10  
21:05:10  Finished running 9 tests in 0 hours 0 minutes and 6.58 seconds (6.58s).
21:05:11  
21:05:11  Completed successfully
21:05:11  
21:05:11  Done. PASS=9 WARN=0 ERROR=0 SKIP=0 TOTAL=9
```

5. Run dbt docs generate to generate the documentation for your project. dbt introspects your project and your warehouse to generate a JSON file with rich documentation about your project


### Source:
    - Quickstart fort dbt Bigquery: https://docs.getdbt.com/guides/bigquery
