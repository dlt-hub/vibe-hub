In this guide, we'll set up a complete ServiceNow data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def service_now_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://your-instance.service-now.com/api/now/table/",
            "auth": {
                "type": "basic",
                "username": dlt.secrets['api_username'],
                "password": dlt.secrets['api_password'],
            },
        },
        "resources": [
            "cmdb_ci_wap_network",
            "cmdb_ci_ip_router",
            "cmdb_ci_ip_switch"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='service_now_pipeline',
        destination='duckdb',
        dataset_name='service_now_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(service_now_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from service_now’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Configuration Management Database (CMDB): Access and manage information about hardware, networks, and other IT infrastructure components.
- Tables: General endpoint to interact with various tables, which can store different types of IT and business data.

You can combine these endpoints to build pipelines that extract structured content from ServiceNow workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@service_now-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with ServiceNow support.
    ```shell
    dlt init dlthub:service_now duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for ServiceNow API, as specified in @service_now-docs.yaml 
    Start with endpoints "cmdb_ci_wap_network" and "cmdb_ci_ip_router" and skip incremental loading for now. 
    Place the code in service_now_pipeline.py and name the pipeline service_now_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python service_now_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Uses basic authentication with a username and a password.
    
    To get appropriate API keys, please visit the original source at https://www.servicenow.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python service_now_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline service_now load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset service_now_data
    The duckdb destination used duckdb:/service_now.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline service_now_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("service_now_pipeline").dataset()
   # get cmdb_ci_wap_network table as Pandas frame
   data.cmdb_ci_wap_network.df().head()
    ```

## Running into errors?

This API only supports Full Refresh syncs, which means it does not support incremental updates and must re-import all data each time. Users must have full access to all tables they wish to interact with. Common error responses include 401 Unauthorized, indicating incorrect username or password; 404 Not Found, which suggests incorrect table names; and 429 Too Many Requests, which advises reducing the frequency of requests.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)