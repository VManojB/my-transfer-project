## File Purpose:
* manifest.yml: Defines environment-specific variables such as project IDs, service accounts, and paths to configuration files.
* models/schema.yml: Defines the sources used in the dbt project, mapping source table names to their corresponding BigQuery identifiers.
* dbt_command_loop.sh: A shell script that runs dbt commands by iterating through the configuration files for each model.
* models/single/default.sql: A generic dbt model for simple SELECT * operations, commonly used for most tables.
* models/m0_report/mo_report.sql: Creates the reconciliation_mo_report table by performing data deduplication and transformations on several source tables from the coreruby dataset.
* models/m1_report/m1_report.sql: Similar to mo_report.sql, this model processes data from coreruby tables, handles tax information, and prepares data for the reconciliation_m1_report table.
* preprod_table_config.yml: Configures the preprod environment, specifying source and target BigQuery details for each model.
* table_config.yml: Similar to preprod_table_config.yml, this configures the prod and dev environments.

Currently, the setup relies on dbt_command_loop.sh to run multiple dbt run commands. The script takes an environment argument (prod, preprod, or dev) and iterates through the corresponding configuration file (either table_config.yml or preprod_table_config.yml). For each table in the configuration, it constructs a dbt run command with the appropriate environment variables and model names. The expected outcome is the creation of the target tables in BigQuery based on the configuration.

## Issues

* Maintaining separate configuration files for each environment adds complexity and makes the setup harder to maintain. Using dbt profiles and environment variables can handle environment-specific configurations more effectively, eliminating the need for redundant files.
* The current method runs a separate dbt run for each model, which increases overhead and slows down the process. dbt is designed to build all models in a project in a single run, which would improve efficiency.
