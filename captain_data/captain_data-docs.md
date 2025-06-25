In this guide, we'll set up a complete Captain Data data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def captain_data_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.captaindata.co/v3/",
            "auth": {
                "type": "apikey",
                "api_key": access_token,
                "project_uid": project_uid
            },
        },
        "resources": [
            "workspaces",
            "workflows",
            "jobs"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='captain_data_pipeline',
        destination='duckdb',
        dataset_name='captain_data_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(captain_data_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from captain_data’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Workspaces: Manage and retrieve information about workspaces.
- Workflows: Handle operations related to workflows, such as retrieving them.
- Jobs: Operations related to jobs, including listing and managing them.
- Job Results: Fetch results of specific jobs.

You can combine these endpoints to build pipelines that extract structured content from Captain Data workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@captain_data-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Captain Data support.
    ```shell
    dlt init dlthub:captain_data duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Captain Data API, as specified in @captain_data-docs.yaml 
    Start with endpoints "workspaces" and "workflows" and skip incremental loading for now. 
    Place the code in captain_data_pipeline.py and name the pipeline captain_data_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python captain_data_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is done via an API key and a project UID. These must be included in the headers for each API call.
    
    To get appropriate API keys, please visit the original source at https://www.captaindata.co/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python captain_data_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline captain_data load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset captain_data_data
    The duckdb destination used duckdb:/captain_data.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline captain_data_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("captain_data_pipeline").dataset()
   # get workspaces table as Pandas frame
   data.workspaces.df().head()
    ```

## Running into errors?

The API supports only full refresh syncs; incremental syncs are not supported. Users must manage their request rate to avoid exceeding the request limit, and ensure that the API key and project UID are correct to avoid unauthorized errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)