
# Solution to Eliminate Shell Script and Environment-Specific Config Files in dbt

## Objective

Eliminate shell scripts and separate environment config files by using `vars`, `profiles.yml`, and a single `config.yml` to streamline dbt project setup.

## Steps to Achieve the Solution

### 1. Use `vars` in `dbt_project.yml`

Define table-specific configs with `vars` to avoid separate config files.

```yaml
models:
  my_project:
    mo_report:
      target_table: "{{ var('mo_report_target_table', 'default_mo_report') }}"
```

### 2. Consolidate Configuration in `profiles.yml`

Define multiple environments in a single `profiles.yml` using environment variables.

```yaml
my_project:
  target: dev
  outputs:
    dev:
      project: "{{ env_var('DBT_PROJECT') }}"
      dataset: "{{ env_var('DBT_DATASET') }}"
```

### 3. Centralize Table Configurations in `config.yml`

Consolidate table configs into one YAML file.

```yaml
mo_report:
  source_project: 'my_project'
  target_table: 'reconciliation_mo_report'
```

**Loading `config.yml` in dbt model:**

```sql
{% set config = fromyaml(load_file('config.yml'))['mo_report'] %}
SELECT * FROM {{ source(config.source_project, 'dataset', 'table') }}
```

### 4. Dynamically Generate Models with Python Script

Generate models from the `vars` section of `dbt_project.yml`.

```python
for model, config in dbt_config['models']['my_project'].items():
    model_content = f"SELECT * FROM {{ source('{config['source_project']}', 'dataset', 'table') }}"
    with open(f"models/{model}.sql", 'w') as f: f.write(model_content)
```

### 5. Use Environment Variables

Set environment variables to manage environment-specific settings.

```bash
export DBT_PROJECT=my_project
export DBT_DATASET=my_dataset
dbt run
```

### 6. Remove Shell Script and Use dbt CLI

Run dbt directly with environment variables.

```bash
export DBT_ENV=dev
dbt run
```

## Conclusion

This approach centralizes configuration and simplifies environment management by using `profiles.yml`, `vars`, and a single `config.yml`. It eliminates redundant files and scripts, streamlining the dbt workflow.
