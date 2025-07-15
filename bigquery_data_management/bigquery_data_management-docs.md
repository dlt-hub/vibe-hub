In this guide, we'll set up a complete BigQuery data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def bigquery_data_management_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://bigquery.googleapis.com/bigquery/v2/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "datasets",
            "tables",
            "jobs"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='bigquery_data_management_pipeline',
        destination='duckdb',
        dataset_name='bigquery_data_management_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(bigquery_data_management_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from bigquery_data_management’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Datasets: Manage and control datasets including listing, getting information, and creating datasets.
- Tables: Operations related to tables such as listing, getting details, and creating tables within datasets.
- Jobs: Handle query, load, export, copy, or other jobs within BigQuery.

You can combine these endpoints to build pipelines that extract structured content from BigQuery workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@bigquery_data_management-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with BigQuery support.
    ```shell
    dlt init dlthub:bigquery_data_management duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for BigQuery API, as specified in @bigquery_data_management-docs.yaml 
    Start with endpoints "datasets" and "tables" and skip incremental loading for now. 
    Place the code in bigquery_data_management_pipeline.py and name the pipeline bigquery_data_management_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python bigquery_data_management_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    BigQuery uses OAuth 2.0 for authentication. The flow involves a refresh token which is used to acquire access tokens from a token URL. Client credentials like client ID and secret are required.
    
    To get appropriate API keys, please visit the original source at https://cloud.google.com/bigquery.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python bigquery_data_management_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline bigquery_data_management load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset bigquery_data_management_data
    The duckdb destination used duckdb:/bigquery_data_management.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline bigquery_data_management_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("bigquery_data_management_pipeline").dataset()
   # get datasets table as Pandas frame
   data.datasets.df().head()
    ```

## Running into errors?

Requires a Google Cloud Project with BigQuery enabled. Service Account must have specific roles like 'BigQuery User' and 'BigQuery Data Editor'. Be aware of permissions issues such as Access Denied, and verify project and dataset IDs to avoid Not Found errors. Format and parameters of requests must be carefully managed to avoid Bad Requests.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)