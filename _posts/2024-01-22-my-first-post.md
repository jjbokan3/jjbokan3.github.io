---
title: "My First Post"
date: 2024-01-22
categories: data_engineering
tags: [Jekyll, website, data]
---

# Airflow

> Apache Airflow is an open-source platform used for orchestrating complex computational workflows and data processing pipelines. It allows scheduling, monitoring, and automation of tasks, ensuring they run in the correct order and managing dependencies between tasks. Airflow is widely used for managing ETL processes, machine learning pipelines, and other data-driven workflows.
{: .prompt-tip }

## TaskFlow
### Dag Creation
Creating dags in the TaskFlow syntax utilizes a decorator above the function
```python
@dag(dag_id='my_new_dag', schedule='@daily', catchup=False) # Used to define Dag parameters
def my_new_dag(): #Call tasks in the function body
	func1()
	func2()
	func3()

output = my_new_dag() # Run the dag
```

### Parameters

| `@dag`              | Description                                                                     |
|---------------------|---------------------------------------------------------------------------------|
| `dag_id`            | A unique identifier for the DAG.                                                |
| `schedule_interval` | Defines how often the DAG runs. Accepts cron expressions or cron presets.       |
| `start_date`        | The date when the DAG should start running.                                     |
| `end_date`          | Optional. The date when the DAG should stop running.                            |
| `catchup`           | Whether or not to perform catch-up runs for past intervals.                     |
| `default_args`      | A dictionary of default parameters to be used for all tasks in the DAG.         |
| `description`       | A string describing the DAG.                                                    |
| `tags`              | A list of tags to help categorize and filter DAGs in the UI.                    |
| `max_active_runs`   | The maximum number of active DAG runs, beyond which new runs are not triggered. |
| `concurrency`       | The number of task instances allowed to run concurrently.                       |


### Task Creation
Instead of the usage of the PythonOperator, you utilize the @task decorator
```python
@task(task_id="Write1")
def write_to_file(text):
    with open(Path.home() / "test_airflow.txt", "w") as f:
        f.write(text)
```

| @task              | Description                                                                   |
|--------------------|-------------------------------------------------------------------------------|
| `task_id`          | A unique identifier for the task.                                             |
| `multiple_outputs` | If set to True, allows a task to return a dictionary with multiple outputs.   |
| `retry_delay`      | The time to wait before retrying a failed task instance.                      |
| `retries`          | The number of retries that should be performed before marking it as failed.   |
| `depends_on_past`  | If set to True, the task instance depends on the success of the previous run. |
| `email_on_failure` | If set to True, sends an email when the task fails.                           |
| `email_on_retry`   | If set to True, sends an email on task retry.                                 |
| `trigger_rule`     | Defines the rule to follow for triggering the task.                           |
#### Bash commands
Instead of relying on BashOperator, utilizing python's subprocess library provides more control.
```python
@task(task_id="bash_command")
def bash_command():
    output = subprocess.check_output(
        f"ls -l {Path.home()} | grep airflow", shell=True
    ).decode("utf-8")
    with open(Path.home() / "bash_output.txt", "w") as f:
        f.write(output)
```

## Pendulum
Library used for better datetime creation and processing
### DateTime
```python
import pendulum

now = pendulum.datetime(2024, 1, 1, 15, 30) # January 1, 2024 3:30
# or
now = pendulum.now("Europe/Paris")

# Changing timezone
now.in_timezone("America/Toronto")

# Default support for common datetime formats
now.to_iso8601_string()

# Shifting
now.add(days=2)
```
### Duration
```python
dur = pendulum.duration(days=15)

# More properties
dur.weeks
dur.hours

# Handy methods
dur.in_hours()
360
dur.in_words(locale="en_us")
'2 weeks 1 day'
```
### Period
```python
dt = pendulum.now()

# A period is the difference between 2 instances
period = dt - dt.subtract(days=3)

period.in_days()

# A period is iterable
for dt in period:
    print(dt)
  
```
### Timezones
```python
import pendulum

in_utc = pendulum.datetime(2013, 3, 31, 0, 59, 59)
tz = pendulum.timezone("Europe/Paris")
in_paris = tz.convert(in_utc)
'2013-03-31T01:59:59+01:00'

# Shifting time
in_paris = in_paris.add(seconds=1)
'2013-03-31T03:00:00+02:00'
in_paris.subtract(seconds=1)
'2013-03-31T01:59:59+01:00'
```
