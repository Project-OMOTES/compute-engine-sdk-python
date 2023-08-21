# Compute engine sdk python
Nieuwe Warmte Nu


## install/update pip package
see
https://packaging.python.org/en/latest/tutorials/packaging-projects/#:~:text=Generating%20distribution%20archives

## usage
Install in development mode, in root directory:
```
pip install -e .
```

or install from pypi:
```
pip install nwnsdk
```

For testing locally with RabbitMQ and Postgres on docker desktop clone https://github.com/Nieuwe-Warmte-Nu/computation-engine, copy `.env-template` to `.env` and in the root directory:
```
docker-compose up
```

Example usage

```python
from uuid import uuid4
from nwnsdk import NwnClient, WorkFlowType, PostgresConfig, RabbitmqConfig

postgres_config = PostgresConfig(
    "localhost",
    5432,
    "nieuwewarmtenu",
    "root",
    "1234",
)
rabbitmq_config = RabbitmqConfig(
    "localhost",
    5672,
    "nwn",
    "root",
    "5678",
)
nwn_client = NwnClient(postgres_config, rabbitmq_config)
job_id1: uuid4 = nwn_client.start_work_flow(
    WorkFlowType.GROWTH_OPTIMIZER, "test_job1", "esdl_string", "test_user1", "test_proj"
)
job_id2: uuid4 = nwn_client.start_work_flow(
    WorkFlowType.GROWTH_OPTIMIZER, "test_job2", "esdl_string", "test_user2", "test_proj"
)
print(job_id1)

job1_status = nwn_client.get_job_status(job_id1)
print(f"===== job1 status: {job1_status}")

job1 = nwn_client.get_job_details(job_id1)
print(f"===== {job1.job_name} input esdl: {job1.input_esdl}")

jobs_all = nwn_client.get_all_jobs()
print(f"===== {jobs_all[1].job_name} added at: {jobs_all[1].added_at}")


jobs_from_ids = nwn_client.get_jobs_from_ids([job_id1, job_id2])
print(f"===== {jobs_from_ids[1].job_name} added at: {jobs_from_ids[1].added_at}")


jobs_from_user = nwn_client.get_jobs_from_user("test_user1")
print(f"===== Jobs from test_user1 added at: {','.join([str(job.added_at) for job in jobs_from_user])}")


jobs_from_project = nwn_client.get_jobs_from_project("test_proj")
print(f"===== Jobs from test_proj added at: {','.join([str(job.added_at) for job in jobs_from_project])}")

```