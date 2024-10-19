# Airflow quick setup guide

Airflow does not work with windows, so make sure you have a linux environment (eg. wsl)

Create a conda environment with:
`conda create -n apache python=3.11`
`conda activate apache`

Folow quick start for installation: https://airflow.apache.org/docs/apache-airflow/stable/start.html

Alternatively, just run:
`pip install -r requirements.txt`

Open ~/airflow/airflow.cfg and edit the `dags_folder` variable to the path of the current folder.

Run the following command to initialize the database, create a user, and start all components:
`airflow standalone`

(Alternatively) Run the individual components manually with:
- `airflow db migrate`
- `airflow users create --username admin --firstname Adam --lastname Tay --role Admin --email tayxhwork@gmail.com`
- `airflow webserver --port 8080`
- `airflow scheduler`

Webserver will be started on localhost:8080.

Username and Password will be given in the log. Look for `Login with username: admin  password: <hash>`. It can be changed in `$AIRFLOW_HOME/standalone_admin_password.txt`.

For a quick demo, enable the `example_bash_operator` DAG and run `airflow tasks test example_bash_operator runme_0 2015-01-01` in another terminal.

# Tutorial 1:
The `tutorial.py` is one example of a simple DAG pipeline. The python file is basically just a configuration of the DAG.

On another terminal run `python tutorial.py`.

To Validate the script ran properly:

- Initialize the database tables: 
    - `airflow db migrate`
- Print the list of acive DAGs and look for `tutorial` which is the name of our DAG: 
    - `airflow dags list`
- Print the list of tasks in the DAG: 
    - `airflow tasks list tutorial`
- Print the hierarchy of tasks in the DAG: 
    - `airflow tasks list tutorial --tree`

To Test individual tasks:

- The command layout is `command subcommand [dag_id] [task_id] [(optional date)]
- Testing print_date
    - `airflow tasks test tutorial print_date` 
- Testing sleep
    - `airflow tasks test tutorial sleep 2024-19-10`
- Testing the templated
    - `airflow tasks test tutorial templated` 
- Observe the difference by passing in different dates to the templated task.

To Test backfill:
```
airflow dags backfill tutorial \
    --start-date 2015-06-01 \
    --end-date 2015-06-07
````