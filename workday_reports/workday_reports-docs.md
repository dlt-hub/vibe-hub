In this guide, we'll set up a complete Workday data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def workday_reports_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-workday-instance.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "jobs",
            "job_families",
            "job_profiles"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='workday_reports_pipeline',
        destination='duckdb',
        dataset_name='workday_reports_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(workday_reports_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from workday_reports’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Jobs: Access job reports and their details.
- Job Families: Retrieve information on different job family groupings.
- Job Profiles: Get details about various job profiles within the organization.
- People: Access personal information of individuals associated with the organization.
- Workers: Retrieve worker reports, including direct reports and historical data.
- Worker Payslips: Access payslip information for workers, supports incremental syncs based on updates.
- Worker Time Off Entries: Get time off entries for workers, with optional incremental updates.

You can combine these endpoints to build pipelines that extract structured content from Workday workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@workday_reports-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Workday support.
    ```shell
    dlt init dlthub:workday_reports duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Workday API, as specified in @workday_reports-docs.yaml 
    Start with endpoints "jobs" and "job_families" and skip incremental loading for now. 
    Place the code in workday_reports_pipeline.py and name the pipeline workday_reports_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python workday_reports_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is managed via OAuth2 with refresh tokens. Important parameters include client ID, client secret, and refresh token. Tokens are passed in the header using the 'Authorization' key.
    
    To get appropriate API keys, please visit the original source at https://www.workday.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python workday_reports_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline workday_reports load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset workday_reports_data
    The duckdb destination used duckdb:/workday_reports.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline workday_reports_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("workday_reports_pipeline").dataset()
   # get jobs table as Pandas frame
   data.jobs.df().head()
    ```

## Running into errors?

Requires configuration of a Workday tenant and hostname. Incremental syncs are only available for the Worker Payslips and Worker Time Off Entries endpoints. Ensure correct OAuth token and permissions, especially as 401 and 403 HTTP errors can occur if not properly configured.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)